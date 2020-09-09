---
title: 'Vistas de asignación previamente generadas: EF6'
description: Vistas de asignación previamente generadas en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 917ba9c8-6ddf-4631-ab8c-c4fb378c2fcd
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: 8318030603339c45c5c8459db580b7a8de5febda
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89615857"
---
# <a name="pre-generated-mapping-views"></a>Vistas de asignación generadas previamente
Antes de que el Entity Framework pueda ejecutar una consulta o guardar cambios en el origen de datos, debe generar un conjunto de vistas de asignación para tener acceso a la base de datos. Estas vistas de asignación son un conjunto de Entity SQL instrucción que representa la base de datos de forma abstracta y forman parte de los metadatos que se almacenan en caché por dominio de aplicación. Si crea varias instancias del mismo contexto en el mismo dominio de aplicación, volverán a usar las vistas de asignación de los metadatos almacenados en caché en lugar de volver a generarlas. Dado que la generación de la vista de asignación es una parte significativa del costo total de ejecutar la primera consulta, el Entity Framework permite generar previamente vistas de asignación e incluirlas en el proyecto compilado.Para obtener más información, vea  [consideraciones de rendimiento (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a>Generación de vistas de asignación con EF Power Tools Community Edition

La manera más sencilla de generar vistas previamente es usar la [edición de EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition). Una vez que tenga instaladas las herramientas avanzadas, tendrá una opción de menú para generar vistas, como se indica a continuación.

-   En el caso de los modelos de **code First** , haga clic con el botón derecho en el archivo de código que contiene la clase DbContext.
-   En el caso de los modelos **EF Designer** , haga clic con el botón derecho en el archivo EDMX.

![generar vistas](~/ef6/media/generateviews.png)

Una vez finalizado el proceso, tendrá una clase similar a la siguiente generada

![Vistas generadas](~/ef6/media/generatedviews.png)

Ahora, cuando ejecute la aplicación EF, utilizará esta clase para cargar las vistas según sea necesario. Si el modelo cambia y no vuelve a generar esta clase, EF producirá una excepción.

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>Generar vistas de asignación desde código-EF6 en adelante

La otra forma de generar vistas es usar las API que proporciona EF. Al usar este método, tiene la libertad de serializar las vistas de la manera que desee, pero también debe cargar las vistas por su cuenta.

> [!NOTE]
> **EF6** y versiones posteriores: las API que se muestran en esta sección se introdujeron en Entity Framework 6. Si usa una versión anterior, esta información no se aplica.

### <a name="generating-views"></a>Generar vistas

Las API para generar vistas se encuentran en la clase System. Data. Entity. Core. Mapping. StorageMappingItemCollection. Puede recuperar un StorageMappingCollection para un contexto mediante el MetadataWorkspace de un ObjectContext. Si usa la API DbContext más reciente, puede acceder a ella con IObjectContextAdapter como se muestra a continuación, en este código tenemos una instancia de DbContext derivado denominada dbContext:

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

Una vez que tenga el StorageMappingItemCollection, puede obtener acceso a los métodos GenerateViews y ComputeMappingHashValue.

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

El primer método crea un diccionario con una entrada para cada vista de la asignación de contenedor. El segundo método calcula un valor hash para la asignación de un solo contenedor y se utiliza en tiempo de ejecución para validar que el modelo no ha cambiado desde que se generaron previamente las vistas. Se proporcionan invalidaciones de los dos métodos para escenarios complejos que implican varias asignaciones de contenedor.

Al generar vistas, llamará al método GenerateViews y, a continuación, escribirá el EntitySetBase resultante y DbMappingView. También necesitará almacenar el hash generado por el método ComputeMappingHashValue.

### <a name="loading-views"></a>Cargando vistas

Para cargar las vistas generadas por el método GenerateViews, puede proporcionar EF con una clase que herede de la clase abstracta DbMappingViewCache. DbMappingViewCache especifica dos métodos que debe implementar:

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

La propiedad MappingHashValue debe devolver el hash generado por el método ComputeMappingHashValue. Cuando EF vaya a pedir las vistas, primero generará y comparará el valor hash del modelo con el hash devuelto por esta propiedad. Si no coinciden, EF producirá una excepción EntityCommandCompilationException.

El método GetView (aceptará un EntitySetBase y tendrá que devolver un DbMappingVIew que contenga el EntitySql generado para que se asoció con el EntitySetBase determinado en el Diccionario generado por el método GenerateViews. Si EF solicita una vista que no tiene, GetView (debe devolver null.

A continuación se muestra un extracto del DbMappingViewCache que se genera con las herramientas avanzadas, como se ha descrito anteriormente, en él vemos una forma de almacenar y recuperar el EntitySql necesario.

``` csharp
    public override string MappingHashValue
    {
        get { return "a0b843f03dd29abee99789e190a6fb70ce8e93dc97945d437d9a58fb8e2afd2e"; }
    }

    public override DbMappingView GetView(EntitySetBase extent)
    {
        if (extent == null)
        {
            throw new ArgumentNullException("extent");
        }

        var extentName = extent.EntityContainer.Name + "." + extent.Name;

        if (extentName == "BlogContext.Blogs")
        {
            return GetView2();
        }

        if (extentName == "BlogContext.Posts")
        {
            return GetView3();
        }

        return null;
    }

    private static DbMappingView GetView2()
    {
        return new DbMappingView(@"
            SELECT VALUE -- Constructing Blogs
            [BlogApp.Models.Blog](T1.Blog_BlogId, T1.Blog_Test, T1.Blog_title, T1.Blog_Active, T1.Blog_SomeDecimal)
            FROM (
            SELECT
                T.BlogId AS Blog_BlogId,
                T.Test AS Blog_Test,
                T.title AS Blog_title,
                T.Active AS Blog_Active,
                T.SomeDecimal AS Blog_SomeDecimal,
                True AS _from0
            FROM CodeFirstDatabase.Blog AS T
            ) AS T1");
    }
```

Para que EF use el DbMappingViewCache que agregue, use el DbMappingViewCacheTypeAttribute, especificando el contexto para el que se creó. En el código siguiente se asocia el BlogContext con la clase MyMappingViewCache.

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

En escenarios más complejos, se pueden proporcionar las instancias de la vista de la caché de vistas mediante la especificación de un generador de caché de vista de asignación. Esto se puede hacer implementando la clase abstracta System. Data. Entity. Infrastructure. MappingViews. DbMappingViewCacheFactory. La instancia del generador de caché de la vista de asignación que se usa se puede recuperar o establecer mediante StorageMappingItemCollection. MappingViewCacheFactoryproperty.
