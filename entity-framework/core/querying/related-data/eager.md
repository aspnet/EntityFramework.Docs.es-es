---
title: 'Carga diligente de datos relacionados: EF Core'
description: Carga diligente de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: bd9c9045c1c2707d69ee4070bea59ad8066789f3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430110"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="2c95a-103">Carga diligente de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="2c95a-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="2c95a-104">Carga diligente</span><span class="sxs-lookup"><span data-stu-id="2c95a-104">Eager loading</span></span>

<span data-ttu-id="2c95a-105">Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="2c95a-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="2c95a-106">En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.</span><span class="sxs-lookup"><span data-stu-id="2c95a-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="2c95a-107">Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto.</span><span class="sxs-lookup"><span data-stu-id="2c95a-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="2c95a-108">Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.</span><span class="sxs-lookup"><span data-stu-id="2c95a-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="2c95a-109">Puede incluir los datos relacionados de varias relaciones en una sola consulta.</span><span class="sxs-lookup"><span data-stu-id="2c95a-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> <span data-ttu-id="2c95a-110">La carga diligente de navegación de una colección en una sola consulta puede producir problemas de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="2c95a-110">Eager loading a collection navigation in a single query may cause performance issues.</span></span> <span data-ttu-id="2c95a-111">Para obtener más información, vea [Consultas únicas frente a consultas divididas](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="2c95a-111">For more information, see [Single vs. split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="including-multiple-levels"></a><span data-ttu-id="2c95a-112">Inclusión de varios niveles</span><span class="sxs-lookup"><span data-stu-id="2c95a-112">Including multiple levels</span></span>

<span data-ttu-id="2c95a-113">Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="2c95a-113">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="2c95a-114">En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.</span><span class="sxs-lookup"><span data-stu-id="2c95a-114">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="2c95a-115">Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="2c95a-115">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="2c95a-116">Puede combinar todas las llamadas para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="2c95a-116">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="2c95a-117">Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo.</span><span class="sxs-lookup"><span data-stu-id="2c95a-117">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="2c95a-118">Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`.</span><span class="sxs-lookup"><span data-stu-id="2c95a-118">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="2c95a-119">Para ello, debe especificar cada inicio de ruta de acceso de inclusión en la raíz.</span><span class="sxs-lookup"><span data-stu-id="2c95a-119">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="2c95a-120">Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="2c95a-120">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="2c95a-121">Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF mezclará las combinaciones al generar código SQL.</span><span class="sxs-lookup"><span data-stu-id="2c95a-121">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="filtered-include"></a><span data-ttu-id="2c95a-122">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="2c95a-122">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="2c95a-123">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="2c95a-123">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="2c95a-124">Al aplicar la inclusión para cargar datos relacionados, puede agregar determinadas operaciones enumerables en la navegación de colección incluida, lo que permite filtrar y ordenar los resultados.</span><span class="sxs-lookup"><span data-stu-id="2c95a-124">When applying Include to load related data, you can add certain enumerable operations to the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="2c95a-125">Las operaciones que se admiten son: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` y `Take`.</span><span class="sxs-lookup"><span data-stu-id="2c95a-125">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="2c95a-126">Dichas operaciones se deben aplicar en la navegación de colección en la expresión lambda que se pasa al método Include, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2c95a-126">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="2c95a-127">Cada navegación incluida solo permite un único conjunto de operaciones de filtro.</span><span class="sxs-lookup"><span data-stu-id="2c95a-127">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="2c95a-128">En los casos en los que se aplican varias operaciones Include para una navegación de colección determinada (`blog.Posts` en los ejemplos siguientes), las operaciones de filtro solo se pueden especificar en una de ellas:</span><span class="sxs-lookup"><span data-stu-id="2c95a-128">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="2c95a-129">En lugar de eso, se pueden aplicar operaciones idénticas para cada navegación que esté incluida varias veces:</span><span class="sxs-lookup"><span data-stu-id="2c95a-129">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="2c95a-130">En el caso de las consultas de seguimiento, los resultados de Inclusión filtrada pueden ser inesperados debido a la [corrección de la navegación](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="2c95a-130">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="2c95a-131">Todas las entidades pertinentes que se hayan consultado anteriormente y que se hayan almacenado en el seguimiento de cambios estarán presentes en los resultados de la consulta Include filtrada aunque no cumplan los requisitos del filtro.</span><span class="sxs-lookup"><span data-stu-id="2c95a-131">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="2c95a-132">Valore la posibilidad de usar consultas `NoTracking` o de volver a crear el elemento DbContext al emplear Inclusión filtrada en esas situaciones.</span><span class="sxs-lookup"><span data-stu-id="2c95a-132">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="2c95a-133">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2c95a-133">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> <span data-ttu-id="2c95a-134">En el caso de las consultas de seguimiento, se considera que se ha cargado la navegación en la que se aplicó la inclusión filtrada.</span><span class="sxs-lookup"><span data-stu-id="2c95a-134">In case of tracking queries, the navigation on which filtered include was applied is considered to be loaded.</span></span> <span data-ttu-id="2c95a-135">Esto significa que EF Core no intentará volver a cargar sus valores mediante [la ](xref:core/querying/related-data/explicit)carga explícita[ ni la ](xref:core/querying/related-data/lazy)carga diferida, aunque aún falten algunos elementos.</span><span class="sxs-lookup"><span data-stu-id="2c95a-135">This means that EF Core will not attempt to re-load it's values using [explicit loading](xref:core/querying/related-data/explicit) or [lazy loading](xref:core/querying/related-data/lazy), even though some elements could still be missing.</span></span>

## <a name="include-on-derived-types"></a><span data-ttu-id="2c95a-136">Inclusión en tipos derivados</span><span class="sxs-lookup"><span data-stu-id="2c95a-136">Include on derived types</span></span>

<span data-ttu-id="2c95a-137">Puede incluir datos relacionados provenientes de la navegación que se define solo en un tipo derivado con `Include` y `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="2c95a-137">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="2c95a-138">Dado el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2c95a-138">Given the following model:</span></span>

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

<span data-ttu-id="2c95a-139">El contenido de la navegación `School` de todas las personas que son estudiantes se puede cargar de manera diligente mediante el uso de diversos patrones:</span><span class="sxs-lookup"><span data-stu-id="2c95a-139">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="2c95a-140">con conversión</span><span class="sxs-lookup"><span data-stu-id="2c95a-140">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="2c95a-141">con el operador `as`</span><span class="sxs-lookup"><span data-stu-id="2c95a-141">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="2c95a-142">con la sobrecarga de `Include` que toma el parámetro del tipo `string`</span><span class="sxs-lookup"><span data-stu-id="2c95a-142">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
