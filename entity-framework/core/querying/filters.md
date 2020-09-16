---
title: 'Filtros de consulta global: EF Core'
description: Uso de filtros de consulta globales para filtrar los resultados con Entity Framework Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d5793760ea2e61111416284db8d5a8102dd51a41
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616395"
---
# <a name="global-query-filters"></a><span data-ttu-id="d1115-103">Filtros de consulta global</span><span class="sxs-lookup"><span data-stu-id="d1115-103">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="d1115-104">Esta característica se incluyó por primera vez en EF Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="d1115-104">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="d1115-105">Los filtros de consulta global son predicados de consulta LINQ que se aplican a los tipos de entidad en el modelo de metadatos (normalmente en *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="d1115-105">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="d1115-106">Un predicado de consulta es una expresión booleana que se pasa normalmente al operador de consulta *Where* de LINQ.</span><span class="sxs-lookup"><span data-stu-id="d1115-106">A query predicate is a boolean expression typically passed to the LINQ *Where* query operator.</span></span>  <span data-ttu-id="d1115-107">EF Core aplica estos filtros automáticamente a cualquier consulta LINQ que implique esos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="d1115-107">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="d1115-108">EF Core también los aplica a los tipos de entidad, a los que se hace referencia de forma indirecta mediante el uso de la propiedad de navegación o Include.</span><span class="sxs-lookup"><span data-stu-id="d1115-108">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="d1115-109">Algunas aplicaciones comunes de esta característica son:</span><span class="sxs-lookup"><span data-stu-id="d1115-109">Some common applications of this feature are:</span></span>

* <span data-ttu-id="d1115-110">**Eliminación temporal**: un tipo de entidad define una propiedad *IsDeleted*.</span><span class="sxs-lookup"><span data-stu-id="d1115-110">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="d1115-111">**Servicios multiinquilino**: un tipo de entidad define una propiedad *TenantId*.</span><span class="sxs-lookup"><span data-stu-id="d1115-111">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="d1115-112">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="d1115-112">Example</span></span>

