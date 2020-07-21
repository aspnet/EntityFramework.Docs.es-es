---
title: 'Carga de datos relacionados: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: d3a1810599771befb451715d93454fff63949771
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238312"
---
# <a name="loading-related-data"></a><span data-ttu-id="e451a-102">Carga de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="e451a-102">Loading Related Data</span></span>

<span data-ttu-id="e451a-103">Entity Framework Core permite usar las propiedades de navegación del modelo para cargar las entidades relacionados.</span><span class="sxs-lookup"><span data-stu-id="e451a-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="e451a-104">Existen tres patrones de O/RM comunes que se usan para cargar los datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="e451a-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="e451a-105">**Carga diligente** significa que los datos relacionados se cargan desde la base de datos como parte de la consulta inicial.</span><span class="sxs-lookup"><span data-stu-id="e451a-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="e451a-106">**Carga explícita** significa que los datos relacionados se cargan de manera explícita desde la base de datos más adelante.</span><span class="sxs-lookup"><span data-stu-id="e451a-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="e451a-107">**Carga diferida** significa que los datos relacionados se cargan de manera transparente desde la base de datos cuando se accede a la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="e451a-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="e451a-108">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="e451a-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="e451a-109">Carga diligente</span><span class="sxs-lookup"><span data-stu-id="e451a-109">Eager loading</span></span>

<span data-ttu-id="e451a-110">Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="e451a-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="e451a-111">En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.</span><span class="sxs-lookup"><span data-stu-id="e451a-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="e451a-112">Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto.</span><span class="sxs-lookup"><span data-stu-id="e451a-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="e451a-113">Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.</span><span class="sxs-lookup"><span data-stu-id="e451a-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="e451a-114">Puede incluir los datos relacionados de varias relaciones en una sola consulta.</span><span class="sxs-lookup"><span data-stu-id="e451a-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="e451a-115">Inclusión de varios niveles</span><span class="sxs-lookup"><span data-stu-id="e451a-115">Including multiple levels</span></span>

<span data-ttu-id="e451a-116">Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="e451a-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="e451a-117">En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.</span><span class="sxs-lookup"><span data-stu-id="e451a-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="e451a-118">Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="e451a-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="e451a-119">Puede combinar todo esto para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="e451a-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="e451a-120">Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo.</span><span class="sxs-lookup"><span data-stu-id="e451a-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="e451a-121">Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`.</span><span class="sxs-lookup"><span data-stu-id="e451a-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="e451a-122">Para hacerlo, debe especificar cada inicio de ruta de acceso de inclusión en la raíz.</span><span class="sxs-lookup"><span data-stu-id="e451a-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="e451a-123">Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="e451a-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="e451a-124">Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF consolidará las combinaciones al generar código SQL.</span><span class="sxs-lookup"><span data-stu-id="e451a-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="e451a-125">Consultas únicas y divididas</span><span class="sxs-lookup"><span data-stu-id="e451a-125">Single and split queries</span></span>

> [!NOTE]
> <span data-ttu-id="e451a-126">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="e451a-126">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e451a-127">En las bases de datos relacionales, todas las entidades relacionadas se cargan de forma predeterminada mediante la introducción de instrucciones JOIN:</span><span class="sxs-lookup"><span data-stu-id="e451a-127">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="e451a-128">Si un blog típico tiene varias entradas relacionadas, las filas de estas entradas duplicarán la información del blog, lo que conduce al problema que se conoce como "explosión cartesiana".</span><span class="sxs-lookup"><span data-stu-id="e451a-128">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="e451a-129">A medida que se cargan más relaciones uno a varios, la cantidad de datos duplicados puede crecer y afectar negativamente al rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e451a-129">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

<span data-ttu-id="e451a-130">EF le permite especificar que una consulta LINQ determinada se debe *dividir* en varias consultas SQL.</span><span class="sxs-lookup"><span data-stu-id="e451a-130">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="e451a-131">En lugar de instrucciones JOIN, las consultas divididas realizan una consulta SQL adicional por cada navegación de uno a varios incluida:</span><span class="sxs-lookup"><span data-stu-id="e451a-131">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="e451a-132">El resultado es la siguiente consulta SQL:</span><span class="sxs-lookup"><span data-stu-id="e451a-132">This will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

