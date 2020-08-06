---
title: 'Filtros de consulta global: EF Core'
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 57d81919dedb853d2a41066f76ec20685ae41d6b
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526906"
---
# <a name="global-query-filters"></a><span data-ttu-id="eebf6-102">Filtros de consulta global</span><span class="sxs-lookup"><span data-stu-id="eebf6-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="eebf6-103">Esta característica se incluyó por primera vez en EF Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="eebf6-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="eebf6-104">Los filtros de consulta global son predicados de consulta LINQ (una expresión booleana que habitualmente se pasa al operador de consulta LINQ *Where*) aplicados a los tipos de entidad del modelo de metadatos (habitualmente en *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="eebf6-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="eebf6-105">Estos filtros se aplican automáticamente a las consultas LINQ que implican a esos tipos de entidad, incluidos aquellos a los que se hace referencia de forma indirecta, por ejemplo mediante el uso de Include o de referencias de propiedad de navegación directas.</span><span class="sxs-lookup"><span data-stu-id="eebf6-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="eebf6-106">Algunas aplicaciones comunes de esta característica son:</span><span class="sxs-lookup"><span data-stu-id="eebf6-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="eebf6-107">**Eliminación temporal**: un tipo de entidad define una propiedad *IsDeleted*.</span><span class="sxs-lookup"><span data-stu-id="eebf6-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="eebf6-108">**Servicios multiinquilino**: un tipo de entidad define una propiedad *TenantId*.</span><span class="sxs-lookup"><span data-stu-id="eebf6-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="eebf6-109">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="eebf6-109">Example</span></span>

<span data-ttu-id="eebf6-110">En el ejemplo siguiente se muestra cómo usar los filtros de consulta global para implementar los comportamientos de consulta de multiinquilino y de eliminación temporal en un simple modelo de creación de blogs.</span><span class="sxs-lookup"><span data-stu-id="eebf6-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="eebf6-111">Puede ver un [ejemplo de servicios multiinquilino](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) y [ejemplos con navegaciones](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="eebf6-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="eebf6-112">En primer lugar, defina las entidades:</span><span class="sxs-lookup"><span data-stu-id="eebf6-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="eebf6-113">Tenga en cuenta la declaración de un campo _tenantId_ en la entidad _Blog_.</span><span class="sxs-lookup"><span data-stu-id="eebf6-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="eebf6-114">Se usará para asociar cada instancia de blog con un inquilino específico.</span><span class="sxs-lookup"><span data-stu-id="eebf6-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="eebf6-115">También hay definida una propiedad _IsDeleted_ en el tipo de entidad _Post_.</span><span class="sxs-lookup"><span data-stu-id="eebf6-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="eebf6-116">Se usa para llevar un seguimiento de si una instancia _Post_ se eliminó de manera temporal.</span><span class="sxs-lookup"><span data-stu-id="eebf6-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="eebf6-117">Es decir, la instancia se marca como eliminada sin quitar físicamente los datos subyacentes.</span><span class="sxs-lookup"><span data-stu-id="eebf6-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="eebf6-118">A continuación, configure los filtros de consulta en _OnModelCreating_ con la API `HasQueryFilter`.</span><span class="sxs-lookup"><span data-stu-id="eebf6-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="eebf6-119">Las expresiones de predicado pasadas a las llamadas de _HasQueryFilter_ ahora se aplicarán automáticamente a cualquier consulta LINQ para esos tipos.</span><span class="sxs-lookup"><span data-stu-id="eebf6-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="eebf6-120">Tenga en cuenta el uso de un campo en el nivel de instancia de DbContext: `_tenantId` se usa para establecer el inquilino actual.</span><span class="sxs-lookup"><span data-stu-id="eebf6-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="eebf6-121">Los filtros de nivel de modelo usan el valor de la instancia de contexto correcta (es decir, la instancia que está ejecutando la consulta).</span><span class="sxs-lookup"><span data-stu-id="eebf6-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="eebf6-122">Actualmente no es posible definir varios filtros de consulta en la misma entidad. Solo se aplicará el último.</span><span class="sxs-lookup"><span data-stu-id="eebf6-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="eebf6-123">Sin embargo, puede definir un único filtro con varias condiciones mediante el operador lógico _AND_ ([`&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="eebf6-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="eebf6-124">Uso de navegaciones</span><span class="sxs-lookup"><span data-stu-id="eebf6-124">Use of navigations</span></span>

