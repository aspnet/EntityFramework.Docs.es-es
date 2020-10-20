---
title: 'Filtros de consulta global: EF Core'
description: Uso de filtros de consulta globales para filtrar los resultados con Entity Framework Core
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 8a9eabd7e86864c9ebb4b1dc4a06bf7fc207d496
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062612"
---
# <a name="global-query-filters"></a><span data-ttu-id="c488f-103">Filtros de consulta global</span><span class="sxs-lookup"><span data-stu-id="c488f-103">Global Query Filters</span></span>

<span data-ttu-id="c488f-104">Los filtros de consulta global son predicados de consulta LINQ que se aplican a los tipos de entidad en el modelo de metadatos (normalmente en `OnModelCreating`).</span><span class="sxs-lookup"><span data-stu-id="c488f-104">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in `OnModelCreating`).</span></span> <span data-ttu-id="c488f-105">Un predicado de consulta es una expresión booleana que se pasa normalmente al operador de consulta `Where` de LINQ.</span><span class="sxs-lookup"><span data-stu-id="c488f-105">A query predicate is a boolean expression typically passed to the LINQ `Where` query operator.</span></span>  <span data-ttu-id="c488f-106">EF Core aplica estos filtros automáticamente a cualquier consulta LINQ que implique esos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="c488f-106">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="c488f-107">EF Core también los aplica a los tipos de entidad, a los que se hace referencia de forma indirecta mediante el uso de la propiedad de navegación o Include.</span><span class="sxs-lookup"><span data-stu-id="c488f-107">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="c488f-108">Algunas aplicaciones comunes de esta característica son:</span><span class="sxs-lookup"><span data-stu-id="c488f-108">Some common applications of this feature are:</span></span>

* <span data-ttu-id="c488f-109">**Eliminación temporal**: un tipo de entidad define una propiedad `IsDeleted`.</span><span class="sxs-lookup"><span data-stu-id="c488f-109">**Soft delete** - An Entity Type defines an `IsDeleted` property.</span></span>
* <span data-ttu-id="c488f-110">**Servicios multiinquilino**: un tipo de entidad define una propiedad `TenantId`.</span><span class="sxs-lookup"><span data-stu-id="c488f-110">**Multi-tenancy** - An Entity Type defines a `TenantId` property.</span></span>

## <a name="example"></a><span data-ttu-id="c488f-111">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="c488f-111">Example</span></span>

<span data-ttu-id="c488f-112">En el ejemplo siguiente se muestra cómo usar los filtros de consulta global para implementar los comportamientos de consulta de multiinquilino y eliminación temporal en un modelo sencillo de creación de blogs.</span><span class="sxs-lookup"><span data-stu-id="c488f-112">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="c488f-113">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="c488f-113">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="c488f-114">En primer lugar, defina las entidades:</span><span class="sxs-lookup"><span data-stu-id="c488f-114">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

<span data-ttu-id="c488f-115">Tenga en cuenta la declaración de un campo `_tenantId` en la entidad `Blog`.</span><span class="sxs-lookup"><span data-stu-id="c488f-115">Note the declaration of a `_tenantId` field on the `Blog` entity.</span></span> <span data-ttu-id="c488f-116">Este campo se usará para asociar cada instancia de blog a un inquilino específico.</span><span class="sxs-lookup"><span data-stu-id="c488f-116">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="c488f-117">También hay definida una propiedad `IsDeleted` en el tipo de entidad `Post`.</span><span class="sxs-lookup"><span data-stu-id="c488f-117">Also defined is an `IsDeleted` property on the `Post` entity type.</span></span> <span data-ttu-id="c488f-118">Se usa para llevar un seguimiento de si una instancia post se ha eliminado de manera temporal.</span><span class="sxs-lookup"><span data-stu-id="c488f-118">This property is used to keep track of whether a post instance has been "soft-deleted".</span></span> <span data-ttu-id="c488f-119">Es decir, la instancia se marca como eliminada sin quitar físicamente los datos subyacentes.</span><span class="sxs-lookup"><span data-stu-id="c488f-119">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="c488f-120">A continuación, configure los filtros de consulta en `OnModelCreating` con la API `HasQueryFilter`.</span><span class="sxs-lookup"><span data-stu-id="c488f-120">Next, configure the query filters in `OnModelCreating` using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

