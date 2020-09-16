---
title: 'Cambios importantes en EF Core 5.0: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 5.0
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618686"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="76e05-103">Cambios importantes en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="76e05-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="76e05-104">Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="76e05-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="76e05-105">Resumen</span><span class="sxs-lookup"><span data-stu-id="76e05-105">Summary</span></span>

| <span data-ttu-id="76e05-106">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="76e05-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="76e05-107">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="76e05-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| <span data-ttu-id="76e05-108">[El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).</span><span class="sxs-lookup"><span data-stu-id="76e05-108">[Required on the navigation from principal to dependent has different semantics](#required-dependent)</span></span>                                 | <span data-ttu-id="76e05-109">Media</span><span class="sxs-lookup"><span data-stu-id="76e05-109">Medium</span></span>     |
| [<span data-ttu-id="76e05-110">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="76e05-110">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="76e05-111">Bajo</span><span class="sxs-lookup"><span data-stu-id="76e05-111">Low</span></span>        |
| <span data-ttu-id="76e05-112">[Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado](#non-added-generation).</span><span class="sxs-lookup"><span data-stu-id="76e05-112">[Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted](#non-added-generation)</span></span>  | <span data-ttu-id="76e05-113">Bajo</span><span class="sxs-lookup"><span data-stu-id="76e05-113">Low</span></span>        |
| <span data-ttu-id="76e05-114">[IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).</span><span class="sxs-lookup"><span data-stu-id="76e05-114">[IMigrationsModelDiffer now uses IRelationalModel](#relational-model)</span></span>                                                                 | <span data-ttu-id="76e05-115">Bajo</span><span class="sxs-lookup"><span data-stu-id="76e05-115">Low</span></span>        |
| <span data-ttu-id="76e05-116">[Los discriminadores son de solo lectura](#read-only-discriminators).</span><span class="sxs-lookup"><span data-stu-id="76e05-116">[Discriminators are read-only](#read-only-discriminators)</span></span>                                                                             | <span data-ttu-id="76e05-117">Bajo</span><span class="sxs-lookup"><span data-stu-id="76e05-117">Low</span></span>        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="76e05-118">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="76e05-118">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="76e05-119">Problema de seguimiento n.º 14257</span><span class="sxs-lookup"><span data-stu-id="76e05-119">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="76e05-120">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="76e05-120">**Old behavior**</span></span>

<span data-ttu-id="76e05-121">Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría.</span><span class="sxs-lookup"><span data-stu-id="76e05-121">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="76e05-122">Sin embargo, solo ha afectado a la creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="76e05-122">However, it only ever affected database creation.</span></span> <span data-ttu-id="76e05-123">No era necesario especificarlo para consultar los valores con dimensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="76e05-123">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="76e05-124">Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="76e05-124">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="76e05-125">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="76e05-125">**New behavior**</span></span>

<span data-ttu-id="76e05-126">Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="76e05-126">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="76e05-127">Esto coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="76e05-127">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="76e05-128">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="76e05-128">**Why**</span></span>

<span data-ttu-id="76e05-129">El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.</span><span class="sxs-lookup"><span data-stu-id="76e05-129">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="76e05-130">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="76e05-130">**Mitigations**</span></span>

<span data-ttu-id="76e05-131">Use `HasColumnType` para especificar la dimensión:</span><span class="sxs-lookup"><span data-stu-id="76e05-131">Use `HasColumnType` to specify the dimension:</span></span>

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="76e05-132">El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.</span><span class="sxs-lookup"><span data-stu-id="76e05-132">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="76e05-133">Incidencia de seguimiento n.º 17286</span><span class="sxs-lookup"><span data-stu-id="76e05-133">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="76e05-134">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="76e05-134">**Old behavior**</span></span>

<span data-ttu-id="76e05-135">Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias.</span><span class="sxs-lookup"><span data-stu-id="76e05-135">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="76e05-136">Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.</span><span class="sxs-lookup"><span data-stu-id="76e05-136">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="76e05-137">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="76e05-137">**New behavior**</span></span>

<span data-ttu-id="76e05-138">Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como necesaria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.</span><span class="sxs-lookup"><span data-stu-id="76e05-138">With the added support for required dependents it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="76e05-139">Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:</span><span class="sxs-lookup"><span data-stu-id="76e05-139">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="76e05-140">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="76e05-140">**Why**</span></span>

<span data-ttu-id="76e05-141">El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="76e05-141">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="76e05-142">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="76e05-142">**Mitigations**</span></span>

<span data-ttu-id="76e05-143">Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="76e05-143">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="76e05-144">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado.</span><span class="sxs-lookup"><span data-stu-id="76e05-144">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>

[<span data-ttu-id="76e05-145">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="76e05-145">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="76e05-146">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="76e05-146">**Old behavior**</span></span>

<span data-ttu-id="76e05-147">Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.</span><span class="sxs-lookup"><span data-stu-id="76e05-147">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="76e05-148">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="76e05-148">**New behavior**</span></span>

<span data-ttu-id="76e05-149">Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="76e05-149">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="76e05-150">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="76e05-150">**Why**</span></span>

<span data-ttu-id="76e05-151">Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.</span><span class="sxs-lookup"><span data-stu-id="76e05-151">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="76e05-152">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="76e05-152">**Mitigations**</span></span>

<span data-ttu-id="76e05-153">Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.</span><span class="sxs-lookup"><span data-stu-id="76e05-153">To prevent the value generator from being called assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="76e05-154">IMigrationsModelDiffer ahora usa IRelationalModel.</span><span class="sxs-lookup"><span data-stu-id="76e05-154">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="76e05-155">Incidencia de seguimiento n.º 20305</span><span class="sxs-lookup"><span data-stu-id="76e05-155">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="76e05-156">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="76e05-156">**Old behavior**</span></span>

<span data-ttu-id="76e05-157">`IMigrationsModelDiffer` API se definía mediante `IModel`.</span><span class="sxs-lookup"><span data-stu-id="76e05-157">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="76e05-158">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="76e05-158">**New behavior**</span></span>

<span data-ttu-id="76e05-159">`IMigrationsModelDiffer` API ahora usa `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="76e05-159">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="76e05-160">Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.</span><span class="sxs-lookup"><span data-stu-id="76e05-160">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="76e05-161">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="76e05-161">**Why**</span></span>

<span data-ttu-id="76e05-162">`IRelationalModel` es una representación recién agregada del esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="76e05-162">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="76e05-163">Su uso para encontrar diferencias es más rápido y preciso.</span><span class="sxs-lookup"><span data-stu-id="76e05-163">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="76e05-164">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="76e05-164">**Mitigations**</span></span>

<span data-ttu-id="76e05-165">Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:</span><span class="sxs-lookup"><span data-stu-id="76e05-165">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="76e05-166">Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="76e05-166">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a><span data-ttu-id="76e05-167">Los discriminadores son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="76e05-167">Discriminators are read-only</span></span>

[<span data-ttu-id="76e05-168">Incidencia de seguimiento n.º 21154</span><span class="sxs-lookup"><span data-stu-id="76e05-168">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="76e05-169">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="76e05-169">**Old behavior**</span></span>

<span data-ttu-id="76e05-170">Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="76e05-170">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="76e05-171">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="76e05-171">**New behavior**</span></span>

<span data-ttu-id="76e05-172">En el caso anterior, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="76e05-172">An exception will be throws in the above case.</span></span>

<span data-ttu-id="76e05-173">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="76e05-173">**Why**</span></span>

<span data-ttu-id="76e05-174">EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.</span><span class="sxs-lookup"><span data-stu-id="76e05-174">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state which might result in unexpected behavior.</span></span>

<span data-ttu-id="76e05-175">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="76e05-175">**Mitigations**</span></span>

<span data-ttu-id="76e05-176">Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:</span><span class="sxs-lookup"><span data-stu-id="76e05-176">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges` the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
