---
title: Code First espaciales-EF6
description: Code First espacial en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 6942829809e37b5d62f61488ff6a190c8a38febb
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065243"
---
# <a name="spatial---code-first"></a>Code First espacial
> [!NOTE]
> **EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

En el tutorial de vídeo y paso a paso se muestra cómo asignar tipos espaciales con Entity Framework Code First. También se muestra cómo usar una consulta LINQ para buscar una distancia entre dos ubicaciones.

En este tutorial se utilizará Code First para crear una nueva base de datos, pero también puede usar [code First a una base de datos existente](xref:ef6/modeling/code-first/workflows/existing-database).

La compatibilidad con tipos espaciales se presentó en Entity Framework 5. Tenga en cuenta que para usar las nuevas características, como el tipo espacial, las enumeraciones y las funciones con valores de tabla, debe tener como destino .NET Framework 4,5. Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.

Para usar los tipos de datos espaciales, también debe usar un proveedor de Entity Framework que tenga compatibilidad espacial. Consulte [compatibilidad con proveedores para tipos espaciales](xref:ef6/fundamentals/providers/spatial-support) para obtener más información.

Hay dos tipos de datos espaciales principales: Geography y Geometry. El tipo de datos Geography almacena los datos de datos elipsoidales (por ejemplo, las coordenadas de latitud y longitud de GPS). El tipo de datos Geometry representa el sistema de coordenadas euclidiana (plano).

## <a name="watch-the-video"></a>Visualización del vídeo
Este vídeo muestra cómo asignar tipos espaciales con Entity Framework Code First. También se muestra cómo usar una consulta LINQ para buscar una distancia entre dos ubicaciones.

**Presentada por**: Julia Kornich

**Vídeo**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>Requisitos previos

Deberá tener instalado Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition para completar este tutorial.

## <a name="set-up-the-project"></a>Configurar el proyecto

1.  Abra Visual Studio 2012
2.  En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .
3.  En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
4.  Escriba **SpatialCodeFirst** como nombre del proyecto y haga clic en **Aceptar** .

## <a name="define-a-new-model-using-code-first"></a>Definir un nuevo modelo mediante Code First

Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio). El código siguiente define la clase University.

La Universidad tiene la propiedad Location del tipo DbGeography. Para usar el tipo DbGeography, debe agregar una referencia al ensamblado System. Data. Entity y también agregar la instrucción using System. Data. Spatial.

Abra el archivo Program.cs y pegue las siguientes instrucciones Using en la parte superior del archivo:

``` csharp
using System.Data.Spatial;
```

Agregue la siguiente definición de clase University al archivo Program.cs.

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a>Definir el tipo derivado de DbContext

Además de definir entidades, debe definir una clase que derive de DbContext y exponga las propiedades DbSet&lt;TEntity&gt;. Las propiedades DbSet&lt;TEntity&gt; permiten que el contexto sepa qué tipos desea incluir en el modelo.

Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.

Los tipos DbContext y DbSet se definen en el ensamblado EntityFramework. Se agregará una referencia a este archivo DLL mediante el paquete NuGet EntityFramework.

1.  En Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto.
2.  Seleccione **administrar paquetes NuGet..** .
3.  En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework** .
4.  Haz clic en **Instalar**

Tenga en cuenta que, además del ensamblado EntityFramework, también se agrega una referencia al ensamblado System. ComponentModel. DataAnnotations.

En la parte superior del archivo Program.cs, agregue la siguiente instrucción using:

``` csharp
using System.Data.Entity;
```

En Program.cs, agregue la definición de contexto. 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>Conservar y recuperar datos

Abra el archivo Program.cs en el que se define el método Main. Agregue el código siguiente a la función main.

El código agrega dos nuevos objetos universitarios al contexto. Las propiedades espaciales se inicializan mediante el método DbGeography. FromText. El punto de geografía representado como WellKnownText se pasa al método. Después, el código guarda los datos. A continuación, se crea y se ejecuta la consulta LINQ que devuelve un objeto University donde su ubicación es más cercana a la ubicación especificada.

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
        {
            Name = "School of Fine Art",
            Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
        });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                        orderby u.Location.Distance(myLocation)
                        select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

Compile y ejecute la aplicación. El programa produce el siguiente resultado:

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a>Ver la base de datos generada

Al ejecutar la aplicación por primera vez, el Entity Framework crea una base de datos automáticamente. Dado que tenemos instalado Visual Studio 2012, la base de datos se creará en la instancia de LocalDB. De forma predeterminada, el Entity Framework nombra la base de datos después del nombre completo del contexto derivado (en este ejemplo, que es **SpatialCodeFirst. UniversityContext**). Las veces posteriores en las que se utilizará la base de datos existente.  

Tenga en cuenta que si realiza cambios en el modelo una vez creada la base de datos, debe utilizar Migraciones de Code First para actualizar el esquema de la base de datos. Vea [code First en una nueva base de datos](xref:ef6/modeling/code-first/workflows/new-database) para obtener un ejemplo del uso de las migraciones.

Para ver la base de datos y los datos, haga lo siguiente:

1.  En el menú principal de Visual Studio 2012, seleccione **Ver**  - &gt; **Explorador de objetos de SQL Server**.
2.  Si LocalDB no está en la lista de servidores, haga clic con el botón secundario del mouse en **SQL Server** y seleccione **Agregar SQL Server** usar la **autenticación de Windows** predeterminada para conectarse a la instancia de LocalDB.
3.  Expandir el nodo LocalDB
4.  Desdoblar la carpeta **bases** de datos para ver la nueva base de datos y examinar la tabla **universidades**
5.  Para ver los datos, haga clic con el botón derecho en la tabla y seleccione **ver datos** .

## <a name="summary"></a>Resumen

En este tutorial, hemos visto cómo usar los tipos espaciales con Entity Framework Code First. 