<span data-ttu-id="c488f-121">Las expresiones de predicado pasadas a las llamadas de `HasQueryFilter` ahora se aplicarán automáticamente a cualquier consulta LINQ para esos tipos.</span><span class="sxs-lookup"><span data-stu-id="c488f-121">The predicate expressions passed to the `HasQueryFilter` calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="c488f-122">Tenga en cuenta el uso de un campo en el nivel de instancia de DbContext: `_tenantId` se usa para establecer el inquilino actual.</span><span class="sxs-lookup"><span data-stu-id="c488f-122">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="c488f-123">Los filtros de nivel de modelo usan el valor de la instancia de contexto correcta (es decir, la instancia que está ejecutando la consulta).</span><span class="sxs-lookup"><span data-stu-id="c488f-123">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="c488f-124">Actualmente no es posible definir varios filtros de consulta en la misma entidad. Solo se aplicará el último.</span><span class="sxs-lookup"><span data-stu-id="c488f-124">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="c488f-125">Sin embargo, puede definir un único filtro con varias condiciones mediante el operador lógico `AND` ([`&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="c488f-125">However, you can define a single filter with multiple conditions using the logical `AND` operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="c488f-126">Uso de navegaciones</span><span class="sxs-lookup"><span data-stu-id="c488f-126">Use of navigations</span></span>

<span data-ttu-id="c488f-127">También se pueden usar las navegaciones en la definición de filtros de consulta global.</span><span class="sxs-lookup"><span data-stu-id="c488f-127">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="c488f-128">El uso de navegaciones en el filtro de consulta hará que los filtros de consulta se apliquen de forma recursiva.</span><span class="sxs-lookup"><span data-stu-id="c488f-128">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="c488f-129">Cuando EF Core expande las navegaciones que se usan en los filtros de consulta, también aplica los filtros de consulta definidos en las entidades a las que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="c488f-129">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="c488f-130">Actualmente EF Core no detecta ciclos en las definiciones de filtros de consulta global, por lo que debe tener cuidado al definirlas.</span><span class="sxs-lookup"><span data-stu-id="c488f-130">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="c488f-131">Si se especifican incorrectamente, los ciclos pueden provocar bucles infinitos durante la traslación de consultas.</span><span class="sxs-lookup"><span data-stu-id="c488f-131">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="c488f-132">Acceso a una entidad con filtro de consultas mediante la navegación necesaria</span><span class="sxs-lookup"><span data-stu-id="c488f-132">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="c488f-133">El uso de la navegación necesaria para acceder a la entidad que tiene definido un filtro de consulta global puede producir resultados inesperados.</span><span class="sxs-lookup"><span data-stu-id="c488f-133">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="c488f-134">La navegación necesaria espera que la entidad relacionada esté siempre presente.</span><span class="sxs-lookup"><span data-stu-id="c488f-134">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="c488f-135">Si el filtro de consulta filtra la entidad relacionada necesaria, es posible que la entidad primaria no esté en el resultado.</span><span class="sxs-lookup"><span data-stu-id="c488f-135">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="c488f-136">Por lo tanto, puede que en el resultado obtenga menos elementos de los esperados.</span><span class="sxs-lookup"><span data-stu-id="c488f-136">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="c488f-137">Para ilustrar el problema, podemos usar las entidades `Blog` y `Post` especificadas anteriormente y el siguiente método `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="c488f-137">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following `OnModelCreating` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

<span data-ttu-id="c488f-138">El modelo se puede inicializar con los siguientes datos:</span><span class="sxs-lookup"><span data-stu-id="c488f-138">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

<span data-ttu-id="c488f-139">Se puede observar el problema al ejecutar dos consultas:</span><span class="sxs-lookup"><span data-stu-id="c488f-139">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

<span data-ttu-id="c488f-140">Con la configuración anterior, la primera consulta devuelve las 6 `Post`; sin embargo, la segunda consulta solo devuelve 3.</span><span class="sxs-lookup"><span data-stu-id="c488f-140">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="c488f-141">Esta falta de coincidencia se produce porque el método `Include` de la segunda consulta carga las entidades `Blog` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="c488f-141">This mismatch happens because `Include` method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="c488f-142">Dado que se requiere la navegación entre `Blog` y `Post`, EF Core utiliza `INNER JOIN` al construir la consulta:</span><span class="sxs-lookup"><span data-stu-id="c488f-142">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="c488f-143">El uso de `INNER JOIN` filtra todos los valores `Post` cuyos `Blog` relacionados se han quitado mediante un filtro de consulta global.</span><span class="sxs-lookup"><span data-stu-id="c488f-143">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="c488f-144">Se puede solucionar mediante el uso de la navegación opcional, en lugar de la necesaria.</span><span class="sxs-lookup"><span data-stu-id="c488f-144">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="c488f-145">De este modo, la primera consulta se mantiene igual que antes, pero la segunda consulta generará `LEFT JOIN` y devolverá 6 resultados.</span><span class="sxs-lookup"><span data-stu-id="c488f-145">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

<span data-ttu-id="c488f-146">Un enfoque alternativo consiste en especificar filtros coherentes en las entidades `Blog` y `Post`.</span><span class="sxs-lookup"><span data-stu-id="c488f-146">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="c488f-147">De este modo, los filtros coincidentes se aplican tanto a `Blog` como a `Post`.</span><span class="sxs-lookup"><span data-stu-id="c488f-147">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="c488f-148">Las `Post` que podrían acabar en un estado inesperado se quitan y ambas consultas devuelven 3 resultados.</span><span class="sxs-lookup"><span data-stu-id="c488f-148">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a><span data-ttu-id="c488f-149">Deshabilitación de filtros</span><span class="sxs-lookup"><span data-stu-id="c488f-149">Disabling Filters</span></span>

<span data-ttu-id="c488f-150">Los filtros se pueden deshabilitar para consultas LINQ individuales mediante el operador <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A>.</span><span class="sxs-lookup"><span data-stu-id="c488f-150">Filters may be disabled for individual LINQ queries by using the <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> operator.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="c488f-151">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="c488f-151">Limitations</span></span>

<span data-ttu-id="c488f-152">Los filtros de consulta global tienen las limitaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="c488f-152">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="c488f-153">Solo se pueden definir filtros para el tipo de entidad raíz de una jerarquía de herencia.</span><span class="sxs-lookup"><span data-stu-id="c488f-153">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
