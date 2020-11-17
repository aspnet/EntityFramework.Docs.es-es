---
title: 'Cambios importantes en EF Core 5.0: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 5.0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e2537dbc1d5dba48450bd0fea7712054ba2fa622
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503181"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="093ca-103">Cambios importantes en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="093ca-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="093ca-104">Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="093ca-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="093ca-105">Resumen</span><span class="sxs-lookup"><span data-stu-id="093ca-105">Summary</span></span>

| <span data-ttu-id="093ca-106">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="093ca-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="093ca-107">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="093ca-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| <span data-ttu-id="093ca-108">[El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).</span><span class="sxs-lookup"><span data-stu-id="093ca-108">[Required on the navigation from principal to dependent has different semantics](#required-dependent)</span></span>                                 | <span data-ttu-id="093ca-109">Media</span><span class="sxs-lookup"><span data-stu-id="093ca-109">Medium</span></span>     |
| [<span data-ttu-id="093ca-110">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="093ca-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="093ca-111">Media</span><span class="sxs-lookup"><span data-stu-id="093ca-111">Medium</span></span>     |
| [<span data-ttu-id="093ca-112">Las consultas no sobrescriben las navegaciones de referencia no nula</span><span class="sxs-lookup"><span data-stu-id="093ca-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="093ca-113">Media</span><span class="sxs-lookup"><span data-stu-id="093ca-113">Medium</span></span>     |
| [<span data-ttu-id="093ca-114">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="093ca-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="093ca-115">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-115">Low</span></span>        |
| [<span data-ttu-id="093ca-116">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="093ca-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="093ca-117">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-117">Low</span></span>        |
| [<span data-ttu-id="093ca-118">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="093ca-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="093ca-119">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-119">Low</span></span>        |
| <span data-ttu-id="093ca-120">[Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="093ca-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="093ca-121">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-121">Low</span></span>        |
| [<span data-ttu-id="093ca-122">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="093ca-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="093ca-123">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-123">Low</span></span>        |
| [<span data-ttu-id="093ca-124">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="093ca-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="093ca-125">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-125">Low</span></span>        |
| <span data-ttu-id="093ca-126">[IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).</span><span class="sxs-lookup"><span data-stu-id="093ca-126">[IMigrationsModelDiffer now uses IRelationalModel](#relational-model)</span></span>                                                                 | <span data-ttu-id="093ca-127">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-127">Low</span></span>        |
| <span data-ttu-id="093ca-128">[Los discriminadores son de solo lectura](#read-only-discriminators).</span><span class="sxs-lookup"><span data-stu-id="093ca-128">[Discriminators are read-only](#read-only-discriminators)</span></span>                                                                             | <span data-ttu-id="093ca-129">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-129">Low</span></span>        |
| [<span data-ttu-id="093ca-130">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory</span><span class="sxs-lookup"><span data-stu-id="093ca-130">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="093ca-131">Baja</span><span class="sxs-lookup"><span data-stu-id="093ca-131">Low</span></span>        |
| [<span data-ttu-id="093ca-132">IndexBuilder.HasName ahora está obsoleto</span><span class="sxs-lookup"><span data-stu-id="093ca-132">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="093ca-133">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-133">Low</span></span>        |
| [<span data-ttu-id="093ca-134">Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="093ca-134">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="093ca-135">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-135">Low</span></span>        |
| [<span data-ttu-id="093ca-136">INavigationBase reemplaza a INavigation en algunas API para admitir la omisión de navegaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-136">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="093ca-137">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-137">Low</span></span>        |
| [<span data-ttu-id="093ca-138">Ya no se admiten algunas consultas con colecciones correlacionadas que también usan `Distinct` o `GroupBy`</span><span class="sxs-lookup"><span data-stu-id="093ca-138">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="093ca-139">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-139">Low</span></span>        |
| [<span data-ttu-id="093ca-140">No se admite el uso de una colección de tipo consultable en la proyección</span><span class="sxs-lookup"><span data-stu-id="093ca-140">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="093ca-141">Bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-141">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="093ca-142">Cambios de impacto medio</span><span class="sxs-lookup"><span data-stu-id="093ca-142">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="093ca-143">El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.</span><span class="sxs-lookup"><span data-stu-id="093ca-143">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="093ca-144">Incidencia de seguimiento n.º 17286</span><span class="sxs-lookup"><span data-stu-id="093ca-144">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-145">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-145">Old behavior</span></span>

<span data-ttu-id="093ca-146">Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias.</span><span class="sxs-lookup"><span data-stu-id="093ca-146">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="093ca-147">Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.</span><span class="sxs-lookup"><span data-stu-id="093ca-147">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-148">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-148">New behavior</span></span>

<span data-ttu-id="093ca-149">Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como obligatoria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.</span><span class="sxs-lookup"><span data-stu-id="093ca-149">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="093ca-150">Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:</span><span class="sxs-lookup"><span data-stu-id="093ca-150">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="093ca-151">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-151">Why</span></span>

<span data-ttu-id="093ca-152">El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="093ca-152">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-153">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-153">Mitigations</span></span>

<span data-ttu-id="093ca-154">Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="093ca-154">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="093ca-155">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="093ca-155">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="093ca-156">Problema de seguimiento n.º 18903</span><span class="sxs-lookup"><span data-stu-id="093ca-156">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-157">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-157">Old behavior</span></span>

<span data-ttu-id="093ca-158">Los tipos de entidad se asignaban en la definición de consultas en el nivel básico.</span><span class="sxs-lookup"><span data-stu-id="093ca-158">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="093ca-159">Cuando el tipo de entidad se usaba en la raíz de la consulta del tipo de entidad se sustituía por la consulta de definición de cualquier proveedor.</span><span class="sxs-lookup"><span data-stu-id="093ca-159">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-160">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-160">New behavior</span></span>

<span data-ttu-id="093ca-161">Las API de definición de consulta han quedado en desuso.</span><span class="sxs-lookup"><span data-stu-id="093ca-161">APIs for defining query are deprecated.</span></span> <span data-ttu-id="093ca-162">Se introdujeron nuevas API específicas del proveedor.</span><span class="sxs-lookup"><span data-stu-id="093ca-162">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-163">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-163">Why</span></span>

<span data-ttu-id="093ca-164">Aunque la definición de consulta se implementaba como consulta de reemplazo siempre que se usaba la raíz de la consulta en la consulta, tenía algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="093ca-164">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="093ca-165">Si la definición de consulta establece la proyección del tipo de entidad con `new { ... }` en el método `Select`, su identificación como entidad requería trabajo adicional y lo hacía incoherente con el modo en que EF Core trata los tipos nominales en la consulta.</span><span class="sxs-lookup"><span data-stu-id="093ca-165">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="093ca-166">En el caso de los proveedores relacionales `FromSql` todavía hay que pasar la cadena SQL en forma de expresión LINQ.</span><span class="sxs-lookup"><span data-stu-id="093ca-166">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="093ca-167">Las definiciones de consulta se introdujeron inicialmente como vistas del lado cliente para su uso con el proveedor en memoria para las entidades sin clave (similares a las vistas de base de datos de las bases de datos relacionales).</span><span class="sxs-lookup"><span data-stu-id="093ca-167">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="093ca-168">Esta definición facilita la prueba de la aplicación en la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="093ca-168">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="093ca-169">Después, se convirtieron en ampliamente aplicables, lo que resultaba útil pero no era coherente y resultaba difícil de entender.</span><span class="sxs-lookup"><span data-stu-id="093ca-169">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="093ca-170">Por tanto, decidimos simplificar el concepto.</span><span class="sxs-lookup"><span data-stu-id="093ca-170">So we decided to simplify the concept.</span></span> <span data-ttu-id="093ca-171">Hemos creado una consulta de definición basada en LINQ exclusiva para el proveedor en memoria, que tratamos de otra forma.</span><span class="sxs-lookup"><span data-stu-id="093ca-171">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="093ca-172">Para más información, consulte [este problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="093ca-172">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-173">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-173">Mitigations</span></span>

<span data-ttu-id="093ca-174">En el caso de los proveedores relacionales, use el método `ToSqlQuery` en `OnModelCreating` y pase una cadena SQL que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="093ca-174">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="093ca-175">En el caso del proveedor en memoria, use el método `ToInMemoryQuery` en `OnModelCreating` y pase una consulta LINQ que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="093ca-175">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="093ca-176">Las consultas no sobrescriben las navegaciones de referencia no nula</span><span class="sxs-lookup"><span data-stu-id="093ca-176">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="093ca-177">Incidencia de seguimiento n.º 2693</span><span class="sxs-lookup"><span data-stu-id="093ca-177">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-178">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-178">Old behavior</span></span>

<span data-ttu-id="093ca-179">En EF Core 3.1, las instancias de la entidad de la base de datos sobrescribirán las navegaciones de referencia que se inicializan de manera diligente en valores que no sean NULL, con independencia de si coinciden o no los valores de claves.</span><span class="sxs-lookup"><span data-stu-id="093ca-179">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="093ca-180">Sin embargo, en otros casos, EF Core 3.1 haría lo contrario y dejaría el valor no NULL existente.</span><span class="sxs-lookup"><span data-stu-id="093ca-180">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-181">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-181">New behavior</span></span>

<span data-ttu-id="093ca-182">A partir de EF Core 5.0, las instancias devueltas por una consulta nunca sobrescriben las navegaciones de referencia no NULL.</span><span class="sxs-lookup"><span data-stu-id="093ca-182">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="093ca-183">Tenga en cuenta que todavía se admite la inicialización diligente de una navegación de _colección_ en una colección vacía.</span><span class="sxs-lookup"><span data-stu-id="093ca-183">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-184">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-184">Why</span></span>

<span data-ttu-id="093ca-185">La inicialización de una propiedad de navegación de referencia en una instancia de entidad "vacía" da como resultado un estado ambiguo.</span><span class="sxs-lookup"><span data-stu-id="093ca-185">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="093ca-186">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="093ca-186">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="093ca-187">Normalmente, una consulta de blogs y autores primero creará instancias `Blog` y, después, establecerá las instancias `Author` apropiadas en función de los datos devueltos por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="093ca-187">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="093ca-188">Sin embargo, en este caso, cada propiedad `Blog.Author` ya se ha inicializado en una instancia `Author` vacía.</span><span class="sxs-lookup"><span data-stu-id="093ca-188">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="093ca-189">Excepto EF Core, no tiene ninguna manera de saber que esta instancia está "vacía".</span><span class="sxs-lookup"><span data-stu-id="093ca-189">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="093ca-190">Por lo tanto, si se sobrescribe esta instancia, es posible que se elimine de forma silenciosa una instancia `Author` válida.</span><span class="sxs-lookup"><span data-stu-id="093ca-190">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="093ca-191">Por lo tanto, EF Core 5.0 ahora no sobrescribe de forma coherente una navegación que ya está inicializada.</span><span class="sxs-lookup"><span data-stu-id="093ca-191">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="093ca-192">Este nuevo comportamiento también se alinea con el comportamiento de EF6 en la mayoría de los casos, aunque en la investigación también encontramos algunos casos de incoherencia en EF6.</span><span class="sxs-lookup"><span data-stu-id="093ca-192">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="093ca-193">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-193">Mitigations</span></span>

<span data-ttu-id="093ca-194">Si se detecta esta interrupción, la solución consiste en detener la inicialización diligente de las propiedades de navegación de referencia.</span><span class="sxs-lookup"><span data-stu-id="093ca-194">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="093ca-195">Cambios de impacto bajo</span><span class="sxs-lookup"><span data-stu-id="093ca-195">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="093ca-196">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="093ca-196">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="093ca-197">Problema de seguimiento n.º 14257</span><span class="sxs-lookup"><span data-stu-id="093ca-197">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-198">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-198">Old behavior</span></span>

<span data-ttu-id="093ca-199">Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría.</span><span class="sxs-lookup"><span data-stu-id="093ca-199">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="093ca-200">Sin embargo, solo ha afectado a la creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="093ca-200">However, it only ever affected database creation.</span></span> <span data-ttu-id="093ca-201">No era necesario especificarlo para consultar los valores con dimensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="093ca-201">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="093ca-202">Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="093ca-202">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-203">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-203">New behavior</span></span>

<span data-ttu-id="093ca-204">Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="093ca-204">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="093ca-205">Esta API coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="093ca-205">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-206">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-206">Why</span></span>

<span data-ttu-id="093ca-207">El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.</span><span class="sxs-lookup"><span data-stu-id="093ca-207">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-208">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-208">Mitigations</span></span>

<span data-ttu-id="093ca-209">Use `HasColumnType` para especificar la dimensión:</span><span class="sxs-lookup"><span data-stu-id="093ca-209">Use `HasColumnType` to specify the dimension:</span></span>

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="093ca-210">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="093ca-210">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="093ca-211">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="093ca-211">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-212">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-212">Old behavior</span></span>

<span data-ttu-id="093ca-213">La propiedad de clave de partición solo se agregaba a la clave alternativa que incluye `id`.</span><span class="sxs-lookup"><span data-stu-id="093ca-213">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-214">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-214">New behavior</span></span>

<span data-ttu-id="093ca-215">La propiedad de clave de partición ahora también se agrega por convención a la clave principal.</span><span class="sxs-lookup"><span data-stu-id="093ca-215">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-216">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-216">Why</span></span>

<span data-ttu-id="093ca-217">Este cambio hace que el modelo se alinee mejor con la semántica de Azure Cosmos DB y mejora el rendimiento de `Find` y algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="093ca-217">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-218">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-218">Mitigations</span></span>

<span data-ttu-id="093ca-219">Para evitar que la propiedad de clave de partición se agregue a la clave principal, configúrela en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="093ca-219">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="093ca-220">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="093ca-220">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="093ca-221">Problema de seguimiento n.º 17751</span><span class="sxs-lookup"><span data-stu-id="093ca-221">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-222">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-222">Old behavior</span></span>

<span data-ttu-id="093ca-223">La propiedad Shadow asignada a la propiedad `id` de JSON también se llama `id`.</span><span class="sxs-lookup"><span data-stu-id="093ca-223">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-224">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-224">New behavior</span></span>

<span data-ttu-id="093ca-225">La propiedad Shadow creada por convención se denomina ahora `__id`.</span><span class="sxs-lookup"><span data-stu-id="093ca-225">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-226">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-226">Why</span></span>

<span data-ttu-id="093ca-227">Este cambio hace menos probable que la propiedad `id` entre en conflicto con una propiedad existente en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="093ca-227">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-228">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-228">Mitigations</span></span>

<span data-ttu-id="093ca-229">Para volver al comportamiento de la versión 3.x, configure la propiedad `id` en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="093ca-229">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="093ca-230">Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números</span><span class="sxs-lookup"><span data-stu-id="093ca-230">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="093ca-231">Problema de seguimiento n.º 17306</span><span class="sxs-lookup"><span data-stu-id="093ca-231">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-232">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-232">Old behavior</span></span>

<span data-ttu-id="093ca-233">Las propiedades de tipo byte[] se almacenaban como matriz de números.</span><span class="sxs-lookup"><span data-stu-id="093ca-233">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-234">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-234">New behavior</span></span>

<span data-ttu-id="093ca-235">Las propiedades de tipo byte[] se almacenan ahora como cadena Base64.</span><span class="sxs-lookup"><span data-stu-id="093ca-235">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-236">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-236">Why</span></span>

<span data-ttu-id="093ca-237">Esta representación de byte [] se alinea mejor con las expectativas y es el comportamiento predeterminado de las principales bibliotecas de serialización de JSON.</span><span class="sxs-lookup"><span data-stu-id="093ca-237">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-238">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-238">Mitigations</span></span>

<span data-ttu-id="093ca-239">Los datos existentes almacenados como matrices de números se seguirán consultando correctamente, pero actualmente no hay ninguna manera compatible de volver a cambiar el comportamiento de inserción.</span><span class="sxs-lookup"><span data-stu-id="093ca-239">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="093ca-240">Si esta limitación bloquea su escenario, haga un comentario en [este problema](https://github.com/dotnet/efcore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="093ca-240">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="093ca-241">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="093ca-241">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="093ca-242">Problema de seguimiento n.º 17874</span><span class="sxs-lookup"><span data-stu-id="093ca-242">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-243">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-243">Old behavior</span></span>

<span data-ttu-id="093ca-244">Anteriormente, se llamaba a los métodos de extensión `GetPropertyName` y `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="093ca-244">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-245">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-245">New behavior</span></span>

<span data-ttu-id="093ca-246">La API antigua se ha quitado y se han agregado métodos nuevos: `GetJsonPropertyName` y `SetJsonPropertyName`.</span><span class="sxs-lookup"><span data-stu-id="093ca-246">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-247">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-247">Why</span></span>

<span data-ttu-id="093ca-248">Este cambio elimina la ambigüedad en torno a lo que estos métodos configuran.</span><span class="sxs-lookup"><span data-stu-id="093ca-248">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-249">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-249">Mitigations</span></span>

<span data-ttu-id="093ca-250">Use la API nueva.</span><span class="sxs-lookup"><span data-stu-id="093ca-250">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="093ca-251">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="093ca-251">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="093ca-252">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="093ca-252">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-253">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-253">Old behavior</span></span>

<span data-ttu-id="093ca-254">Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.</span><span class="sxs-lookup"><span data-stu-id="093ca-254">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-255">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-255">New behavior</span></span>

<span data-ttu-id="093ca-256">Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="093ca-256">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-257">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-257">Why</span></span>

<span data-ttu-id="093ca-258">Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.</span><span class="sxs-lookup"><span data-stu-id="093ca-258">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-259">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-259">Mitigations</span></span>

<span data-ttu-id="093ca-260">Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.</span><span class="sxs-lookup"><span data-stu-id="093ca-260">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="093ca-261">IMigrationsModelDiffer ahora usa IRelationalModel.</span><span class="sxs-lookup"><span data-stu-id="093ca-261">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="093ca-262">Incidencia de seguimiento n.º 20305</span><span class="sxs-lookup"><span data-stu-id="093ca-262">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-263">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-263">Old behavior</span></span>

<span data-ttu-id="093ca-264">`IMigrationsModelDiffer` API se definía mediante `IModel`.</span><span class="sxs-lookup"><span data-stu-id="093ca-264">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-265">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-265">New behavior</span></span>

<span data-ttu-id="093ca-266">`IMigrationsModelDiffer` API ahora usa `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="093ca-266">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="093ca-267">Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.</span><span class="sxs-lookup"><span data-stu-id="093ca-267">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-268">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-268">Why</span></span>

<span data-ttu-id="093ca-269">`IRelationalModel` es una representación recién agregada del esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="093ca-269">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="093ca-270">Su uso para encontrar diferencias es más rápido y preciso.</span><span class="sxs-lookup"><span data-stu-id="093ca-270">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-271">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-271">Mitigations</span></span>

<span data-ttu-id="093ca-272">Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:</span><span class="sxs-lookup"><span data-stu-id="093ca-272">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

<span data-ttu-id="093ca-273">Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="093ca-273">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="093ca-274">Los discriminadores son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="093ca-274">Discriminators are read-only</span></span>

[<span data-ttu-id="093ca-275">Incidencia de seguimiento n.º 21154</span><span class="sxs-lookup"><span data-stu-id="093ca-275">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-276">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-276">Old behavior</span></span>

<span data-ttu-id="093ca-277">Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="093ca-277">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-278">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-278">New behavior</span></span>

<span data-ttu-id="093ca-279">En el caso anterior, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="093ca-279">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-280">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-280">Why</span></span>

<span data-ttu-id="093ca-281">EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.</span><span class="sxs-lookup"><span data-stu-id="093ca-281">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-282">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-282">Mitigations</span></span>

<span data-ttu-id="093ca-283">Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:</span><span class="sxs-lookup"><span data-stu-id="093ca-283">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="093ca-284">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="093ca-284">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="093ca-285">Incidencia de seguimiento n.º 20294</span><span class="sxs-lookup"><span data-stu-id="093ca-285">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-286">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-286">Old behavior</span></span>

<span data-ttu-id="093ca-287">Los métodos EF.Functions específicos del proveedor incluían la implementación para la ejecución del cliente, lo cual les permitía ejecutarse en el proveedor de InMemory.</span><span class="sxs-lookup"><span data-stu-id="093ca-287">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="093ca-288">Por ejemplo, `EF.Functions.DateDiffDay` es un método específico de SQL Server que funcionaba en el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="093ca-288">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-289">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-289">New behavior</span></span>

<span data-ttu-id="093ca-290">Los métodos específicos del proveedor se han actualizado para producir una excepción en el cuerpo del método a fin de bloquear su evaluación en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="093ca-290">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-291">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-291">Why</span></span>

<span data-ttu-id="093ca-292">Los métodos específicos del proveedor se asignan a una función de base de datos.</span><span class="sxs-lookup"><span data-stu-id="093ca-292">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="093ca-293">El cálculo realizado por la función de base de datos asignada no siempre se puede replicar en el lado cliente en LINQ.</span><span class="sxs-lookup"><span data-stu-id="093ca-293">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="093ca-294">Esto puede provocar que el resultado del servidor sea diferente al ejecutar el mismo método en el cliente.</span><span class="sxs-lookup"><span data-stu-id="093ca-294">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="093ca-295">Dado que estos métodos se usan en LINQ para traducir a funciones específicas de base de datos, no es necesario que se evalúen en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="093ca-295">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="093ca-296">Dado que el proveedor InMemory es una base de datos *diferente*, estos métodos no están disponibles para este proveedor.</span><span class="sxs-lookup"><span data-stu-id="093ca-296">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="093ca-297">Al intentar ejecutarlos para el proveedor InMemory o cualquier otro proveedor que no traduzca estos métodos, se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="093ca-297">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-298">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-298">Mitigations</span></span>

<span data-ttu-id="093ca-299">Dado que no hay forma de imitar el comportamiento de las funciones de base de datos con precisión, debe probar las consultas que las contienen en el mismo tipo de base de datos que en producción.</span><span class="sxs-lookup"><span data-stu-id="093ca-299">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="093ca-300">IndexBuilder.HasName ahora está obsoleto</span><span class="sxs-lookup"><span data-stu-id="093ca-300">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="093ca-301">Incidencia de seguimiento n.º 21089</span><span class="sxs-lookup"><span data-stu-id="093ca-301">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-302">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-302">Old behavior</span></span>

<span data-ttu-id="093ca-303">Anteriormente, solo se podía definir un índice en un conjunto determinado de propiedades.</span><span class="sxs-lookup"><span data-stu-id="093ca-303">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="093ca-304">El nombre de la base de datos de un índice se configuró mediante IndexBuilder.HasName.</span><span class="sxs-lookup"><span data-stu-id="093ca-304">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-305">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-305">New behavior</span></span>

<span data-ttu-id="093ca-306">Ahora se permiten varios índices en el mismo conjunto o las mismas propiedades.</span><span class="sxs-lookup"><span data-stu-id="093ca-306">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="093ca-307">Ahora, estos índices se distinguen por un nombre en el modelo.</span><span class="sxs-lookup"><span data-stu-id="093ca-307">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="093ca-308">Por convención, el nombre del modelo se utiliza como nombre de la base de datos; sin embargo, también se puede configurar de forma independiente utilizando HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="093ca-308">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-309">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-309">Why</span></span>

<span data-ttu-id="093ca-310">En el futuro, nos gustaría habilitar índices ascendentes y descendentes o índices con distintas intercalaciones en el mismo conjunto de propiedades.</span><span class="sxs-lookup"><span data-stu-id="093ca-310">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="093ca-311">Este cambio nos hace dar otro paso en esa dirección.</span><span class="sxs-lookup"><span data-stu-id="093ca-311">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-312">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-312">Mitigations</span></span>

<span data-ttu-id="093ca-313">Cualquier código que llamara anteriormente a IndexBuilder.HasName debe actualizarse para llamar a HasDatabaseName en su lugar.</span><span class="sxs-lookup"><span data-stu-id="093ca-313">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="093ca-314">Si su proyecto incluye migraciones generadas antes de la versión 2.0.0 de EF Core, puede ignorar la advertencia en esos archivos de forma segura y suprimirla agregando `#pragma warning disable 612, 618`.</span><span class="sxs-lookup"><span data-stu-id="093ca-314">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="093ca-315">Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="093ca-315">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="093ca-316">Incidencia de seguimiento n.º 11160</span><span class="sxs-lookup"><span data-stu-id="093ca-316">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-317">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-317">Old behavior</span></span>

<span data-ttu-id="093ca-318">Anteriormente, tenía que instalar un paquete de pluralizador independiente para pluralizar los nombres de navegación de colección y DbSet y singularizar los nombres de tabla al aplicar scaffolding a DbContext y los tipos de entidad mediante la utilización de técnicas de ingeniería inversa en un esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="093ca-318">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-319">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-319">New behavior</span></span>

<span data-ttu-id="093ca-320">EF Core incluye ahora un pluralizador que usa la biblioteca [Humanizer](https://github.com/Humanizr/Humanizer).</span><span class="sxs-lookup"><span data-stu-id="093ca-320">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="093ca-321">Se trata de la misma biblioteca que Visual Studio usa para recomendar nombres de variable.</span><span class="sxs-lookup"><span data-stu-id="093ca-321">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-322">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-322">Why</span></span>

<span data-ttu-id="093ca-323">El uso de formas plurales de palabras para las propiedades de colección y de formas singulares para los tipos y las propiedades de referencia es idiomático en .NET.</span><span class="sxs-lookup"><span data-stu-id="093ca-323">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-324">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-324">Mitigations</span></span>

<span data-ttu-id="093ca-325">Para deshabilitar el pluralizador, use la opción `--no-pluralize` en `dotnet ef dbcontext scaffold` o el modificador `-NoPluralize` en `Scaffold-DbContext`.</span><span class="sxs-lookup"><span data-stu-id="093ca-325">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="093ca-326">INavigationBase reemplaza a INavigation en algunas API para admitir la omisión de navegaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-326">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="093ca-327">Incidencia de seguimiento n.º 2568</span><span class="sxs-lookup"><span data-stu-id="093ca-327">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="093ca-328">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="093ca-328">Old behavior</span></span>

<span data-ttu-id="093ca-329">Las versiones de EF Core anteriores a la 5.0 solo admiten una forma de propiedad de navegación, representada por la interfaz `INavigation`.</span><span class="sxs-lookup"><span data-stu-id="093ca-329">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="093ca-330">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="093ca-330">New behavior</span></span>

<span data-ttu-id="093ca-331">EF Core 5.0 presenta relaciones varios a varios que usan las "navegaciones por omisión".</span><span class="sxs-lookup"><span data-stu-id="093ca-331">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="093ca-332">Se representan mediante la interfaz `ISkipNavigation`, y la mayor parte de la funcionalidad de `INavigation` se ha insertado en una interfaz base común: `INavigationBase`.</span><span class="sxs-lookup"><span data-stu-id="093ca-332">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="093ca-333">Por qué</span><span class="sxs-lookup"><span data-stu-id="093ca-333">Why</span></span>

<span data-ttu-id="093ca-334">La mayor parte de la funcionalidad entre las navegaciones normal y por omisión es la misma.</span><span class="sxs-lookup"><span data-stu-id="093ca-334">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="093ca-335">Sin embargo, las navegaciones por omisión tienen una relación diferente con las claves externas con respecto a las navegaciones normales, ya que las claves externas implicadas no están directamente en ninguno de los extremos de la relación, sino en la entidad join.</span><span class="sxs-lookup"><span data-stu-id="093ca-335">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="093ca-336">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="093ca-336">Mitigations</span></span>

<span data-ttu-id="093ca-337">En muchos casos, las aplicaciones pueden cambiar al uso de la nueva interfaz base sin realizar ningún otro cambio.</span><span class="sxs-lookup"><span data-stu-id="093ca-337">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="093ca-338">Sin embargo, en los casos en los que se usa la navegación para acceder a las propiedades de clave externa, el código de aplicación se debe restringir solo a las navegaciones normales, o bien actualizarse para hacer lo adecuado para las navegaciones normal y por omisión.</span><span class="sxs-lookup"><span data-stu-id="093ca-338">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="093ca-339">Ya no se admiten algunas consultas con colecciones correlacionadas que también usan `Distinct` o `GroupBy`</span><span class="sxs-lookup"><span data-stu-id="093ca-339">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="093ca-340">Incidencia de seguimiento n.º 15873</span><span class="sxs-lookup"><span data-stu-id="093ca-340">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="093ca-341">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="093ca-341">**Old behavior**</span></span>

<span data-ttu-id="093ca-342">Anteriormente, las consultas que implicaban colecciones correlacionadas seguidas de `GroupBy`, así como algunas consultas con `Distinct` que permitimos ejecutar.</span><span class="sxs-lookup"><span data-stu-id="093ca-342">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="093ca-343">Ejemplo de GroupBy:</span><span class="sxs-lookup"><span data-stu-id="093ca-343">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="093ca-344">Ejemplo de `Distinct`: en concreto, las consultas `Distinct` en las que la proyección de la colección interna no contiene la clave principal:</span><span class="sxs-lookup"><span data-stu-id="093ca-344">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="093ca-345">Estas consultas podrían devolver resultados incorrectos si la colección interna contuviera duplicados, pero funcionaría correctamente si todos los elementos de la colección interna fueran únicos.</span><span class="sxs-lookup"><span data-stu-id="093ca-345">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="093ca-346">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="093ca-346">**New behavior**</span></span>

<span data-ttu-id="093ca-347">Estas consultas ya no son compatibles.</span><span class="sxs-lookup"><span data-stu-id="093ca-347">These queries are no longer supported.</span></span> <span data-ttu-id="093ca-348">Se produce una excepción que indica que no hay suficiente información para compilar los resultados correctamente.</span><span class="sxs-lookup"><span data-stu-id="093ca-348">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="093ca-349">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="093ca-349">**Why**</span></span>

<span data-ttu-id="093ca-350">En el caso de los escenarios de colecciones correlacionadas, es necesario conocer la clave principal de la entidad para asignar entidades de colección al elemento primario correcto.</span><span class="sxs-lookup"><span data-stu-id="093ca-350">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="093ca-351">Cuando la colección interna no utiliza `GroupBy` ni `Distinct`, la clave principal que falta se puede agregar simplemente a la proyección.</span><span class="sxs-lookup"><span data-stu-id="093ca-351">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="093ca-352">Sin embargo, en el caso de `GroupBy` y `Distinct`, no se puede hacer porque cambiaría el resultado de la operación `GroupBy` o `Distinct`.</span><span class="sxs-lookup"><span data-stu-id="093ca-352">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="093ca-353">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="093ca-353">**Mitigations**</span></span>

<span data-ttu-id="093ca-354">Vuelva a escribir la consulta para que no use las operaciones `GroupBy` o `Distinct` en la colección interna y, en su lugar, realice estas operaciones en el cliente.</span><span class="sxs-lookup"><span data-stu-id="093ca-354">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="093ca-355">No se admite el uso de una colección de tipo consultable en la proyección</span><span class="sxs-lookup"><span data-stu-id="093ca-355">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="093ca-356">Incidencia de seguimiento n.º 16314</span><span class="sxs-lookup"><span data-stu-id="093ca-356">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="093ca-357">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="093ca-357">**Old behavior**</span></span>

<span data-ttu-id="093ca-358">Anteriormente, era posible usar la colección de un tipo consultable dentro de la proyección en algunos casos, por ejemplo, como un argumento de un constructor `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="093ca-358">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="093ca-359">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="093ca-359">**New behavior**</span></span>

<span data-ttu-id="093ca-360">Estas consultas ya no son compatibles.</span><span class="sxs-lookup"><span data-stu-id="093ca-360">These queries are no longer supported.</span></span> <span data-ttu-id="093ca-361">Se produce una excepción que indica que no se puede crear un objeto de tipo consultable y, además, se sugiere cómo corregir este problema.</span><span class="sxs-lookup"><span data-stu-id="093ca-361">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="093ca-362">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="093ca-362">**Why**</span></span>

<span data-ttu-id="093ca-363">No se puede materializar un objeto de un tipo consultable, por lo que, en su lugar, se creará automáticamente con el tipo `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="093ca-363">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="093ca-364">Esto suele provocar una excepción debido a una falta de coincidencia de tipos que no era muy clara y podría ser sorprendente para algunos usuarios.</span><span class="sxs-lookup"><span data-stu-id="093ca-364">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="093ca-365">Decidimos reconocer el patrón y producir una excepción más significativa.</span><span class="sxs-lookup"><span data-stu-id="093ca-365">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="093ca-366">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="093ca-366">**Mitigations**</span></span>

<span data-ttu-id="093ca-367">Agregue la llamada a `ToList()` después del objeto consultable en la proyección:</span><span class="sxs-lookup"><span data-stu-id="093ca-367">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
