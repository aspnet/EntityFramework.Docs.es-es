---
title: 'Cambios importantes en EF Core 5.0: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 5.0
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210372"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="6bff8-103">Cambios importantes en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="6bff8-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="6bff8-104">Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="6bff8-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="6bff8-105">Resumen</span><span class="sxs-lookup"><span data-stu-id="6bff8-105">Summary</span></span>

| <span data-ttu-id="6bff8-106">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="6bff8-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="6bff8-107">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="6bff8-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| <span data-ttu-id="6bff8-108">[El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).</span><span class="sxs-lookup"><span data-stu-id="6bff8-108">[Required on the navigation from principal to dependent has different semantics](#required-dependent)</span></span>                                 | <span data-ttu-id="6bff8-109">Media</span><span class="sxs-lookup"><span data-stu-id="6bff8-109">Medium</span></span>     |
| [<span data-ttu-id="6bff8-110">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="6bff8-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="6bff8-111">Media</span><span class="sxs-lookup"><span data-stu-id="6bff8-111">Medium</span></span>     |
| [<span data-ttu-id="6bff8-112">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="6bff8-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="6bff8-113">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-113">Low</span></span>        |
| [<span data-ttu-id="6bff8-114">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="6bff8-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="6bff8-115">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-115">Low</span></span>        |
| [<span data-ttu-id="6bff8-116">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="6bff8-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="6bff8-117">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-117">Low</span></span>        |
| <span data-ttu-id="6bff8-118">[Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="6bff8-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="6bff8-119">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-119">Low</span></span>        |
| [<span data-ttu-id="6bff8-120">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="6bff8-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="6bff8-121">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-121">Low</span></span>        |
| [<span data-ttu-id="6bff8-122">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="6bff8-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="6bff8-123">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-123">Low</span></span>        |
| <span data-ttu-id="6bff8-124">[IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).</span><span class="sxs-lookup"><span data-stu-id="6bff8-124">[IMigrationsModelDiffer now uses IRelationalModel](#relational-model)</span></span>                                                                 | <span data-ttu-id="6bff8-125">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-125">Low</span></span>        |
| <span data-ttu-id="6bff8-126">[Los discriminadores son de solo lectura](#read-only-discriminators).</span><span class="sxs-lookup"><span data-stu-id="6bff8-126">[Discriminators are read-only](#read-only-discriminators)</span></span>                                                                             | <span data-ttu-id="6bff8-127">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-127">Low</span></span>        |
| [<span data-ttu-id="6bff8-128">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory</span><span class="sxs-lookup"><span data-stu-id="6bff8-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="6bff8-129">Bajo</span><span class="sxs-lookup"><span data-stu-id="6bff8-129">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="6bff8-130">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="6bff8-130">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="6bff8-131">Problema de seguimiento n.º 14257</span><span class="sxs-lookup"><span data-stu-id="6bff8-131">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="6bff8-132">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-132">**Old behavior**</span></span>

<span data-ttu-id="6bff8-133">Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría.</span><span class="sxs-lookup"><span data-stu-id="6bff8-133">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="6bff8-134">Sin embargo, solo ha afectado a la creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6bff8-134">However, it only ever affected database creation.</span></span> <span data-ttu-id="6bff8-135">No era necesario especificarlo para consultar los valores con dimensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="6bff8-135">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="6bff8-136">Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="6bff8-136">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="6bff8-137">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-137">**New behavior**</span></span>

<span data-ttu-id="6bff8-138">Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="6bff8-138">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="6bff8-139">Esta API coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="6bff8-139">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="6bff8-140">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-140">**Why**</span></span>

<span data-ttu-id="6bff8-141">El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.</span><span class="sxs-lookup"><span data-stu-id="6bff8-141">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="6bff8-142">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-142">**Mitigations**</span></span>

<span data-ttu-id="6bff8-143">Use `HasColumnType` para especificar la dimensión:</span><span class="sxs-lookup"><span data-stu-id="6bff8-143">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="6bff8-144">El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.</span><span class="sxs-lookup"><span data-stu-id="6bff8-144">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="6bff8-145">Incidencia de seguimiento n.º 17286</span><span class="sxs-lookup"><span data-stu-id="6bff8-145">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="6bff8-146">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-146">**Old behavior**</span></span>

<span data-ttu-id="6bff8-147">Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias.</span><span class="sxs-lookup"><span data-stu-id="6bff8-147">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="6bff8-148">Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.</span><span class="sxs-lookup"><span data-stu-id="6bff8-148">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="6bff8-149">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-149">**New behavior**</span></span>

<span data-ttu-id="6bff8-150">Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como obligatoria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.</span><span class="sxs-lookup"><span data-stu-id="6bff8-150">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="6bff8-151">Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:</span><span class="sxs-lookup"><span data-stu-id="6bff8-151">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="6bff8-152">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-152">**Why**</span></span>

<span data-ttu-id="6bff8-153">El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="6bff8-153">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="6bff8-154">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-154">**Mitigations**</span></span>

<span data-ttu-id="6bff8-155">Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="6bff8-155">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="6bff8-156">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="6bff8-156">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="6bff8-157">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="6bff8-157">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="6bff8-158">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-158">**Old behavior**</span></span>

<span data-ttu-id="6bff8-159">La propiedad de clave de partición solo se agregaba a la clave alternativa que incluye `id`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-159">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="6bff8-160">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-160">**New behavior**</span></span>

<span data-ttu-id="6bff8-161">La propiedad de clave de partición ahora también se agrega por convención a la clave principal.</span><span class="sxs-lookup"><span data-stu-id="6bff8-161">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="6bff8-162">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-162">**Why**</span></span>

<span data-ttu-id="6bff8-163">Este cambio hace que el modelo se alinee mejor con la semántica de Azure Cosmos DB y mejora el rendimiento de `Find` y algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="6bff8-163">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="6bff8-164">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-164">**Mitigations**</span></span>

<span data-ttu-id="6bff8-165">Para evitar que la propiedad de clave de partición se agregue a la clave principal, configúrela en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-165">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="6bff8-166">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="6bff8-166">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="6bff8-167">Problema de seguimiento n.º 17751</span><span class="sxs-lookup"><span data-stu-id="6bff8-167">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="6bff8-168">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-168">**Old behavior**</span></span>

<span data-ttu-id="6bff8-169">La propiedad Shadow asignada a la propiedad `id` de JSON también se llama `id`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-169">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="6bff8-170">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-170">**New behavior**</span></span>

<span data-ttu-id="6bff8-171">La propiedad Shadow creada por convención se denomina ahora `__id`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-171">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="6bff8-172">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-172">**Why**</span></span>

<span data-ttu-id="6bff8-173">Este cambio hace menos probable que la propiedad `id` entre en conflicto con una propiedad existente en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="6bff8-173">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="6bff8-174">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-174">**Mitigations**</span></span>

<span data-ttu-id="6bff8-175">Para volver al comportamiento de la versión 3.x, configure la propiedad `id` en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-175">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="6bff8-176">Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números</span><span class="sxs-lookup"><span data-stu-id="6bff8-176">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="6bff8-177">Problema de seguimiento n.º 17306</span><span class="sxs-lookup"><span data-stu-id="6bff8-177">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="6bff8-178">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-178">**Old behavior**</span></span>

<span data-ttu-id="6bff8-179">Las propiedades de tipo byte[] se almacenaban como matriz de números.</span><span class="sxs-lookup"><span data-stu-id="6bff8-179">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="6bff8-180">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-180">**New behavior**</span></span>

<span data-ttu-id="6bff8-181">Las propiedades de tipo byte[] se almacenan ahora como cadena Base64.</span><span class="sxs-lookup"><span data-stu-id="6bff8-181">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="6bff8-182">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-182">**Why**</span></span>

<span data-ttu-id="6bff8-183">Esta representación de byte [] se alinea mejor con las expectativas y es el comportamiento predeterminado de las principales bibliotecas de serialización de JSON.</span><span class="sxs-lookup"><span data-stu-id="6bff8-183">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="6bff8-184">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-184">**Mitigations**</span></span>

<span data-ttu-id="6bff8-185">Los datos existentes almacenados como matrices de números se seguirán consultando correctamente, pero actualmente no hay ninguna manera compatible de volver a cambiar el comportamiento de inserción.</span><span class="sxs-lookup"><span data-stu-id="6bff8-185">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="6bff8-186">Si esta limitación bloquea su escenario, haga un comentario en [este problema](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="6bff8-186">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="6bff8-187">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="6bff8-187">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="6bff8-188">Problema de seguimiento n.º 17874</span><span class="sxs-lookup"><span data-stu-id="6bff8-188">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="6bff8-189">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-189">**Old behavior**</span></span>

<span data-ttu-id="6bff8-190">Anteriormente, se llamaba a los métodos de extensión `GetPropertyName` y `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="6bff8-190">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="6bff8-191">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-191">**New behavior**</span></span>

<span data-ttu-id="6bff8-192">La API antigua se ha quitado y se han agregado métodos nuevos: `GetJsonPropertyName` y `SetJsonPropertyName`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-192">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="6bff8-193">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-193">**Why**</span></span>

<span data-ttu-id="6bff8-194">Este cambio elimina la ambigüedad en torno a lo que estos métodos configuran.</span><span class="sxs-lookup"><span data-stu-id="6bff8-194">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="6bff8-195">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-195">**Mitigations**</span></span>

<span data-ttu-id="6bff8-196">Use la API nueva.</span><span class="sxs-lookup"><span data-stu-id="6bff8-196">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="6bff8-197">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="6bff8-197">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="6bff8-198">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="6bff8-198">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="6bff8-199">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-199">**Old behavior**</span></span>

<span data-ttu-id="6bff8-200">Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.</span><span class="sxs-lookup"><span data-stu-id="6bff8-200">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="6bff8-201">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-201">**New behavior**</span></span>

<span data-ttu-id="6bff8-202">Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="6bff8-202">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="6bff8-203">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-203">**Why**</span></span>

<span data-ttu-id="6bff8-204">Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.</span><span class="sxs-lookup"><span data-stu-id="6bff8-204">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="6bff8-205">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-205">**Mitigations**</span></span>

<span data-ttu-id="6bff8-206">Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.</span><span class="sxs-lookup"><span data-stu-id="6bff8-206">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="6bff8-207">IMigrationsModelDiffer ahora usa IRelationalModel.</span><span class="sxs-lookup"><span data-stu-id="6bff8-207">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="6bff8-208">Incidencia de seguimiento n.º 20305</span><span class="sxs-lookup"><span data-stu-id="6bff8-208">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="6bff8-209">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-209">**Old behavior**</span></span>

<span data-ttu-id="6bff8-210">`IMigrationsModelDiffer` API se definía mediante `IModel`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-210">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="6bff8-211">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-211">**New behavior**</span></span>

<span data-ttu-id="6bff8-212">`IMigrationsModelDiffer` API ahora usa `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-212">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="6bff8-213">Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.</span><span class="sxs-lookup"><span data-stu-id="6bff8-213">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="6bff8-214">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-214">**Why**</span></span>

<span data-ttu-id="6bff8-215">`IRelationalModel` es una representación recién agregada del esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6bff8-215">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="6bff8-216">Su uso para encontrar diferencias es más rápido y preciso.</span><span class="sxs-lookup"><span data-stu-id="6bff8-216">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="6bff8-217">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-217">**Mitigations**</span></span>

<span data-ttu-id="6bff8-218">Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:</span><span class="sxs-lookup"><span data-stu-id="6bff8-218">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="6bff8-219">Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="6bff8-219">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="6bff8-220">Los discriminadores son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="6bff8-220">Discriminators are read-only</span></span>

[<span data-ttu-id="6bff8-221">Incidencia de seguimiento n.º 21154</span><span class="sxs-lookup"><span data-stu-id="6bff8-221">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="6bff8-222">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-222">**Old behavior**</span></span>

<span data-ttu-id="6bff8-223">Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="6bff8-223">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="6bff8-224">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-224">**New behavior**</span></span>

<span data-ttu-id="6bff8-225">En el caso anterior, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="6bff8-225">An exception will be throws in the above case.</span></span>

<span data-ttu-id="6bff8-226">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-226">**Why**</span></span>

<span data-ttu-id="6bff8-227">EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.</span><span class="sxs-lookup"><span data-stu-id="6bff8-227">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="6bff8-228">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-228">**Mitigations**</span></span>

<span data-ttu-id="6bff8-229">Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:</span><span class="sxs-lookup"><span data-stu-id="6bff8-229">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="6bff8-230">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="6bff8-230">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="6bff8-231">Problema de seguimiento n.º 18903</span><span class="sxs-lookup"><span data-stu-id="6bff8-231">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="6bff8-232">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-232">**Old behavior**</span></span>

<span data-ttu-id="6bff8-233">Los tipos de entidad se asignaban en la definición de consultas en el nivel básico.</span><span class="sxs-lookup"><span data-stu-id="6bff8-233">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="6bff8-234">Cuando el tipo de entidad se usaba en la raíz de la consulta del tipo de entidad se sustituía por la consulta de definición de cualquier proveedor.</span><span class="sxs-lookup"><span data-stu-id="6bff8-234">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="6bff8-235">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-235">**New behavior**</span></span>

<span data-ttu-id="6bff8-236">Las API de definición de consulta han quedado en desuso.</span><span class="sxs-lookup"><span data-stu-id="6bff8-236">APIs for defining query are deprecated.</span></span> <span data-ttu-id="6bff8-237">Se introdujeron nuevas API específicas del proveedor.</span><span class="sxs-lookup"><span data-stu-id="6bff8-237">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="6bff8-238">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-238">**Why**</span></span>

<span data-ttu-id="6bff8-239">Aunque la definición de consulta se implementaba como consulta de reemplazo siempre que se usaba la raíz de la consulta en la consulta, tenía algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="6bff8-239">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="6bff8-240">Si la definición de consulta establece la proyección del tipo de entidad con `new { ... }` en el método `Select`, su identificación como entidad requería trabajo adicional y lo hacía incoherente con el modo en que EF Core trata los tipos nominales en la consulta.</span><span class="sxs-lookup"><span data-stu-id="6bff8-240">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="6bff8-241">En el caso de los proveedores relacionales `FromSql` todavía hay que pasar la cadena SQL en forma de expresión LINQ.</span><span class="sxs-lookup"><span data-stu-id="6bff8-241">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="6bff8-242">Las definiciones de consulta se introdujeron inicialmente como vistas del lado cliente para su uso con el proveedor en memoria para las entidades sin clave (similares a las vistas de base de datos de las bases de datos relacionales).</span><span class="sxs-lookup"><span data-stu-id="6bff8-242">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="6bff8-243">Esta definición facilita la prueba de la aplicación en la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="6bff8-243">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="6bff8-244">Después, se convirtieron en ampliamente aplicables, lo que resultaba útil pero no era coherente y resultaba difícil de entender.</span><span class="sxs-lookup"><span data-stu-id="6bff8-244">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="6bff8-245">Por tanto, decidimos simplificar el concepto.</span><span class="sxs-lookup"><span data-stu-id="6bff8-245">So we decided to simplify the concept.</span></span> <span data-ttu-id="6bff8-246">Hemos creado una consulta de definición basada en LINQ exclusiva para el proveedor en memoria, que tratamos de otra forma.</span><span class="sxs-lookup"><span data-stu-id="6bff8-246">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="6bff8-247">Para más información, consulte [este problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="6bff8-247">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="6bff8-248">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-248">**Mitigations**</span></span>

<span data-ttu-id="6bff8-249">En el caso de los proveedores relacionales, use el método `ToSqlQuery` en `OnModelCreating` y pase una cadena SQL que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="6bff8-249">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="6bff8-250">En el caso del proveedor en memoria, use el método `ToInMemoryQuery` en `OnModelCreating` y pase una consulta LINQ que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="6bff8-250">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="6bff8-251">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="6bff8-251">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="6bff8-252">Incidencia de seguimiento n.º 20294</span><span class="sxs-lookup"><span data-stu-id="6bff8-252">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="6bff8-253">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="6bff8-253">**Old behavior**</span></span>

<span data-ttu-id="6bff8-254">Los métodos EF.Functions específicos del proveedor incluían la implementación para la ejecución del cliente, lo cual les permitía ejecutarse en el proveedor de InMemory.</span><span class="sxs-lookup"><span data-stu-id="6bff8-254">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="6bff8-255">Por ejemplo, `EF.Functions.DateDiffDay` es un método específico de SQL Server que funcionaba en el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="6bff8-255">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="6bff8-256">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="6bff8-256">**New behavior**</span></span>

<span data-ttu-id="6bff8-257">Los métodos específicos del proveedor se han actualizado para producir una excepción en el cuerpo del método a fin de bloquear su evaluación en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="6bff8-257">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="6bff8-258">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="6bff8-258">**Why**</span></span>

<span data-ttu-id="6bff8-259">Los métodos específicos del proveedor se asignan a una función de base de datos.</span><span class="sxs-lookup"><span data-stu-id="6bff8-259">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="6bff8-260">El cálculo realizado por la función de base de datos asignada no siempre se puede replicar en el lado cliente en LINQ.</span><span class="sxs-lookup"><span data-stu-id="6bff8-260">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="6bff8-261">Esto puede provocar que el resultado del servidor sea diferente al ejecutar el mismo método en el cliente.</span><span class="sxs-lookup"><span data-stu-id="6bff8-261">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="6bff8-262">Dado que estos métodos se usan en LINQ para traducir a funciones específicas de base de datos, no es necesario que se evalúen en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="6bff8-262">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="6bff8-263">Dado que el proveedor InMemory es una base de datos *diferente*, estos métodos no están disponibles para este proveedor.</span><span class="sxs-lookup"><span data-stu-id="6bff8-263">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="6bff8-264">Al intentar ejecutarlos para el proveedor InMemory o cualquier otro proveedor que no traduzca estos métodos, se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="6bff8-264">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="6bff8-265">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="6bff8-265">**Mitigations**</span></span>

<span data-ttu-id="6bff8-266">Dado que no hay forma de imitar el comportamiento de las funciones de base de datos con precisión, debe probar las consultas que las contienen en el mismo tipo de base de datos que en producción.</span><span class="sxs-lookup"><span data-stu-id="6bff8-266">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>
