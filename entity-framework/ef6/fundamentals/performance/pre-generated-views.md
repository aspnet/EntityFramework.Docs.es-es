---
title: 'Vistas de asignación previamente generadas: EF6'
description: Vistas de asignación previamente generadas en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: 0c698e50f5c747d9953d9cf55dbbd8824ff8126d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062963"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="cba22-103">Vistas de asignación generadas previamente</span><span class="sxs-lookup"><span data-stu-id="cba22-103">Pre-generated mapping views</span></span>
<span data-ttu-id="cba22-104">Antes de que el Entity Framework pueda ejecutar una consulta o guardar cambios en el origen de datos, debe generar un conjunto de vistas de asignación para tener acceso a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="cba22-104">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="cba22-105">Estas vistas de asignación son un conjunto de Entity SQL instrucción que representa la base de datos de forma abstracta y forman parte de los metadatos que se almacenan en caché por dominio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="cba22-105">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="cba22-106">Si crea varias instancias del mismo contexto en el mismo dominio de aplicación, volverán a usar las vistas de asignación de los metadatos almacenados en caché en lugar de volver a generarlas.</span><span class="sxs-lookup"><span data-stu-id="cba22-106">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="cba22-107">Dado que la generación de la vista de asignación es una parte significativa del costo total de ejecutar la primera consulta, el Entity Framework permite generar previamente vistas de asignación e incluirlas en el proyecto compilado.</span><span class="sxs-lookup"><span data-stu-id="cba22-107">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span><span data-ttu-id="cba22-108">Para obtener más información, vea  [consideraciones de rendimiento (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span><span class="sxs-lookup"><span data-stu-id="cba22-108"> For more information, see  [Performance Considerations (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a><span data-ttu-id="cba22-109">Generación de vistas de asignación con EF Power Tools Community Edition</span><span class="sxs-lookup"><span data-stu-id="cba22-109">Generating Mapping Views with the EF Power Tools Community Edition</span></span>

<span data-ttu-id="cba22-110">La manera más sencilla de generar vistas previamente es usar la [edición de EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span><span class="sxs-lookup"><span data-stu-id="cba22-110">The easiest way to pre-generate views is to use the [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span></span> <span data-ttu-id="cba22-111">Una vez que tenga instaladas las herramientas avanzadas, tendrá una opción de menú para generar vistas, como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="cba22-111">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="cba22-112">En el caso de los modelos de **code First** , haga clic con el botón derecho en el archivo de código que contiene la clase DbContext.</span><span class="sxs-lookup"><span data-stu-id="cba22-112">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="cba22-113">En el caso de los modelos **EF Designer** , haga clic con el botón derecho en el archivo EDMX.</span><span class="sxs-lookup"><span data-stu-id="cba22-113">For **EF Designer** models right-click on your EDMX file.</span></span>

![generar vistas](~/ef6/media/generateviews.png)

<span data-ttu-id="cba22-115">Una vez finalizado el proceso, tendrá una clase similar a la siguiente generada</span><span class="sxs-lookup"><span data-stu-id="cba22-115">Once the process is finished you will have a class similar to the following generated</span></span>

![Vistas generadas](~/ef6/media/generatedviews.png)

<span data-ttu-id="cba22-117">Ahora, cuando ejecute la aplicación EF, utilizará esta clase para cargar las vistas según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="cba22-117">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="cba22-118">Si el modelo cambia y no vuelve a generar esta clase, EF producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="cba22-118">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="cba22-119">Generar vistas de asignación desde código-EF6 en adelante</span><span class="sxs-lookup"><span data-stu-id="cba22-119">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="cba22-120">La otra forma de generar vistas es usar las API que proporciona EF.</span><span class="sxs-lookup"><span data-stu-id="cba22-120">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="cba22-121">Al usar este método, tiene la libertad de serializar las vistas de la manera que desee, pero también debe cargar las vistas por su cuenta.</span><span class="sxs-lookup"><span data-stu-id="cba22-121">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="cba22-122">**EF6** y versiones posteriores: las API que se muestran en esta sección se introdujeron en Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="cba22-122">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="cba22-123">Si usa una versión anterior, esta información no se aplica.</span><span class="sxs-lookup"><span data-stu-id="cba22-123">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="cba22-124">Generar vistas</span><span class="sxs-lookup"><span data-stu-id="cba22-124">Generating Views</span></span>

<span data-ttu-id="cba22-125">Las API para generar vistas se encuentran en la clase System. Data. Entity. Core. Mapping. StorageMappingItemCollection.</span><span class="sxs-lookup"><span data-stu-id="cba22-125">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="cba22-126">Puede recuperar un StorageMappingCollection para un contexto mediante el MetadataWorkspace de un ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="cba22-126">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="cba22-127">Si usa la API DbContext más reciente, puede acceder a ella con IObjectContextAdapter como se muestra a continuación, en este código tenemos una instancia de DbContext derivado denominada dbContext:</span><span class="sxs-lookup"><span data-stu-id="cba22-127">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="cba22-128">Una vez que tenga el StorageMappingItemCollection, puede obtener acceso a los métodos GenerateViews y ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="cba22-128">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="cba22-129">El primer método crea un diccionario con una entrada para cada vista de la asignación de contenedor.</span><span class="sxs-lookup"><span data-stu-id="cba22-129">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="cba22-130">El segundo método calcula un valor hash para la asignación de un solo contenedor y se utiliza en tiempo de ejecución para validar que el modelo no ha cambiado desde que se generaron previamente las vistas.</span><span class="sxs-lookup"><span data-stu-id="cba22-130">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="cba22-131">Se proporcionan invalidaciones de los dos métodos para escenarios complejos que implican varias asignaciones de contenedor.</span><span class="sxs-lookup"><span data-stu-id="cba22-131">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="cba22-132">Al generar vistas, llamará al método GenerateViews y, a continuación, escribirá el EntitySetBase resultante y DbMappingView.</span><span class="sxs-lookup"><span data-stu-id="cba22-132">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="cba22-133">También necesitará almacenar el hash generado por el método ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="cba22-133">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="cba22-134">Cargando vistas</span><span class="sxs-lookup"><span data-stu-id="cba22-134">Loading Views</span></span>

<span data-ttu-id="cba22-135">Para cargar las vistas generadas por el método GenerateViews, puede proporcionar EF con una clase que herede de la clase abstracta DbMappingViewCache.</span><span class="sxs-lookup"><span data-stu-id="cba22-135">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="cba22-136">DbMappingViewCache especifica dos métodos que debe implementar:</span><span class="sxs-lookup"><span data-stu-id="cba22-136">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="cba22-137">La propiedad MappingHashValue debe devolver el hash generado por el método ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="cba22-137">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="cba22-138">Cuando EF vaya a pedir las vistas, primero generará y comparará el valor hash del modelo con el hash devuelto por esta propiedad.</span><span class="sxs-lookup"><span data-stu-id="cba22-138">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="cba22-139">Si no coinciden, EF producirá una excepción EntityCommandCompilationException.</span><span class="sxs-lookup"><span data-stu-id="cba22-139">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="cba22-140">El método GetView (aceptará un EntitySetBase y tendrá que devolver un DbMappingVIew que contenga el EntitySql generado para que se asoció con el EntitySetBase determinado en el Diccionario generado por el método GenerateViews.</span><span class="sxs-lookup"><span data-stu-id="cba22-140">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="cba22-141">Si EF solicita una vista que no tiene, GetView (debe devolver null.</span><span class="sxs-lookup"><span data-stu-id="cba22-141">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="cba22-142">A continuación se muestra un extracto del DbMappingViewCache que se genera con las herramientas avanzadas, como se ha descrito anteriormente, en él vemos una forma de almacenar y recuperar el EntitySql necesario.</span><span class="sxs-lookup"><span data-stu-id="cba22-142">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

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

<span data-ttu-id="cba22-143">Para que EF use el DbMappingViewCache que agregue, use el DbMappingViewCacheTypeAttribute, especificando el contexto para el que se creó.</span><span class="sxs-lookup"><span data-stu-id="cba22-143">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="cba22-144">En el código siguiente se asocia el BlogContext con la clase MyMappingViewCache.</span><span class="sxs-lookup"><span data-stu-id="cba22-144">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="cba22-145">En escenarios más complejos, se pueden proporcionar las instancias de la vista de la caché de vistas mediante la especificación de un generador de caché de vista de asignación.</span><span class="sxs-lookup"><span data-stu-id="cba22-145">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="cba22-146">Esto se puede hacer implementando la clase abstracta System. Data. Entity. Infrastructure. MappingViews. DbMappingViewCacheFactory.</span><span class="sxs-lookup"><span data-stu-id="cba22-146">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="cba22-147">La instancia del generador de caché de la vista de asignación que se usa se puede recuperar o establecer mediante StorageMappingItemCollection. MappingViewCacheFactoryproperty.</span><span class="sxs-lookup"><span data-stu-id="cba22-147">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
