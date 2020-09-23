---
title: 'Cambios importantes en EF Core 5.0: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 5.0
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 63fd1d1a01b7a72fd34bb9a0130191131306426c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070801"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="e2115-103">Cambios importantes en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="e2115-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="e2115-104">Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="e2115-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="e2115-105">Resumen</span><span class="sxs-lookup"><span data-stu-id="e2115-105">Summary</span></span>

| <span data-ttu-id="e2115-106">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="e2115-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="e2115-107">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="e2115-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| <span data-ttu-id="e2115-108">[El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).</span><span class="sxs-lookup"><span data-stu-id="e2115-108">[Required on the navigation from principal to dependent has different semantics](#required-dependent)</span></span>                                 | <span data-ttu-id="e2115-109">Media</span><span class="sxs-lookup"><span data-stu-id="e2115-109">Medium</span></span>     |
| [<span data-ttu-id="e2115-110">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="e2115-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="e2115-111">Media</span><span class="sxs-lookup"><span data-stu-id="e2115-111">Medium</span></span>     |
| [<span data-ttu-id="e2115-112">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="e2115-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="e2115-113">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-113">Low</span></span>        |
| [<span data-ttu-id="e2115-114">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="e2115-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="e2115-115">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-115">Low</span></span>        |
| [<span data-ttu-id="e2115-116">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="e2115-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="e2115-117">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-117">Low</span></span>        |
| <span data-ttu-id="e2115-118">[Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="e2115-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="e2115-119">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-119">Low</span></span>        |
| [<span data-ttu-id="e2115-120">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="e2115-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="e2115-121">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-121">Low</span></span>        |
| [<span data-ttu-id="e2115-122">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="e2115-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="e2115-123">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-123">Low</span></span>        |
| <span data-ttu-id="e2115-124">[IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).</span><span class="sxs-lookup"><span data-stu-id="e2115-124">[IMigrationsModelDiffer now uses IRelationalModel](#relational-model)</span></span>                                                                 | <span data-ttu-id="e2115-125">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-125">Low</span></span>        |
| <span data-ttu-id="e2115-126">[Los discriminadores son de solo lectura](#read-only-discriminators).</span><span class="sxs-lookup"><span data-stu-id="e2115-126">[Discriminators are read-only](#read-only-discriminators)</span></span>                                                                             | <span data-ttu-id="e2115-127">Bajo</span><span class="sxs-lookup"><span data-stu-id="e2115-127">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="e2115-128">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="e2115-128">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="e2115-129">Problema de seguimiento n.º 14257</span><span class="sxs-lookup"><span data-stu-id="e2115-129">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="e2115-130">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-130">**Old behavior**</span></span>

<span data-ttu-id="e2115-131">Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría.</span><span class="sxs-lookup"><span data-stu-id="e2115-131">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="e2115-132">Sin embargo, solo ha afectado a la creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e2115-132">However, it only ever affected database creation.</span></span> <span data-ttu-id="e2115-133">No era necesario especificarlo para consultar los valores con dimensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="e2115-133">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="e2115-134">Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="e2115-134">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="e2115-135">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-135">**New behavior**</span></span>

<span data-ttu-id="e2115-136">Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="e2115-136">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="e2115-137">Esta API coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="e2115-137">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="e2115-138">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-138">**Why**</span></span>

<span data-ttu-id="e2115-139">El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.</span><span class="sxs-lookup"><span data-stu-id="e2115-139">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="e2115-140">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-140">**Mitigations**</span></span>

<span data-ttu-id="e2115-141">Use `HasColumnType` para especificar la dimensión:</span><span class="sxs-lookup"><span data-stu-id="e2115-141">Use `HasColumnType` to specify the dimension:</span></span>

```cs
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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="e2115-142">El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.</span><span class="sxs-lookup"><span data-stu-id="e2115-142">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="e2115-143">Incidencia de seguimiento n.º 17286</span><span class="sxs-lookup"><span data-stu-id="e2115-143">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="e2115-144">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-144">**Old behavior**</span></span>

<span data-ttu-id="e2115-145">Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias.</span><span class="sxs-lookup"><span data-stu-id="e2115-145">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="e2115-146">Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.</span><span class="sxs-lookup"><span data-stu-id="e2115-146">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="e2115-147">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-147">**New behavior**</span></span>

<span data-ttu-id="e2115-148">Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como obligatoria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.</span><span class="sxs-lookup"><span data-stu-id="e2115-148">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="e2115-149">Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:</span><span class="sxs-lookup"><span data-stu-id="e2115-149">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="e2115-150">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-150">**Why**</span></span>

<span data-ttu-id="e2115-151">El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="e2115-151">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="e2115-152">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-152">**Mitigations**</span></span>

<span data-ttu-id="e2115-153">Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="e2115-153">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="e2115-154">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="e2115-154">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="e2115-155">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="e2115-155">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="e2115-156">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-156">**Old behavior**</span></span>

<span data-ttu-id="e2115-157">La propiedad de clave de partición solo se agregaba a la clave alternativa que incluye `id`.</span><span class="sxs-lookup"><span data-stu-id="e2115-157">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="e2115-158">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-158">**New behavior**</span></span>

<span data-ttu-id="e2115-159">La propiedad de clave de partición ahora también se agrega por convención a la clave principal.</span><span class="sxs-lookup"><span data-stu-id="e2115-159">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="e2115-160">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-160">**Why**</span></span>

<span data-ttu-id="e2115-161">Este cambio hace que el modelo se alinee mejor con la semántica de Azure Cosmos DB y mejora el rendimiento de `Find` y algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="e2115-161">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="e2115-162">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-162">**Mitigations**</span></span>

<span data-ttu-id="e2115-163">Para evitar que la propiedad de clave de partición se agregue a la clave principal, configúrela en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="e2115-163">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="e2115-164">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="e2115-164">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="e2115-165">Problema de seguimiento n.º 17751</span><span class="sxs-lookup"><span data-stu-id="e2115-165">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="e2115-166">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-166">**Old behavior**</span></span>

<span data-ttu-id="e2115-167">La propiedad Shadow asignada a la propiedad `id` de JSON también se llama `id`.</span><span class="sxs-lookup"><span data-stu-id="e2115-167">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="e2115-168">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-168">**New behavior**</span></span>

<span data-ttu-id="e2115-169">La propiedad Shadow creada por convención se denomina ahora `__id`.</span><span class="sxs-lookup"><span data-stu-id="e2115-169">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="e2115-170">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-170">**Why**</span></span>

<span data-ttu-id="e2115-171">Este cambio hace menos probable que la propiedad `id` entre en conflicto con una propiedad existente en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="e2115-171">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="e2115-172">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-172">**Mitigations**</span></span>

<span data-ttu-id="e2115-173">Para volver al comportamiento de la versión 3.x, configure la propiedad `id` en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="e2115-173">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="e2115-174">Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números</span><span class="sxs-lookup"><span data-stu-id="e2115-174">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="e2115-175">Problema de seguimiento n.º 17306</span><span class="sxs-lookup"><span data-stu-id="e2115-175">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="e2115-176">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-176">**Old behavior**</span></span>

<span data-ttu-id="e2115-177">Las propiedades de tipo byte[] se almacenaban como matriz de números.</span><span class="sxs-lookup"><span data-stu-id="e2115-177">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="e2115-178">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-178">**New behavior**</span></span>

<span data-ttu-id="e2115-179">Las propiedades de tipo byte[] se almacenan ahora como cadena Base64.</span><span class="sxs-lookup"><span data-stu-id="e2115-179">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="e2115-180">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-180">**Why**</span></span>

<span data-ttu-id="e2115-181">Esta representación de byte [] se alinea mejor con las expectativas y es el comportamiento predeterminado de las principales bibliotecas de serialización de JSON.</span><span class="sxs-lookup"><span data-stu-id="e2115-181">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="e2115-182">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-182">**Mitigations**</span></span>

<span data-ttu-id="e2115-183">Los datos existentes almacenados como matrices de números se seguirán consultando correctamente, pero actualmente no hay ninguna manera compatible de volver a cambiar el comportamiento de inserción.</span><span class="sxs-lookup"><span data-stu-id="e2115-183">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="e2115-184">Si esta limitación bloquea su escenario, haga un comentario en [este problema](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="e2115-184">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="e2115-185">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="e2115-185">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="e2115-186">Problema de seguimiento n.º 17874</span><span class="sxs-lookup"><span data-stu-id="e2115-186">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="e2115-187">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-187">**Old behavior**</span></span>

<span data-ttu-id="e2115-188">Anteriormente, se llamaba a los métodos de extensión `GetPropertyName` y `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="e2115-188">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="e2115-189">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-189">**New behavior**</span></span>

<span data-ttu-id="e2115-190">La API antigua estaba obsoleta y se agregaron nuevos métodos: `GetJsonPropertyName`, `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="e2115-190">The old API was obsoleted and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="e2115-191">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-191">**Why**</span></span>

<span data-ttu-id="e2115-192">Este cambio elimina la ambigüedad en torno a lo que estos métodos configuran.</span><span class="sxs-lookup"><span data-stu-id="e2115-192">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="e2115-193">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-193">**Mitigations**</span></span>

<span data-ttu-id="e2115-194">Use la nueva API o suspenda temporalmente las advertencias obsoletas.</span><span class="sxs-lookup"><span data-stu-id="e2115-194">Use the new API or temporarily suspend the obsolete warnings.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="e2115-195">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="e2115-195">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="e2115-196">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="e2115-196">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="e2115-197">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-197">**Old behavior**</span></span>

<span data-ttu-id="e2115-198">Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.</span><span class="sxs-lookup"><span data-stu-id="e2115-198">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="e2115-199">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-199">**New behavior**</span></span>

<span data-ttu-id="e2115-200">Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="e2115-200">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="e2115-201">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-201">**Why**</span></span>

<span data-ttu-id="e2115-202">Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.</span><span class="sxs-lookup"><span data-stu-id="e2115-202">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="e2115-203">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-203">**Mitigations**</span></span>

<span data-ttu-id="e2115-204">Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.</span><span class="sxs-lookup"><span data-stu-id="e2115-204">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="e2115-205">IMigrationsModelDiffer ahora usa IRelationalModel.</span><span class="sxs-lookup"><span data-stu-id="e2115-205">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="e2115-206">Incidencia de seguimiento n.º 20305</span><span class="sxs-lookup"><span data-stu-id="e2115-206">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="e2115-207">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-207">**Old behavior**</span></span>

<span data-ttu-id="e2115-208">`IMigrationsModelDiffer` API se definía mediante `IModel`.</span><span class="sxs-lookup"><span data-stu-id="e2115-208">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="e2115-209">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-209">**New behavior**</span></span>

<span data-ttu-id="e2115-210">`IMigrationsModelDiffer` API ahora usa `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="e2115-210">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="e2115-211">Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.</span><span class="sxs-lookup"><span data-stu-id="e2115-211">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="e2115-212">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-212">**Why**</span></span>

<span data-ttu-id="e2115-213">`IRelationalModel` es una representación recién agregada del esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e2115-213">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="e2115-214">Su uso para encontrar diferencias es más rápido y preciso.</span><span class="sxs-lookup"><span data-stu-id="e2115-214">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="e2115-215">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-215">**Mitigations**</span></span>

<span data-ttu-id="e2115-216">Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:</span><span class="sxs-lookup"><span data-stu-id="e2115-216">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```cs
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

<span data-ttu-id="e2115-217">Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="e2115-217">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="e2115-218">Los discriminadores son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="e2115-218">Discriminators are read-only</span></span>

[<span data-ttu-id="e2115-219">Incidencia de seguimiento n.º 21154</span><span class="sxs-lookup"><span data-stu-id="e2115-219">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="e2115-220">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-220">**Old behavior**</span></span>

<span data-ttu-id="e2115-221">Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="e2115-221">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="e2115-222">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-222">**New behavior**</span></span>

<span data-ttu-id="e2115-223">En el caso anterior, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="e2115-223">An exception will be throws in the above case.</span></span>

<span data-ttu-id="e2115-224">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-224">**Why**</span></span>

<span data-ttu-id="e2115-225">EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.</span><span class="sxs-lookup"><span data-stu-id="e2115-225">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="e2115-226">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-226">**Mitigations**</span></span>

<span data-ttu-id="e2115-227">Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:</span><span class="sxs-lookup"><span data-stu-id="e2115-227">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="e2115-228">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="e2115-228">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="e2115-229">Problema de seguimiento n.º 18903</span><span class="sxs-lookup"><span data-stu-id="e2115-229">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="e2115-230">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="e2115-230">**Old behavior**</span></span>

<span data-ttu-id="e2115-231">Los tipos de entidad se asignaban en la definición de consultas en el nivel básico.</span><span class="sxs-lookup"><span data-stu-id="e2115-231">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="e2115-232">Cuando el tipo de entidad se usaba en la raíz de la consulta del tipo de entidad se sustituía por la consulta de definición de cualquier proveedor.</span><span class="sxs-lookup"><span data-stu-id="e2115-232">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="e2115-233">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="e2115-233">**New behavior**</span></span>

<span data-ttu-id="e2115-234">Las API de definición de consulta han quedado en desuso.</span><span class="sxs-lookup"><span data-stu-id="e2115-234">APIs for defining query are deprecated.</span></span> <span data-ttu-id="e2115-235">Se introdujeron nuevas API específicas del proveedor.</span><span class="sxs-lookup"><span data-stu-id="e2115-235">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="e2115-236">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="e2115-236">**Why**</span></span>

<span data-ttu-id="e2115-237">Aunque la definición de consulta se implementaba como consulta de reemplazo siempre que se usaba la raíz de la consulta en la consulta, tenía algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="e2115-237">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="e2115-238">Si la definición de consulta establece la proyección del tipo de entidad con `new { ... }` en el método `Select`, su identificación como entidad requería trabajo adicional y lo hacía incoherente con el modo en que EF Core trata los tipos nominales en la consulta.</span><span class="sxs-lookup"><span data-stu-id="e2115-238">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="e2115-239">En el caso de los proveedores relacionales `FromSql` todavía hay que pasar la cadena SQL en forma de expresión LINQ.</span><span class="sxs-lookup"><span data-stu-id="e2115-239">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="e2115-240">Las definiciones de consulta se introdujeron inicialmente como vistas del lado cliente para su uso con el proveedor en memoria para las entidades sin clave (similares a las vistas de base de datos de las bases de datos relacionales).</span><span class="sxs-lookup"><span data-stu-id="e2115-240">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="e2115-241">Esta definición facilita la prueba de la aplicación en la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="e2115-241">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="e2115-242">Después, se convirtieron en ampliamente aplicables, lo que resultaba útil pero no era coherente y resultaba difícil de entender.</span><span class="sxs-lookup"><span data-stu-id="e2115-242">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="e2115-243">Por tanto, decidimos simplificar el concepto.</span><span class="sxs-lookup"><span data-stu-id="e2115-243">So we decided to simplify the concept.</span></span> <span data-ttu-id="e2115-244">Hemos creado una consulta de definición basada en LINQ exclusiva para el proveedor en memoria, que tratamos de otra forma.</span><span class="sxs-lookup"><span data-stu-id="e2115-244">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="e2115-245">Para más información, consulte [este problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="e2115-245">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="e2115-246">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="e2115-246">**Mitigations**</span></span>

<span data-ttu-id="e2115-247">En el caso de los proveedores relacionales, use el método `ToSqlQuery` en `OnModelCreating` y pase una cadena SQL que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="e2115-247">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="e2115-248">En el caso del proveedor en memoria, use el método `ToInMemoryQuery` en `OnModelCreating` y pase una consulta LINQ que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="e2115-248">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>