<span data-ttu-id="d1115-113">En el ejemplo siguiente se muestra cómo usar los filtros de consulta global para implementar los comportamientos de consulta de multiinquilino y eliminación temporal en un modelo sencillo de creación de blogs.</span><span class="sxs-lookup"><span data-stu-id="d1115-113">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="d1115-114">Puede ver un [ejemplo de servicios multiinquilino](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) y [ejemplos con navegaciones](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="d1115-114">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span>

<span data-ttu-id="d1115-115">En primer lugar, defina las entidades:</span><span class="sxs-lookup"><span data-stu-id="d1115-115">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="d1115-116">Tenga en cuenta la declaración de un campo _tenantId_ en la entidad _Blog_.</span><span class="sxs-lookup"><span data-stu-id="d1115-116">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="d1115-117">Este campo se usará para asociar cada instancia de blog a un inquilino específico.</span><span class="sxs-lookup"><span data-stu-id="d1115-117">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="d1115-118">También hay definida una propiedad _IsDeleted_ en el tipo de entidad _Post_.</span><span class="sxs-lookup"><span data-stu-id="d1115-118">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="d1115-119">Se usa para llevar un seguimiento de si una instancia _Post_ se ha eliminado de manera temporal.</span><span class="sxs-lookup"><span data-stu-id="d1115-119">This property is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="d1115-120">Es decir, la instancia se marca como eliminada sin quitar físicamente los datos subyacentes.</span><span class="sxs-lookup"><span data-stu-id="d1115-120">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="d1115-121">A continuación, configure los filtros de consulta en _OnModelCreating_ con la API `HasQueryFilter`.</span><span class="sxs-lookup"><span data-stu-id="d1115-121">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="d1115-122">Las expresiones de predicado pasadas a las llamadas de _HasQueryFilter_ ahora se aplicarán automáticamente a cualquier consulta LINQ para esos tipos.</span><span class="sxs-lookup"><span data-stu-id="d1115-122">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="d1115-123">Tenga en cuenta el uso de un campo en el nivel de instancia de DbContext: `_tenantId` se usa para establecer el inquilino actual.</span><span class="sxs-lookup"><span data-stu-id="d1115-123">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="d1115-124">Los filtros de nivel de modelo usan el valor de la instancia de contexto correcta (es decir, la instancia que está ejecutando la consulta).</span><span class="sxs-lookup"><span data-stu-id="d1115-124">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="d1115-125">Actualmente no es posible definir varios filtros de consulta en la misma entidad. Solo se aplicará el último.</span><span class="sxs-lookup"><span data-stu-id="d1115-125">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="d1115-126">Sin embargo, puede definir un único filtro con varias condiciones mediante el operador lógico _AND_ ([`&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="d1115-126">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="d1115-127">Uso de navegaciones</span><span class="sxs-lookup"><span data-stu-id="d1115-127">Use of navigations</span></span>

<span data-ttu-id="d1115-128">También se pueden usar las navegaciones en la definición de filtros de consulta global.</span><span class="sxs-lookup"><span data-stu-id="d1115-128">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="d1115-129">El uso de navegaciones en el filtro de consulta hará que los filtros de consulta se apliquen de forma recursiva.</span><span class="sxs-lookup"><span data-stu-id="d1115-129">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="d1115-130">Cuando EF Core expande las navegaciones que se usan en los filtros de consulta, también aplica los filtros de consulta definidos en las entidades a las que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="d1115-130">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="d1115-131">Actualmente EF Core no detecta ciclos en las definiciones de filtros de consulta global, por lo que debe tener cuidado al definirlas.</span><span class="sxs-lookup"><span data-stu-id="d1115-131">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="d1115-132">Si se especifican incorrectamente, los ciclos pueden provocar bucles infinitos durante la traslación de consultas.</span><span class="sxs-lookup"><span data-stu-id="d1115-132">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="d1115-133">Acceso a una entidad con filtro de consultas mediante la navegación necesaria</span><span class="sxs-lookup"><span data-stu-id="d1115-133">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="d1115-134">El uso de la navegación necesaria para acceder a la entidad que tiene definido un filtro de consulta global puede producir resultados inesperados.</span><span class="sxs-lookup"><span data-stu-id="d1115-134">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="d1115-135">La navegación necesaria espera que la entidad relacionada esté siempre presente.</span><span class="sxs-lookup"><span data-stu-id="d1115-135">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="d1115-136">Si el filtro de consulta filtra la entidad relacionada necesaria, es posible que la entidad primaria no esté en el resultado.</span><span class="sxs-lookup"><span data-stu-id="d1115-136">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="d1115-137">Por lo tanto, puede que en el resultado obtenga menos elementos de los esperados.</span><span class="sxs-lookup"><span data-stu-id="d1115-137">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="d1115-138">Para ilustrar el problema, podemos usar las entidades `Blog` y `Post` especificadas anteriormente y el siguiente método _OnModelCreating_:</span><span class="sxs-lookup"><span data-stu-id="d1115-138">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="d1115-139">El modelo se puede inicializar con los siguientes datos:</span><span class="sxs-lookup"><span data-stu-id="d1115-139">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="d1115-140">Se puede observar el problema al ejecutar dos consultas:</span><span class="sxs-lookup"><span data-stu-id="d1115-140">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="d1115-141">Con la configuración anterior, la primera consulta devuelve las 6 `Post`; sin embargo, la segunda consulta solo devuelve 3.</span><span class="sxs-lookup"><span data-stu-id="d1115-141">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="d1115-142">Esta falta de coincidencia se produce porque el método _Include_ de la segunda consulta carga las entidades `Blog` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="d1115-142">This mismatch happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="d1115-143">Dado que se requiere la navegación entre `Blog` y `Post`, EF Core utiliza `INNER JOIN` al construir la consulta:</span><span class="sxs-lookup"><span data-stu-id="d1115-143">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="d1115-144">El uso de `INNER JOIN` filtra todos los valores `Post` cuyos `Blog` relacionados se han quitado mediante un filtro de consulta global.</span><span class="sxs-lookup"><span data-stu-id="d1115-144">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="d1115-145">Se puede solucionar mediante el uso de la navegación opcional, en lugar de la necesaria.</span><span class="sxs-lookup"><span data-stu-id="d1115-145">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="d1115-146">De este modo, la primera consulta se mantiene igual que antes, pero la segunda consulta generará `LEFT JOIN` y devolverá 6 resultados.</span><span class="sxs-lookup"><span data-stu-id="d1115-146">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="d1115-147">Un enfoque alternativo consiste en especificar filtros coherentes en las entidades `Blog` y `Post`.</span><span class="sxs-lookup"><span data-stu-id="d1115-147">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="d1115-148">De este modo, los filtros coincidentes se aplican tanto a `Blog` como a `Post`.</span><span class="sxs-lookup"><span data-stu-id="d1115-148">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="d1115-149">Las `Post` que podrían acabar en un estado inesperado se quitan y ambas consultas devuelven 3 resultados.</span><span class="sxs-lookup"><span data-stu-id="d1115-149">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="d1115-150">Deshabilitación de filtros</span><span class="sxs-lookup"><span data-stu-id="d1115-150">Disabling Filters</span></span>

<span data-ttu-id="d1115-151">Los filtros se pueden deshabilitar para consultas LINQ individuales mediante el operador `IgnoreQueryFilters()`.</span><span class="sxs-lookup"><span data-stu-id="d1115-151">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="d1115-152">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="d1115-152">Limitations</span></span>

<span data-ttu-id="d1115-153">Los filtros de consulta global tienen las limitaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d1115-153">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="d1115-154">Solo se pueden definir filtros para el tipo de entidad raíz de una jerarquía de herencia.</span><span class="sxs-lookup"><span data-stu-id="d1115-154">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
