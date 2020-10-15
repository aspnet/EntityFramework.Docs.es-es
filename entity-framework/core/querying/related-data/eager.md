---
title: 'Carga diligente de datos relacionados: EF Core'
description: Carga diligente de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 97ec45a0f8bfecce4d4a59e5d1c36c0268d96052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062581"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="bf30c-103">Carga diligente de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="bf30c-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="bf30c-104">Carga diligente</span><span class="sxs-lookup"><span data-stu-id="bf30c-104">Eager loading</span></span>

<span data-ttu-id="bf30c-105">Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="bf30c-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="bf30c-106">En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.</span><span class="sxs-lookup"><span data-stu-id="bf30c-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="bf30c-107">Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto.</span><span class="sxs-lookup"><span data-stu-id="bf30c-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="bf30c-108">Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.</span><span class="sxs-lookup"><span data-stu-id="bf30c-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="bf30c-109">Puede incluir los datos relacionados de varias relaciones en una sola consulta.</span><span class="sxs-lookup"><span data-stu-id="bf30c-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a><span data-ttu-id="bf30c-110">Inclusión de varios niveles</span><span class="sxs-lookup"><span data-stu-id="bf30c-110">Including multiple levels</span></span>

<span data-ttu-id="bf30c-111">Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="bf30c-111">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="bf30c-112">En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.</span><span class="sxs-lookup"><span data-stu-id="bf30c-112">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="bf30c-113">Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="bf30c-113">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="bf30c-114">Puede combinar todas las llamadas para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="bf30c-114">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="bf30c-115">Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo.</span><span class="sxs-lookup"><span data-stu-id="bf30c-115">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="bf30c-116">Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`.</span><span class="sxs-lookup"><span data-stu-id="bf30c-116">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="bf30c-117">Para ello, debe especificar cada inicio de ruta de acceso de inclusión en la raíz.</span><span class="sxs-lookup"><span data-stu-id="bf30c-117">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="bf30c-118">Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="bf30c-118">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="bf30c-119">Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF mezclará las combinaciones al generar código SQL.</span><span class="sxs-lookup"><span data-stu-id="bf30c-119">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a><span data-ttu-id="bf30c-120">Consultas únicas y divididas</span><span class="sxs-lookup"><span data-stu-id="bf30c-120">Single and split queries</span></span>

### <a name="single-queries"></a><span data-ttu-id="bf30c-121">Consultas únicas</span><span class="sxs-lookup"><span data-stu-id="bf30c-121">Single queries</span></span>

<span data-ttu-id="bf30c-122">En las bases de datos relacionales, todas las entidades relacionadas se cargan de forma predeterminada mediante la introducción de instrucciones JOIN:</span><span class="sxs-lookup"><span data-stu-id="bf30c-122">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="bf30c-123">Si un blog típico tiene varias entradas relacionadas, las filas de estas entradas duplicarán la información del blog, lo que conduce al problema que se conoce como "explosión cartesiana".</span><span class="sxs-lookup"><span data-stu-id="bf30c-123">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="bf30c-124">A medida que se cargan más relaciones uno a varios, la cantidad de datos duplicados puede crecer y afectar negativamente al rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bf30c-124">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="bf30c-125">De forma predeterminada, EF Core emite una advertencia si detecta consultas que cargan métodos Include de la colección que puedan provocar incidencias de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="bf30c-125">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

### <a name="split-queries"></a><span data-ttu-id="bf30c-126">Consultas divididas</span><span class="sxs-lookup"><span data-stu-id="bf30c-126">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="bf30c-127">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="bf30c-127">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="bf30c-128">EF le permite especificar que una consulta LINQ determinada se debe *dividir* en varias consultas SQL.</span><span class="sxs-lookup"><span data-stu-id="bf30c-128">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="bf30c-129">En lugar de instrucciones JOIN, las consultas divididas realizan una consulta SQL adicional por cada navegación de uno a varios incluida:</span><span class="sxs-lookup"><span data-stu-id="bf30c-129">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="bf30c-130">Generará la consulta SQL siguiente:</span><span class="sxs-lookup"><span data-stu-id="bf30c-130">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="bf30c-131">Las entidades relacionadas uno a uno se cargan siempre a través de instrucciones JOIN en la misma consulta, ya que el rendimiento no se ve afectado.</span><span class="sxs-lookup"><span data-stu-id="bf30c-131">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

### <a name="enabling-split-queries-globally"></a><span data-ttu-id="bf30c-132">Habilitación de consultas divididas globalmente</span><span class="sxs-lookup"><span data-stu-id="bf30c-132">Enabling split queries globally</span></span>

<span data-ttu-id="bf30c-133">También puede configurar consultas divididas como el valor predeterminado para el contexto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="bf30c-133">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="bf30c-134">Cuando las consultas divididas están configuradas como valor predeterminado, todavía es posible configurar consultas específicas para que se ejecuten como consultas únicas:</span><span class="sxs-lookup"><span data-stu-id="bf30c-134">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="bf30c-135">Si el modo de división de consultas no se especifica de forma explícita, ni globalmente ni en la consulta, y EF Core detecta que una sola consulta carga varios métodos Include de la colección, se emite una advertencia para llamar la atención sobre las posibles incidencias de rendimiento resultantes.</span><span class="sxs-lookup"><span data-stu-id="bf30c-135">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="bf30c-136">Establecer el modo de consulta en SingleQuery hará que no se genere la advertencia.</span><span class="sxs-lookup"><span data-stu-id="bf30c-136">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

### <a name="characteristics-of-split-queries"></a><span data-ttu-id="bf30c-137">Características de las consultas divididas</span><span class="sxs-lookup"><span data-stu-id="bf30c-137">Characteristics of split queries</span></span>

<span data-ttu-id="bf30c-138">Si bien las consultas divididas evitan las incidencias de rendimiento asociadas a las instrucciones JOIN y la explosión cartesiana, también existen algunas desventajas:</span><span class="sxs-lookup"><span data-stu-id="bf30c-138">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="bf30c-139">Aunque la mayoría de las bases de datos garantizan la coherencia de los datos en las consultas únicas, no sucede lo mismo en el caso de varias consultas.</span><span class="sxs-lookup"><span data-stu-id="bf30c-139">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="bf30c-140">Si la base de datos se actualiza de forma simultánea al ejecutar las consultas, es posible que los datos resultantes no sean coherentes.</span><span class="sxs-lookup"><span data-stu-id="bf30c-140">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="bf30c-141">Esto se puede mitigar mediante el encapsulado de las consultas en una transacción serializable o de instantáneas, aunque esto puede generar incidencias propias de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="bf30c-141">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="bf30c-142">Para obtener más información, vea la documentación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf30c-142">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="bf30c-143">Cada consulta implica actualmente un ciclo adicional de ida y vuelta de red a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf30c-143">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="bf30c-144">Varios ciclos de ida y vuelta de red pueden degradar el rendimiento, sobre todo si la latencia en la base de datos es alta (por ejemplo, servicios en la nube).</span><span class="sxs-lookup"><span data-stu-id="bf30c-144">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="bf30c-145">Aunque algunas bases de datos permiten el consumo de los resultados de varias consultas al mismo tiempo (SQL Server con MARS, SQLite), la mayoría de ellas solo permiten sola consulta activa en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="bf30c-145">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="bf30c-146">Por lo tanto, todos los resultados de las consultas anteriores deben almacenarse en búfer en la memoria de la aplicación antes de ejecutar consultas posteriores, lo que llevará a un aumento en los requisitos de memoria.</span><span class="sxs-lookup"><span data-stu-id="bf30c-146">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="bf30c-147">Por desgracia, no hay una estrategia para cargar entidades relacionadas que se ajuste a todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="bf30c-147">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="bf30c-148">Tenga en cuenta las ventajas y desventajas de las consultas únicas y divididas, y seleccione la que mejor se ajuste a sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="bf30c-148">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="bf30c-149">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="bf30c-149">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="bf30c-150">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="bf30c-150">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="bf30c-151">Al aplicar Include para cargar datos relacionados, puede aplicar determinadas operaciones enumerables en la navegación de colección incluida, lo que permite filtrar y ordenar los resultados.</span><span class="sxs-lookup"><span data-stu-id="bf30c-151">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="bf30c-152">Las operaciones que se admiten son: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` y `Take`.</span><span class="sxs-lookup"><span data-stu-id="bf30c-152">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="bf30c-153">Dichas operaciones se deben aplicar en la navegación de colección en la expresión lambda que se pasa al método Include, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bf30c-153">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="bf30c-154">Cada navegación incluida solo permite un único conjunto de operaciones de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf30c-154">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="bf30c-155">En los casos en los que se aplican varias operaciones Include para una navegación de colección determinada (`blog.Posts` en los ejemplos siguientes), las operaciones de filtro solo se pueden especificar en una de ellas:</span><span class="sxs-lookup"><span data-stu-id="bf30c-155">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="bf30c-156">En lugar de eso, se pueden aplicar operaciones idénticas para cada navegación que esté incluida varias veces:</span><span class="sxs-lookup"><span data-stu-id="bf30c-156">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="bf30c-157">En el caso de las consultas de seguimiento, los resultados de Inclusión filtrada pueden ser inesperados debido a la [corrección de la navegación](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="bf30c-157">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="bf30c-158">Todas las entidades pertinentes que se hayan consultado anteriormente y que se hayan almacenado en el seguimiento de cambios estarán presentes en los resultados de la consulta Include filtrada aunque no cumplan los requisitos del filtro.</span><span class="sxs-lookup"><span data-stu-id="bf30c-158">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="bf30c-159">Valore la posibilidad de usar consultas `NoTracking` o de volver a crear el elemento DbContext al emplear Inclusión filtrada en esas situaciones.</span><span class="sxs-lookup"><span data-stu-id="bf30c-159">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="bf30c-160">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bf30c-160">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a><span data-ttu-id="bf30c-161">Inclusión en tipos derivados</span><span class="sxs-lookup"><span data-stu-id="bf30c-161">Include on derived types</span></span>

<span data-ttu-id="bf30c-162">Puede incluir datos relacionados provenientes de la navegación que se define solo en un tipo derivado con `Include` y `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="bf30c-162">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="bf30c-163">Dado el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bf30c-163">Given the following model:</span></span>

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

<span data-ttu-id="bf30c-164">El contenido de la navegación `School` de todas las personas que son estudiantes se puede cargar de manera diligente mediante el uso de diversos patrones:</span><span class="sxs-lookup"><span data-stu-id="bf30c-164">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="bf30c-165">con conversión</span><span class="sxs-lookup"><span data-stu-id="bf30c-165">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="bf30c-166">con el operador `as`</span><span class="sxs-lookup"><span data-stu-id="bf30c-166">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="bf30c-167">con la sobrecarga de `Include` que toma el parámetro del tipo `string`</span><span class="sxs-lookup"><span data-stu-id="bf30c-167">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