<span data-ttu-id="e451a-133">Si bien se evitan los problemas de rendimiento asociados a las instrucciones JOIN y la explosión cartesiana, también existen algunas desventajas:</span><span class="sxs-lookup"><span data-stu-id="e451a-133">While this avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="e451a-134">Aunque la mayoría de las bases de datos garantizan la coherencia de los datos en las consultas únicas, no sucede lo mismo en el caso de varias consultas.</span><span class="sxs-lookup"><span data-stu-id="e451a-134">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="e451a-135">Esto significa que, si la base de datos se actualiza al mismo tiempo que se ejecutan las consultas, es posible que los datos resultantes no sean coherentes.</span><span class="sxs-lookup"><span data-stu-id="e451a-135">This means that if the database is being updated concurrently as your queries are being executed, resulting data may not be consistent.</span></span> <span data-ttu-id="e451a-136">Para resolver este problema, se pueden encapsular las consultas en una transacción serializable o de instantáneas, aunque esta solución puede generar problemas de rendimiento propios.</span><span class="sxs-lookup"><span data-stu-id="e451a-136">This may be mitigated by wrapping the queries in a serializable or snapshot transaction, although this may create performance issues of its own.</span></span> <span data-ttu-id="e451a-137">Para más información, consulte la documentación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e451a-137">Consult your database's documentation for more details.</span></span>
* <span data-ttu-id="e451a-138">Cada consulta supone actualmente un recorrido adicional de ida y vuelta de la red a la base de datos; como consecuencia, se puede degradar el rendimiento, en especial cuando la latencia hacia la base de datos es alta (por ejemplo, servicios en la nube).</span><span class="sxs-lookup"><span data-stu-id="e451a-138">Each query currently implies an additional network roundtrip to your database; this can degrade performance, especially where latency to the database is high (e.g. cloud services).</span></span> <span data-ttu-id="e451a-139">EF Core mejorará esta situación en el futuro gracias al procesamiento por lotes de las consultas en un único recorrido de ida y vuelta.</span><span class="sxs-lookup"><span data-stu-id="e451a-139">EF Core will improve this in the future by batching the queries into a single roundtrip.</span></span>
* <span data-ttu-id="e451a-140">Aunque algunas bases de datos permiten el consumo de los resultados de varias consultas al mismo tiempo (SQL Server con MARS, SQLite), la mayoría de ellas solo permiten sola consulta activa en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="e451a-140">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="e451a-141">Esto significa que todos los resultados de las consultas anteriores deben almacenarse en búfer en la memoria de la aplicación antes de ejecutar consultas posteriores, lo que aumenta los requisitos de memoria de una manera importante.</span><span class="sxs-lookup"><span data-stu-id="e451a-141">This means that all results from earlier queries must be buffered in your application's memory before executing later queries, increasing your memory requirements in a potentially significant way.</span></span>

<span data-ttu-id="e451a-142">Por desgracia, no hay una estrategia para cargar entidades relacionadas que se ajuste a todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="e451a-142">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="e451a-143">Tenga en cuenta las ventajas y desventajas de las consultas únicas y divididas, y seleccione la que mejor se ajuste a sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="e451a-143">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