<span data-ttu-id="eebf6-125">Las navegaciones se pueden utilizar al definir filtros de consulta global.</span><span class="sxs-lookup"><span data-stu-id="eebf6-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="eebf6-126">Se aplican de forma recursiva: cuando se trasladan las navegaciones utilizadas en los filtros de consulta, también se aplican los filtros de consulta definidos en las entidades a las que se hace referencia, lo que puede agregar más navegaciones.</span><span class="sxs-lookup"><span data-stu-id="eebf6-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="eebf6-127">Actualmente EF Core no detecta ciclos en las definiciones de filtros de consulta global, por lo que debe tener cuidado al definirlas.</span><span class="sxs-lookup"><span data-stu-id="eebf6-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="eebf6-128">Si se especifica incorrectamente, podrían producirse bucles infinitos durante la traslación de consultas.</span><span class="sxs-lookup"><span data-stu-id="eebf6-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="eebf6-129">Acceso a una entidad con filtro de consultas mediante la navegación necesaria</span><span class="sxs-lookup"><span data-stu-id="eebf6-129">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="eebf6-130">El uso de la navegación necesaria para acceder a la entidad que tiene definido un filtro de consulta global puede producir resultados inesperados.</span><span class="sxs-lookup"><span data-stu-id="eebf6-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="eebf6-131">La navegación necesaria espera que la entidad relacionada esté siempre presente.</span><span class="sxs-lookup"><span data-stu-id="eebf6-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="eebf6-132">Si el filtro de consultas filtra la entidad relacionada necesaria, la entidad principal podría acabar en un estado inesperado.</span><span class="sxs-lookup"><span data-stu-id="eebf6-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="eebf6-133">Esto puede dar lugar a que se devuelvan menos elementos de los esperados.</span><span class="sxs-lookup"><span data-stu-id="eebf6-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="eebf6-134">Para ilustrar el problema, podemos usar las entidades `Blog` y `Post` especificadas anteriormente y el siguiente método _OnModelCreating_:</span><span class="sxs-lookup"><span data-stu-id="eebf6-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="eebf6-135">El modelo se puede inicializar con los siguientes datos:</span><span class="sxs-lookup"><span data-stu-id="eebf6-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="eebf6-136">Se puede observar el problema al ejecutar dos consultas:</span><span class="sxs-lookup"><span data-stu-id="eebf6-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="eebf6-137">Con esta configuración, la primera consulta devuelve las 6 `Post`; sin embargo, la segunda consulta solo devuelve 3.</span><span class="sxs-lookup"><span data-stu-id="eebf6-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="eebf6-138">Esto sucede porque el método _Include_ de la segunda consulta carga las entidades `Blog` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="eebf6-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="eebf6-139">Dado que se requiere la navegación entre `Blog` y `Post`, EF Core utiliza `INNER JOIN` al construir la consulta:</span><span class="sxs-lookup"><span data-stu-id="eebf6-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="eebf6-140">El uso de `INNER JOIN` filtra todos los valores `Post` cuyos `Blog` relacionados se han quitado mediante un filtro de consulta global.</span><span class="sxs-lookup"><span data-stu-id="eebf6-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="eebf6-141">Se puede solucionar mediante el uso de la navegación opcional, en lugar de la necesaria.</span><span class="sxs-lookup"><span data-stu-id="eebf6-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="eebf6-142">De este modo, la primera consulta se mantiene igual que antes, pero la segunda consulta generará `LEFT JOIN` y devolverá 6 resultados.</span><span class="sxs-lookup"><span data-stu-id="eebf6-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="eebf6-143">Un enfoque alternativo consiste en especificar filtros coherentes en las entidades `Blog` y `Post`.</span><span class="sxs-lookup"><span data-stu-id="eebf6-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="eebf6-144">De este modo, los filtros coincidentes se aplican tanto a `Blog` como a `Post`.</span><span class="sxs-lookup"><span data-stu-id="eebf6-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="eebf6-145">Las `Post` que podrían acabar en un estado inesperado se quitan y ambas consultas devuelven 3 resultados.</span><span class="sxs-lookup"><span data-stu-id="eebf6-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="eebf6-146">Deshabilitación de filtros</span><span class="sxs-lookup"><span data-stu-id="eebf6-146">Disabling Filters</span></span>

<span data-ttu-id="eebf6-147">Los filtros se pueden deshabilitar para consultas LINQ individuales mediante el operador `IgnoreQueryFilters()`.</span><span class="sxs-lookup"><span data-stu-id="eebf6-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="eebf6-148">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="eebf6-148">Limitations</span></span>

<span data-ttu-id="eebf6-149">Los filtros de consulta global tienen las limitaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="eebf6-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="eebf6-150">Solo se pueden definir filtros para el tipo de entidad raíz de una jerarquía de herencia.</span><span class="sxs-lookup"><span data-stu-id="eebf6-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
