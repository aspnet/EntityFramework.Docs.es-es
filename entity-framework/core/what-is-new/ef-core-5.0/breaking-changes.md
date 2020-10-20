---
title: 'Cambios importantes en EF Core 5.0: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 5.0
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065646"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="06e3c-103">Cambios importantes en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="06e3c-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="06e3c-104">Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="06e3c-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="06e3c-105">Resumen</span><span class="sxs-lookup"><span data-stu-id="06e3c-105">Summary</span></span>

| <span data-ttu-id="06e3c-106">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="06e3c-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="06e3c-107">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="06e3c-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| <span data-ttu-id="06e3c-108">[El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).</span><span class="sxs-lookup"><span data-stu-id="06e3c-108">[Required on the navigation from principal to dependent has different semantics](#required-dependent)</span></span>                                 | <span data-ttu-id="06e3c-109">Media</span><span class="sxs-lookup"><span data-stu-id="06e3c-109">Medium</span></span>     |
| [<span data-ttu-id="06e3c-110">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="06e3c-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="06e3c-111">Media</span><span class="sxs-lookup"><span data-stu-id="06e3c-111">Medium</span></span>     |
| [<span data-ttu-id="06e3c-112">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="06e3c-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="06e3c-113">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-113">Low</span></span>        |
| [<span data-ttu-id="06e3c-114">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="06e3c-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="06e3c-115">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-115">Low</span></span>        |
| [<span data-ttu-id="06e3c-116">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="06e3c-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="06e3c-117">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-117">Low</span></span>        |
| <span data-ttu-id="06e3c-118">[Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="06e3c-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="06e3c-119">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-119">Low</span></span>        |
| [<span data-ttu-id="06e3c-120">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="06e3c-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="06e3c-121">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-121">Low</span></span>        |
| [<span data-ttu-id="06e3c-122">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="06e3c-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="06e3c-123">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-123">Low</span></span>        |
| <span data-ttu-id="06e3c-124">[IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).</span><span class="sxs-lookup"><span data-stu-id="06e3c-124">[IMigrationsModelDiffer now uses IRelationalModel](#relational-model)</span></span>                                                                 | <span data-ttu-id="06e3c-125">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-125">Low</span></span>        |
| <span data-ttu-id="06e3c-126">[Los discriminadores son de solo lectura](#read-only-discriminators).</span><span class="sxs-lookup"><span data-stu-id="06e3c-126">[Discriminators are read-only](#read-only-discriminators)</span></span>                                                                             | <span data-ttu-id="06e3c-127">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-127">Low</span></span>        |
| [<span data-ttu-id="06e3c-128">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory</span><span class="sxs-lookup"><span data-stu-id="06e3c-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="06e3c-129">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-129">Low</span></span>        |
| [<span data-ttu-id="06e3c-130">IndexBuilder.HasName ahora está obsoleto</span><span class="sxs-lookup"><span data-stu-id="06e3c-130">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="06e3c-131">Baja</span><span class="sxs-lookup"><span data-stu-id="06e3c-131">Low</span></span>        |
| [<span data-ttu-id="06e3c-132">Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="06e3c-132">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="06e3c-133">Bajo</span><span class="sxs-lookup"><span data-stu-id="06e3c-133">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="06e3c-134">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="06e3c-134">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="06e3c-135">Problema de seguimiento n.º 14257</span><span class="sxs-lookup"><span data-stu-id="06e3c-135">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="06e3c-136">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-136">**Old behavior**</span></span>

<span data-ttu-id="06e3c-137">Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría.</span><span class="sxs-lookup"><span data-stu-id="06e3c-137">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="06e3c-138">Sin embargo, solo ha afectado a la creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="06e3c-138">However, it only ever affected database creation.</span></span> <span data-ttu-id="06e3c-139">No era necesario especificarlo para consultar los valores con dimensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="06e3c-139">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="06e3c-140">Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="06e3c-140">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="06e3c-141">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-141">**New behavior**</span></span>

<span data-ttu-id="06e3c-142">Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="06e3c-142">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="06e3c-143">Esta API coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="06e3c-143">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="06e3c-144">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-144">**Why**</span></span>

<span data-ttu-id="06e3c-145">El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.</span><span class="sxs-lookup"><span data-stu-id="06e3c-145">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="06e3c-146">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-146">**Mitigations**</span></span>

<span data-ttu-id="06e3c-147">Use `HasColumnType` para especificar la dimensión:</span><span class="sxs-lookup"><span data-stu-id="06e3c-147">Use `HasColumnType` to specify the dimension:</span></span>

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

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="06e3c-148">El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.</span><span class="sxs-lookup"><span data-stu-id="06e3c-148">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="06e3c-149">Incidencia de seguimiento n.º 17286</span><span class="sxs-lookup"><span data-stu-id="06e3c-149">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="06e3c-150">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-150">**Old behavior**</span></span>

<span data-ttu-id="06e3c-151">Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias.</span><span class="sxs-lookup"><span data-stu-id="06e3c-151">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="06e3c-152">Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.</span><span class="sxs-lookup"><span data-stu-id="06e3c-152">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="06e3c-153">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-153">**New behavior**</span></span>

<span data-ttu-id="06e3c-154">Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como obligatoria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.</span><span class="sxs-lookup"><span data-stu-id="06e3c-154">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="06e3c-155">Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:</span><span class="sxs-lookup"><span data-stu-id="06e3c-155">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="06e3c-156">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-156">**Why**</span></span>

<span data-ttu-id="06e3c-157">El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="06e3c-157">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="06e3c-158">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-158">**Mitigations**</span></span>

<span data-ttu-id="06e3c-159">Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="06e3c-159">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="06e3c-160">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="06e3c-160">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="06e3c-161">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="06e3c-161">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="06e3c-162">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-162">**Old behavior**</span></span>

<span data-ttu-id="06e3c-163">La propiedad de clave de partición solo se agregaba a la clave alternativa que incluye `id`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-163">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="06e3c-164">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-164">**New behavior**</span></span>

<span data-ttu-id="06e3c-165">La propiedad de clave de partición ahora también se agrega por convención a la clave principal.</span><span class="sxs-lookup"><span data-stu-id="06e3c-165">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="06e3c-166">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-166">**Why**</span></span>

<span data-ttu-id="06e3c-167">Este cambio hace que el modelo se alinee mejor con la semántica de Azure Cosmos DB y mejora el rendimiento de `Find` y algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="06e3c-167">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="06e3c-168">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-168">**Mitigations**</span></span>

<span data-ttu-id="06e3c-169">Para evitar que la propiedad de clave de partición se agregue a la clave principal, configúrela en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-169">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="06e3c-170">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="06e3c-170">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="06e3c-171">Problema de seguimiento n.º 17751</span><span class="sxs-lookup"><span data-stu-id="06e3c-171">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="06e3c-172">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-172">**Old behavior**</span></span>

<span data-ttu-id="06e3c-173">La propiedad Shadow asignada a la propiedad `id` de JSON también se llama `id`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-173">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="06e3c-174">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-174">**New behavior**</span></span>

<span data-ttu-id="06e3c-175">La propiedad Shadow creada por convención se denomina ahora `__id`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-175">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="06e3c-176">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-176">**Why**</span></span>

<span data-ttu-id="06e3c-177">Este cambio hace menos probable que la propiedad `id` entre en conflicto con una propiedad existente en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="06e3c-177">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="06e3c-178">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-178">**Mitigations**</span></span>

<span data-ttu-id="06e3c-179">Para volver al comportamiento de la versión 3.x, configure la propiedad `id` en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-179">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="06e3c-180">Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números</span><span class="sxs-lookup"><span data-stu-id="06e3c-180">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="06e3c-181">Problema de seguimiento n.º 17306</span><span class="sxs-lookup"><span data-stu-id="06e3c-181">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="06e3c-182">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-182">**Old behavior**</span></span>

<span data-ttu-id="06e3c-183">Las propiedades de tipo byte[] se almacenaban como matriz de números.</span><span class="sxs-lookup"><span data-stu-id="06e3c-183">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="06e3c-184">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-184">**New behavior**</span></span>

<span data-ttu-id="06e3c-185">Las propiedades de tipo byte[] se almacenan ahora como cadena Base64.</span><span class="sxs-lookup"><span data-stu-id="06e3c-185">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="06e3c-186">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-186">**Why**</span></span>

<span data-ttu-id="06e3c-187">Esta representación de byte [] se alinea mejor con las expectativas y es el comportamiento predeterminado de las principales bibliotecas de serialización de JSON.</span><span class="sxs-lookup"><span data-stu-id="06e3c-187">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="06e3c-188">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-188">**Mitigations**</span></span>

<span data-ttu-id="06e3c-189">Los datos existentes almacenados como matrices de números se seguirán consultando correctamente, pero actualmente no hay ninguna manera compatible de volver a cambiar el comportamiento de inserción.</span><span class="sxs-lookup"><span data-stu-id="06e3c-189">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="06e3c-190">Si esta limitación bloquea su escenario, haga un comentario en [este problema](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="06e3c-190">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="06e3c-191">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="06e3c-191">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="06e3c-192">Problema de seguimiento n.º 17874</span><span class="sxs-lookup"><span data-stu-id="06e3c-192">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="06e3c-193">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-193">**Old behavior**</span></span>

<span data-ttu-id="06e3c-194">Anteriormente, se llamaba a los métodos de extensión `GetPropertyName` y `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="06e3c-194">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="06e3c-195">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-195">**New behavior**</span></span>

<span data-ttu-id="06e3c-196">La API antigua se ha quitado y se han agregado métodos nuevos: `GetJsonPropertyName` y `SetJsonPropertyName`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-196">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="06e3c-197">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-197">**Why**</span></span>

<span data-ttu-id="06e3c-198">Este cambio elimina la ambigüedad en torno a lo que estos métodos configuran.</span><span class="sxs-lookup"><span data-stu-id="06e3c-198">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="06e3c-199">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-199">**Mitigations**</span></span>

<span data-ttu-id="06e3c-200">Use la API nueva.</span><span class="sxs-lookup"><span data-stu-id="06e3c-200">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="06e3c-201">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="06e3c-201">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="06e3c-202">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="06e3c-202">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="06e3c-203">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-203">**Old behavior**</span></span>

<span data-ttu-id="06e3c-204">Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.</span><span class="sxs-lookup"><span data-stu-id="06e3c-204">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="06e3c-205">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-205">**New behavior**</span></span>

<span data-ttu-id="06e3c-206">Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="06e3c-206">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="06e3c-207">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-207">**Why**</span></span>

<span data-ttu-id="06e3c-208">Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.</span><span class="sxs-lookup"><span data-stu-id="06e3c-208">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="06e3c-209">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-209">**Mitigations**</span></span>

<span data-ttu-id="06e3c-210">Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.</span><span class="sxs-lookup"><span data-stu-id="06e3c-210">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="06e3c-211">IMigrationsModelDiffer ahora usa IRelationalModel.</span><span class="sxs-lookup"><span data-stu-id="06e3c-211">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="06e3c-212">Incidencia de seguimiento n.º 20305</span><span class="sxs-lookup"><span data-stu-id="06e3c-212">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="06e3c-213">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-213">**Old behavior**</span></span>

<span data-ttu-id="06e3c-214">`IMigrationsModelDiffer` API se definía mediante `IModel`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-214">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="06e3c-215">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-215">**New behavior**</span></span>

<span data-ttu-id="06e3c-216">`IMigrationsModelDiffer` API ahora usa `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-216">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="06e3c-217">Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.</span><span class="sxs-lookup"><span data-stu-id="06e3c-217">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="06e3c-218">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-218">**Why**</span></span>

<span data-ttu-id="06e3c-219">`IRelationalModel` es una representación recién agregada del esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="06e3c-219">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="06e3c-220">Su uso para encontrar diferencias es más rápido y preciso.</span><span class="sxs-lookup"><span data-stu-id="06e3c-220">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="06e3c-221">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-221">**Mitigations**</span></span>

<span data-ttu-id="06e3c-222">Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:</span><span class="sxs-lookup"><span data-stu-id="06e3c-222">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="06e3c-223">Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="06e3c-223">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="06e3c-224">Los discriminadores son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="06e3c-224">Discriminators are read-only</span></span>

[<span data-ttu-id="06e3c-225">Incidencia de seguimiento n.º 21154</span><span class="sxs-lookup"><span data-stu-id="06e3c-225">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="06e3c-226">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-226">**Old behavior**</span></span>

<span data-ttu-id="06e3c-227">Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-227">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="06e3c-228">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-228">**New behavior**</span></span>

<span data-ttu-id="06e3c-229">En el caso anterior, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="06e3c-229">An exception will be throws in the above case.</span></span>

<span data-ttu-id="06e3c-230">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-230">**Why**</span></span>

<span data-ttu-id="06e3c-231">EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.</span><span class="sxs-lookup"><span data-stu-id="06e3c-231">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="06e3c-232">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-232">**Mitigations**</span></span>

<span data-ttu-id="06e3c-233">Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:</span><span class="sxs-lookup"><span data-stu-id="06e3c-233">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="06e3c-234">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="06e3c-234">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="06e3c-235">Problema de seguimiento n.º 18903</span><span class="sxs-lookup"><span data-stu-id="06e3c-235">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="06e3c-236">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-236">**Old behavior**</span></span>

<span data-ttu-id="06e3c-237">Los tipos de entidad se asignaban en la definición de consultas en el nivel básico.</span><span class="sxs-lookup"><span data-stu-id="06e3c-237">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="06e3c-238">Cuando el tipo de entidad se usaba en la raíz de la consulta del tipo de entidad se sustituía por la consulta de definición de cualquier proveedor.</span><span class="sxs-lookup"><span data-stu-id="06e3c-238">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="06e3c-239">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-239">**New behavior**</span></span>

<span data-ttu-id="06e3c-240">Las API de definición de consulta han quedado en desuso.</span><span class="sxs-lookup"><span data-stu-id="06e3c-240">APIs for defining query are deprecated.</span></span> <span data-ttu-id="06e3c-241">Se introdujeron nuevas API específicas del proveedor.</span><span class="sxs-lookup"><span data-stu-id="06e3c-241">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="06e3c-242">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-242">**Why**</span></span>

<span data-ttu-id="06e3c-243">Aunque la definición de consulta se implementaba como consulta de reemplazo siempre que se usaba la raíz de la consulta en la consulta, tenía algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="06e3c-243">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="06e3c-244">Si la definición de consulta establece la proyección del tipo de entidad con `new { ... }` en el método `Select`, su identificación como entidad requería trabajo adicional y lo hacía incoherente con el modo en que EF Core trata los tipos nominales en la consulta.</span><span class="sxs-lookup"><span data-stu-id="06e3c-244">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="06e3c-245">En el caso de los proveedores relacionales `FromSql` todavía hay que pasar la cadena SQL en forma de expresión LINQ.</span><span class="sxs-lookup"><span data-stu-id="06e3c-245">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="06e3c-246">Las definiciones de consulta se introdujeron inicialmente como vistas del lado cliente para su uso con el proveedor en memoria para las entidades sin clave (similares a las vistas de base de datos de las bases de datos relacionales).</span><span class="sxs-lookup"><span data-stu-id="06e3c-246">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="06e3c-247">Esta definición facilita la prueba de la aplicación en la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="06e3c-247">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="06e3c-248">Después, se convirtieron en ampliamente aplicables, lo que resultaba útil pero no era coherente y resultaba difícil de entender.</span><span class="sxs-lookup"><span data-stu-id="06e3c-248">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="06e3c-249">Por tanto, decidimos simplificar el concepto.</span><span class="sxs-lookup"><span data-stu-id="06e3c-249">So we decided to simplify the concept.</span></span> <span data-ttu-id="06e3c-250">Hemos creado una consulta de definición basada en LINQ exclusiva para el proveedor en memoria, que tratamos de otra forma.</span><span class="sxs-lookup"><span data-stu-id="06e3c-250">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="06e3c-251">Para más información, consulte [este problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="06e3c-251">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="06e3c-252">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-252">**Mitigations**</span></span>

<span data-ttu-id="06e3c-253">En el caso de los proveedores relacionales, use el método `ToSqlQuery` en `OnModelCreating` y pase una cadena SQL que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="06e3c-253">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="06e3c-254">En el caso del proveedor en memoria, use el método `ToInMemoryQuery` en `OnModelCreating` y pase una consulta LINQ que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="06e3c-254">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="06e3c-255">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="06e3c-255">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="06e3c-256">Incidencia de seguimiento n.º 20294</span><span class="sxs-lookup"><span data-stu-id="06e3c-256">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="06e3c-257">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-257">**Old behavior**</span></span>

<span data-ttu-id="06e3c-258">Los métodos EF.Functions específicos del proveedor incluían la implementación para la ejecución del cliente, lo cual les permitía ejecutarse en el proveedor de InMemory.</span><span class="sxs-lookup"><span data-stu-id="06e3c-258">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="06e3c-259">Por ejemplo, `EF.Functions.DateDiffDay` es un método específico de SQL Server que funcionaba en el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="06e3c-259">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="06e3c-260">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-260">**New behavior**</span></span>

<span data-ttu-id="06e3c-261">Los métodos específicos del proveedor se han actualizado para producir una excepción en el cuerpo del método a fin de bloquear su evaluación en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="06e3c-261">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="06e3c-262">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-262">**Why**</span></span>

<span data-ttu-id="06e3c-263">Los métodos específicos del proveedor se asignan a una función de base de datos.</span><span class="sxs-lookup"><span data-stu-id="06e3c-263">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="06e3c-264">El cálculo realizado por la función de base de datos asignada no siempre se puede replicar en el lado cliente en LINQ.</span><span class="sxs-lookup"><span data-stu-id="06e3c-264">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="06e3c-265">Esto puede provocar que el resultado del servidor sea diferente al ejecutar el mismo método en el cliente.</span><span class="sxs-lookup"><span data-stu-id="06e3c-265">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="06e3c-266">Dado que estos métodos se usan en LINQ para traducir a funciones específicas de base de datos, no es necesario que se evalúen en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="06e3c-266">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="06e3c-267">Dado que el proveedor InMemory es una base de datos *diferente*, estos métodos no están disponibles para este proveedor.</span><span class="sxs-lookup"><span data-stu-id="06e3c-267">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="06e3c-268">Al intentar ejecutarlos para el proveedor InMemory o cualquier otro proveedor que no traduzca estos métodos, se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="06e3c-268">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="06e3c-269">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-269">**Mitigations**</span></span>

<span data-ttu-id="06e3c-270">Dado que no hay forma de imitar el comportamiento de las funciones de base de datos con precisión, debe probar las consultas que las contienen en el mismo tipo de base de datos que en producción.</span><span class="sxs-lookup"><span data-stu-id="06e3c-270">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="06e3c-271">IndexBuilder.HasName ahora está obsoleto</span><span class="sxs-lookup"><span data-stu-id="06e3c-271">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="06e3c-272">Incidencia de seguimiento n.º 21089</span><span class="sxs-lookup"><span data-stu-id="06e3c-272">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

<span data-ttu-id="06e3c-273">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-273">**Old behavior**</span></span>

<span data-ttu-id="06e3c-274">Anteriormente, solo se podía definir un índice en un conjunto determinado de propiedades.</span><span class="sxs-lookup"><span data-stu-id="06e3c-274">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="06e3c-275">El nombre de la base de datos de un índice se configuró mediante IndexBuilder.HasName.</span><span class="sxs-lookup"><span data-stu-id="06e3c-275">The database name of an index was configured using IndexBuilder.HasName.</span></span>

<span data-ttu-id="06e3c-276">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-276">**New behavior**</span></span>

<span data-ttu-id="06e3c-277">Ahora se permiten varios índices en el mismo conjunto o las mismas propiedades.</span><span class="sxs-lookup"><span data-stu-id="06e3c-277">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="06e3c-278">Ahora, estos índices se distinguen por un nombre en el modelo.</span><span class="sxs-lookup"><span data-stu-id="06e3c-278">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="06e3c-279">Por convención, el nombre del modelo se utiliza como nombre de la base de datos; sin embargo, también se puede configurar de forma independiente utilizando HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="06e3c-279">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

<span data-ttu-id="06e3c-280">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-280">**Why**</span></span>

<span data-ttu-id="06e3c-281">En el futuro, nos gustaría habilitar índices ascendentes y descendentes o índices con distintas intercalaciones en el mismo conjunto de propiedades.</span><span class="sxs-lookup"><span data-stu-id="06e3c-281">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="06e3c-282">Este cambio nos hace dar otro paso en esa dirección.</span><span class="sxs-lookup"><span data-stu-id="06e3c-282">This change moves us another step in that direction.</span></span>

<span data-ttu-id="06e3c-283">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-283">**Mitigations**</span></span>

<span data-ttu-id="06e3c-284">Cualquier código que llamara anteriormente a IndexBuilder.HasName debe actualizarse para llamar a HasDatabaseName en su lugar.</span><span class="sxs-lookup"><span data-stu-id="06e3c-284">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="06e3c-285">Si su proyecto incluye migraciones generadas antes de la versión 2.0.0 de EF Core, puede ignorar la advertencia en esos archivos de forma segura y suprimirla agregando `#pragma warning disable 612, 618`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-285">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="06e3c-286">Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="06e3c-286">A pluarlizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="06e3c-287">Incidencia de seguimiento n.º 11160</span><span class="sxs-lookup"><span data-stu-id="06e3c-287">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

<span data-ttu-id="06e3c-288">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="06e3c-288">**Old behavior**</span></span>

<span data-ttu-id="06e3c-289">Anteriormente, tenía que instalar un paquete de pluralizador independiente para pluralizar los nombres de navegación de colección y DbSet y singularizar los nombres de tabla al aplicar scaffoding a DbContext y los tipos de entidad mediante la utilización de técnicas de ingeniería inversa en un esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="06e3c-289">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffoding a DbContext and entity types by reverse engineering a database schema.</span></span>

<span data-ttu-id="06e3c-290">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="06e3c-290">**New behavior**</span></span>

<span data-ttu-id="06e3c-291">EF Core incluye ahora un pluralizador que usa la biblioteca [Humanizer](https://github.com/Humanizr/Humanizer).</span><span class="sxs-lookup"><span data-stu-id="06e3c-291">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="06e3c-292">Se trata de la misma biblioteca que Visual Studio usa para recomendar nombres de variable.</span><span class="sxs-lookup"><span data-stu-id="06e3c-292">This is the same library Visual Studio uses to recommend variable names.</span></span>

<span data-ttu-id="06e3c-293">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="06e3c-293">**Why**</span></span>

<span data-ttu-id="06e3c-294">El uso de formas plurales de palabras para las propiedades de colección y de formas singulares para los tipos y las propiedades de referencia es idiomático en .NET.</span><span class="sxs-lookup"><span data-stu-id="06e3c-294">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

<span data-ttu-id="06e3c-295">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="06e3c-295">**Mitigations**</span></span>

<span data-ttu-id="06e3c-296">Para deshabilitar el pluralizador, use la opción `--no-pluralize` en `dotnet ef dbcontext scaffold` o el modificador `-NoPluralize` en `Scaffold-DbContext`.</span><span class="sxs-lookup"><span data-stu-id="06e3c-296">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>