> [!NOTE]
> <span data-ttu-id="e451a-144">Las entidades relacionadas uno a uno se cargan siempre a través de instrucciones JOIN, ya que el rendimiento no se ve afectado.</span><span class="sxs-lookup"><span data-stu-id="e451a-144">One-to-one related entities are always loaded via JOINs, as this has no performance impact.</span></span>
>
> <span data-ttu-id="e451a-145">Por el momento, el uso de la división de consultas en SQL Server requiere la configuración `MultipleActiveResultSets=true` en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="e451a-145">At the moment, use of query splitting on SQL Server requires settings `MultipleActiveResultSets=true` in your connection string.</span></span> <span data-ttu-id="e451a-146">Este requisito desaparecerá en una versión preliminar futura.</span><span class="sxs-lookup"><span data-stu-id="e451a-146">This requirement will be removed in a future preview.</span></span>
>
> <span data-ttu-id="e451a-147">Las versiones preliminares futuras de EF Core 5.0 permitirán especificar la división de consultas como valor predeterminado del contexto.</span><span class="sxs-lookup"><span data-stu-id="e451a-147">Future previews of EF Core 5.0 will allow specifying query splitting as the default for your context.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="e451a-148">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="e451a-148">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="e451a-149">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="e451a-149">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e451a-150">Al aplicar Include para cargar datos relacionados, puede aplicar determinadas operaciones enumerables en la navegación de colección incluida, lo que permite filtrar y ordenar los resultados.</span><span class="sxs-lookup"><span data-stu-id="e451a-150">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="e451a-151">Las operaciones que se admiten son: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` y `Take`.</span><span class="sxs-lookup"><span data-stu-id="e451a-151">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="e451a-152">Dichas operaciones se deben aplicar en la navegación de colección en la expresión lambda que se pasa al método Include, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e451a-152">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="e451a-153">Cada navegación incluida solo permite un único conjunto de operaciones de filtro.</span><span class="sxs-lookup"><span data-stu-id="e451a-153">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="e451a-154">En los casos en los que se aplican varias operaciones Include para una navegación de colección determinada (`blog.Posts` en los ejemplos siguientes), las operaciones de filtro solo se pueden especificar en una de ellas:</span><span class="sxs-lookup"><span data-stu-id="e451a-154">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="e451a-155">De manera alternativa, se pueden aplicar operaciones idénticas para cada navegación que esté incluida varias veces:</span><span class="sxs-lookup"><span data-stu-id="e451a-155">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="e451a-156">En el caso de las consultas de seguimiento, los resultados de Inclusión filtrada pueden ser inesperados debido a la [corrección de la navegación](tracking.md).</span><span class="sxs-lookup"><span data-stu-id="e451a-156">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](tracking.md).</span></span> <span data-ttu-id="e451a-157">Todas las entidades pertinentes que se hayan consultado anteriormente y que se hayan almacenado en el seguimiento de cambios estarán presentes en los resultados de la consulta Inclusión filtrada aunque no cumplan los requisitos del filtro.</span><span class="sxs-lookup"><span data-stu-id="e451a-157">All relevant entities that have been querried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="e451a-158">Valore la posibilidad de usar consultas `NoTracking` o de volver a crear el elemento DbContext al emplear Inclusión filtrada en esas situaciones.</span><span class="sxs-lookup"><span data-stu-id="e451a-158">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="e451a-159">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e451a-159">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="e451a-160">Inclusión en tipos derivados</span><span class="sxs-lookup"><span data-stu-id="e451a-160">Include on derived types</span></span>

<span data-ttu-id="e451a-161">Puede incluir datos relacionados provenientes de las navegaciones que se definen solo en un tipo derivado con `Include` y `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="e451a-161">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="e451a-162">Dado el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e451a-162">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="e451a-163">El contenido de la navegación `School` de todas las personas que son estudiantes se puede cargar de manera diligente mediante el uso de diversos patrones:</span><span class="sxs-lookup"><span data-stu-id="e451a-163">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="e451a-164">con conversión</span><span class="sxs-lookup"><span data-stu-id="e451a-164">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="e451a-165">con el operador `as`</span><span class="sxs-lookup"><span data-stu-id="e451a-165">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="e451a-166">con la sobrecarga de `Include` que toma el parámetro del tipo `string`</span><span class="sxs-lookup"><span data-stu-id="e451a-166">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="e451a-167">Carga explícita</span><span class="sxs-lookup"><span data-stu-id="e451a-167">Explicit loading</span></span>

<span data-ttu-id="e451a-168">Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="e451a-168">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="e451a-169">También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e451a-169">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="e451a-170">Si está habilitado el seguimiento de cambios, cuando se cargue una entidad, EF Core establecerá automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada y establecerá las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recién cargada.</span><span class="sxs-lookup"><span data-stu-id="e451a-170">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="e451a-171">Consulta de las entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="e451a-171">Querying related entities</span></span>

<span data-ttu-id="e451a-172">También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="e451a-172">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="e451a-173">Esto permite, entre otras acciones, ejecutar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.</span><span class="sxs-lookup"><span data-stu-id="e451a-173">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="e451a-174">También puede filtrar las entidades relacionadas que se cargan en la memoria.</span><span class="sxs-lookup"><span data-stu-id="e451a-174">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="e451a-175">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="e451a-175">Lazy loading</span></span>

<span data-ttu-id="e451a-176">La manera más simple de usar la carga diferida es instalar el paquete [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) y habilitarlo con una llamada a `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="e451a-176">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="e451a-177">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e451a-177">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="e451a-178">O al usar AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="e451a-178">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="e451a-179">EF Core habilitará la carga diferida de cualquier propiedad de navegación que se pueda invalidar, es decir, debe ser `virtual` y debe estar en una clase desde la que se pueda heredar.</span><span class="sxs-lookup"><span data-stu-id="e451a-179">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="e451a-180">Por ejemplo, en las entidades siguientes, las propiedades de navegación `Post.Blog` y `Blog.Posts` serán de carga diferida.</span><span class="sxs-lookup"><span data-stu-id="e451a-180">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="e451a-181">Carga diferida sin servidores proxy</span><span class="sxs-lookup"><span data-stu-id="e451a-181">Lazy loading without proxies</span></span>

<span data-ttu-id="e451a-182">Los servidores proxy de carga diferida funcionan inyectando el servicio `ILazyLoader` en una entidad, tal como se describe en [Entity Type Constructors](../modeling/constructors.md) (Constructores de tipo de entidad).</span><span class="sxs-lookup"><span data-stu-id="e451a-182">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="e451a-183">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e451a-183">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="e451a-184">Esto no requiere tipos de entidad de los cuales heredar ni propiedades de navegación para ser virtual y permite que las instancias de entidad creadas con `new` se carguen de manera diferida una vez que se asocian a un contexto.</span><span class="sxs-lookup"><span data-stu-id="e451a-184">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="e451a-185">Sin embargo, requiere una referencia al servicio `ILazyLoader`, que está definido en el paquete [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="e451a-185">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="e451a-186">Este paquete contiene un conjunto mínimo de tipos, por lo que es muy poco el impacto al depender de él.</span><span class="sxs-lookup"><span data-stu-id="e451a-186">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="e451a-187">Sin embargo, para evitar por completo depender de cualquier paquete de EF Core en los tipos de entidad, es posible insertar el método `ILazyLoader.Load` como delegado.</span><span class="sxs-lookup"><span data-stu-id="e451a-187">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="e451a-188">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e451a-188">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="e451a-189">El código anterior usa un método de extensión `Load` para que el uso del delegado sea un poco más limpio:</span><span class="sxs-lookup"><span data-stu-id="e451a-189">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="e451a-190">El parámetro de constructor del delegado de carga diferida se debe denominar "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="e451a-190">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="e451a-191">La configuración para usar otro nombre está planificada para una versión futura.</span><span class="sxs-lookup"><span data-stu-id="e451a-191">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="e451a-192">Datos relacionados y serialización</span><span class="sxs-lookup"><span data-stu-id="e451a-192">Related data and serialization</span></span>

<span data-ttu-id="e451a-193">Como EF Core corregirá automáticamente las propiedades de navegación, puede terminar con ciclos en el grafo de objetos.</span><span class="sxs-lookup"><span data-stu-id="e451a-193">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="e451a-194">Por ejemplo, cargar un blog y sus entradas relacionadas dará lugar a un objeto de blog que hará referencia a una colección de entradas.</span><span class="sxs-lookup"><span data-stu-id="e451a-194">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="e451a-195">Cada una de esas entradas tendrá una referencia de vuelta al blog.</span><span class="sxs-lookup"><span data-stu-id="e451a-195">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="e451a-196">Algunos marcos de serialización no permiten ese tipo de ciclos.</span><span class="sxs-lookup"><span data-stu-id="e451a-196">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="e451a-197">Por ejemplo, JSON.NET generará la excepción siguiente si se encuentra un ciclo.</span><span class="sxs-lookup"><span data-stu-id="e451a-197">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="e451a-198">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: se detectó un bucle con autorreferencia para la propiedad "Blog" con el tipo "MyApplication.Models.Blog").</span><span class="sxs-lookup"><span data-stu-id="e451a-198">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="e451a-199">Si usa ASP.NET Core, puede configurar JSON.NET para que omita los ciclos que encuentre en el grafo del objeto.</span><span class="sxs-lookup"><span data-stu-id="e451a-199">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="e451a-200">Esto se hace en el método `ConfigureServices(...)` en `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="e451a-200">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="e451a-201">Otra alternativa consiste en decorar una de las propiedades de navegación con el atributo `[JsonIgnore]`, que indica a JSON.NET que no recorra esa propiedad de navegación mientras se serializa.</span><span class="sxs-lookup"><span data-stu-id="e451a-201">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
