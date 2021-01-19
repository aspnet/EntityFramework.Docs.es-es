---
title: 'Cambios importantes en EF Core 5.0: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 5.0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 4a463e785edaceaf5dd96164c39e2cc9b5f86de4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128750"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="9989d-103">Cambios importantes en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="9989d-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="9989d-104">Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="9989d-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="9989d-105">Resumen</span><span class="sxs-lookup"><span data-stu-id="9989d-105">Summary</span></span>

| <span data-ttu-id="9989d-106">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="9989d-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="9989d-107">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="9989d-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="9989d-108">EF Core 5.0 no admite .NET Framework</span><span class="sxs-lookup"><span data-stu-id="9989d-108">EF Core 5.0 does not support .NET Framework</span></span>](#netstandard21)                                                                         | <span data-ttu-id="9989d-109">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-109">Medium</span></span>     |
| [<span data-ttu-id="9989d-110">IProperty.GetColumnName() está obsoleto</span><span class="sxs-lookup"><span data-stu-id="9989d-110">IProperty.GetColumnName() is now obsolete</span></span>](#getcolumnname-obsolete)                                                                  | <span data-ttu-id="9989d-111">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-111">Medium</span></span>     |
| [<span data-ttu-id="9989d-112">Los decimales necesitan precisión y escala</span><span class="sxs-lookup"><span data-stu-id="9989d-112">Precision and scale are required for decimals</span></span>](#decimals)                                                                            | <span data-ttu-id="9989d-113">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-113">Medium</span></span>     |
| <span data-ttu-id="9989d-114">[El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).</span><span class="sxs-lookup"><span data-stu-id="9989d-114">[Required on the navigation from principal to dependent has different semantics](#required-dependent)</span></span>                                 | <span data-ttu-id="9989d-115">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-115">Medium</span></span>     |
| [<span data-ttu-id="9989d-116">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="9989d-116">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="9989d-117">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-117">Medium</span></span>     |
| [<span data-ttu-id="9989d-118">Las consultas no sobrescriben las navegaciones de referencia no nula</span><span class="sxs-lookup"><span data-stu-id="9989d-118">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="9989d-119">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-119">Medium</span></span>     |
| [<span data-ttu-id="9989d-120">ToView() se trata de forma diferente en las migraciones</span><span class="sxs-lookup"><span data-stu-id="9989d-120">ToView() is treated differently by migrations</span></span>](#toview)                                                                              | <span data-ttu-id="9989d-121">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-121">Medium</span></span>     |
| [<span data-ttu-id="9989d-122">ToTable(null) marca el tipo de entidad como no asignado a una tabla</span><span class="sxs-lookup"><span data-stu-id="9989d-122">ToTable(null) marks the entity type as not mapped to a table</span></span>](#totable)                                                              | <span data-ttu-id="9989d-123">Media</span><span class="sxs-lookup"><span data-stu-id="9989d-123">Medium</span></span>     |
| [<span data-ttu-id="9989d-124">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="9989d-124">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="9989d-125">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-125">Low</span></span>        |
| [<span data-ttu-id="9989d-126">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="9989d-126">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="9989d-127">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-127">Low</span></span>        |
| [<span data-ttu-id="9989d-128">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="9989d-128">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="9989d-129">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-129">Low</span></span>        |
| <span data-ttu-id="9989d-130">[Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="9989d-130">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="9989d-131">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-131">Low</span></span>        |
| [<span data-ttu-id="9989d-132">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="9989d-132">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="9989d-133">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-133">Low</span></span>        |
| [<span data-ttu-id="9989d-134">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="9989d-134">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="9989d-135">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-135">Low</span></span>        |
| <span data-ttu-id="9989d-136">[IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).</span><span class="sxs-lookup"><span data-stu-id="9989d-136">[IMigrationsModelDiffer now uses IRelationalModel](#relational-model)</span></span>                                                                 | <span data-ttu-id="9989d-137">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-137">Low</span></span>        |
| <span data-ttu-id="9989d-138">[Los discriminadores son de solo lectura](#read-only-discriminators).</span><span class="sxs-lookup"><span data-stu-id="9989d-138">[Discriminators are read-only](#read-only-discriminators)</span></span>                                                                             | <span data-ttu-id="9989d-139">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-139">Low</span></span>        |
| [<span data-ttu-id="9989d-140">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory</span><span class="sxs-lookup"><span data-stu-id="9989d-140">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="9989d-141">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-141">Low</span></span>        |
| [<span data-ttu-id="9989d-142">IndexBuilder.HasName ahora está obsoleto</span><span class="sxs-lookup"><span data-stu-id="9989d-142">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="9989d-143">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-143">Low</span></span>        |
| [<span data-ttu-id="9989d-144">Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="9989d-144">A pluralizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="9989d-145">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-145">Low</span></span>        |
| [<span data-ttu-id="9989d-146">INavigationBase reemplaza a INavigation en algunas API para admitir la omisión de navegaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-146">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="9989d-147">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-147">Low</span></span>        |
| [<span data-ttu-id="9989d-148">Ya no se admiten algunas consultas con colecciones correlacionadas que también usan `Distinct` o `GroupBy`</span><span class="sxs-lookup"><span data-stu-id="9989d-148">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="9989d-149">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-149">Low</span></span>        |
| [<span data-ttu-id="9989d-150">No se admite el uso de una colección de tipo consultable en la proyección</span><span class="sxs-lookup"><span data-stu-id="9989d-150">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="9989d-151">Bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-151">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="9989d-152">Cambios de impacto medio</span><span class="sxs-lookup"><span data-stu-id="9989d-152">Medium-impact changes</span></span>

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a><span data-ttu-id="9989d-153">EF Core 5.0 no admite .NET Framework</span><span class="sxs-lookup"><span data-stu-id="9989d-153">EF Core 5.0 does not support .NET Framework</span></span>

[<span data-ttu-id="9989d-154">Problema de seguimiento n.º 15498</span><span class="sxs-lookup"><span data-stu-id="9989d-154">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-155">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-155">Old behavior</span></span>

<span data-ttu-id="9989d-156">EF Core 3.1 tiene como destino .NET Standard 2.0, que es compatible con .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="9989d-156">EF Core 3.1 targets .NET Standard 2.0, which is supported by .NET Framework.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-157">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-157">New behavior</span></span>

<span data-ttu-id="9989d-158">EF Core 5.0 tiene como destino .NET Standard 2.1, que no es compatible con .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="9989d-158">EF Core 5.0 targets .NET Standard 2.1, which is not supported by .NET Framework.</span></span> <span data-ttu-id="9989d-159">Esto significa que EF Core 5.0 no se puede usar con aplicaciones de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="9989d-159">This means EF Core 5.0 cannot be used with .NET Framework applications.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-160">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-160">Why</span></span>

<span data-ttu-id="9989d-161">Esto forma parte de un mayor movimiento entre los equipos de .NET destinado a la unificación en una sola plataforma .NET de destino.</span><span class="sxs-lookup"><span data-stu-id="9989d-161">This is part of the wider movement across .NET teams aimed at unification to a single .NET target framework.</span></span> <span data-ttu-id="9989d-162">Para obtener más información, vea [El futuro de .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span><span class="sxs-lookup"><span data-stu-id="9989d-162">For more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-163">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-163">Mitigations</span></span>

<span data-ttu-id="9989d-164">Las aplicaciones de .NET Framework pueden seguir usando EF Core 3.1, que es una [versión de soporte a largo plazo (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="9989d-164">.NET Framework applications can continue to use EF Core 3.1, which is a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="9989d-165">Como alternativa, las aplicaciones se pueden actualizar para que usen .NET Core 2.1, .NET Core 3.1 o .NET 5, que admiten .NET Standard 2.1.</span><span class="sxs-lookup"><span data-stu-id="9989d-165">Alternately, applications can be updated to use .NET Core 2.1, .NET Core 3.1, or .NET 5, all of which support .NET Standard 2.1.</span></span>

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a><span data-ttu-id="9989d-166">IProperty.GetColumnName() está obsoleto</span><span class="sxs-lookup"><span data-stu-id="9989d-166">IProperty.GetColumnName() is now obsolete</span></span>

[<span data-ttu-id="9989d-167">Incidencia de seguimiento n.º 2266</span><span class="sxs-lookup"><span data-stu-id="9989d-167">Tracking Issue #2266</span></span>](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-168">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-168">Old behavior</span></span>

<span data-ttu-id="9989d-169">`GetColumnName()` devolvía el nombre de la columna a la que se asigna una propiedad.</span><span class="sxs-lookup"><span data-stu-id="9989d-169">`GetColumnName()` returned the name of the column that a property is mapped to.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-170">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-170">New behavior</span></span>

<span data-ttu-id="9989d-171">`GetColumnName()` todavía devuelve el nombre de la columna a la que se asigna una propiedad, pero este comportamiento ahora es ambiguo, ya que EF Core 5 admite el modelo de tabla por tipo y la asignación simultánea a una vista o una función en la que estas asignaciones podrían usar nombres de columna diferentes para la misma propiedad.</span><span class="sxs-lookup"><span data-stu-id="9989d-171">`GetColumnName()` still returns the name of a column that a property is mapped to, but this behavior is now ambiguous since EF Core 5 supports TPT and simultaneous mapping to a view or a function where these mappings could use different column names for the same property.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-172">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-172">Why</span></span>

<span data-ttu-id="9989d-173">Hemos marcado este método como obsoleto para guiar a los usuarios a una sobrecarga más precisa: <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span><span class="sxs-lookup"><span data-stu-id="9989d-173">We marked this method as obsolete to guide users to a more accurate overload - <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-174">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-174">Mitigations</span></span>

<span data-ttu-id="9989d-175">Use el siguiente código para obtener el nombre de columna de una tabla específica:</span><span class="sxs-lookup"><span data-stu-id="9989d-175">Use the following code to get the column name for a specific table:</span></span>

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a><span data-ttu-id="9989d-176">Los decimales necesitan precisión y escala</span><span class="sxs-lookup"><span data-stu-id="9989d-176">Precision and scale are required for decimals</span></span>

[<span data-ttu-id="9989d-177">Incidencia de seguimiento n.º 19293</span><span class="sxs-lookup"><span data-stu-id="9989d-177">Tracking Issue #19293</span></span>](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-178">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-178">Old behavior</span></span>

<span data-ttu-id="9989d-179">Normalmente, en EF Core no se establecía la precisión y la escala en los objetos <xref:Microsoft.Data.SqlClient.SqlParameter>.</span><span class="sxs-lookup"><span data-stu-id="9989d-179">EF Core did not normally set precision and scale on <xref:Microsoft.Data.SqlClient.SqlParameter> objects.</span></span> <span data-ttu-id="9989d-180">Esto significa que la precisión y la escala completa se enviaban a SQL Server, donde se redondeaba en función de la precisión y la escala de la columna de base de datos.</span><span class="sxs-lookup"><span data-stu-id="9989d-180">This means the full precision and scale was sent to SQL Server, at which point SQL Server would round based on the precision and scale of the database column.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-181">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-181">New behavior</span></span>

<span data-ttu-id="9989d-182">Ahora en EF Core se establece la precisión y la escala de los parámetros con los valores configurados para las propiedades en el modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="9989d-182">EF Core now sets precision and scale on parameters using the values configured for properties in the EF Core model.</span></span> <span data-ttu-id="9989d-183">Esto significa que ahora el redondeo se produce en SqlClient.</span><span class="sxs-lookup"><span data-stu-id="9989d-183">This means rounding now happens in SqlClient.</span></span> <span data-ttu-id="9989d-184">Por tanto, si la precisión y la escala configuradas no coinciden con las de la base de datos, es posible que cambie el redondeo que se ve.</span><span class="sxs-lookup"><span data-stu-id="9989d-184">Consequentially, if the configured precision and scale do not match the database precision and scale, then the rounding seen may change.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-185">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-185">Why</span></span>

<span data-ttu-id="9989d-186">Para las características más recientes de SQL Server, incluida Always Encrypted, es necesario que las facetas de parámetro se especifiquen de forma completa.</span><span class="sxs-lookup"><span data-stu-id="9989d-186">Newer SQL Server features, including Always Encrypted, require that parameter facets are fully specified.</span></span> <span data-ttu-id="9989d-187">Además, SqlClient ha realizado un cambio para redondear los valores decimales en lugar de truncarlos, lo que coincide con el comportamiento de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9989d-187">In addition, SqlClient made a change to round instead of truncate decimal values, thereby matching the SQL Server behavior.</span></span> <span data-ttu-id="9989d-188">Esto ha hecho posible que EF Core establezca estas facetas sin cambiar el comportamiento de los decimales configurados correctamente.</span><span class="sxs-lookup"><span data-stu-id="9989d-188">This made it possible for EF Core to set these facets without changing the behavior for correctly configured decimals.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-189">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-189">Mitigations</span></span>

<span data-ttu-id="9989d-190">Asigne las propiedades decimales mediante un nombre de tipo que incluya precisión y escala.</span><span class="sxs-lookup"><span data-stu-id="9989d-190">Map your decimal properties using a type name that includes precision and scale.</span></span> <span data-ttu-id="9989d-191">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9989d-191">For example:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

<span data-ttu-id="9989d-192">O bien, use `HasPrecision` en las API de creación de modelos.</span><span class="sxs-lookup"><span data-stu-id="9989d-192">Or use `HasPrecision` in the model building APIs.</span></span> <span data-ttu-id="9989d-193">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9989d-193">For example:</span></span>

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="9989d-194">El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.</span><span class="sxs-lookup"><span data-stu-id="9989d-194">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="9989d-195">Incidencia de seguimiento n.º 17286</span><span class="sxs-lookup"><span data-stu-id="9989d-195">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-196">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-196">Old behavior</span></span>

<span data-ttu-id="9989d-197">Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias.</span><span class="sxs-lookup"><span data-stu-id="9989d-197">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="9989d-198">Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.</span><span class="sxs-lookup"><span data-stu-id="9989d-198">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-199">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-199">New behavior</span></span>

<span data-ttu-id="9989d-200">Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como obligatoria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.</span><span class="sxs-lookup"><span data-stu-id="9989d-200">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="9989d-201">Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:</span><span class="sxs-lookup"><span data-stu-id="9989d-201">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="9989d-202">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-202">Why</span></span>

<span data-ttu-id="9989d-203">El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="9989d-203">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-204">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-204">Mitigations</span></span>

<span data-ttu-id="9989d-205">Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="9989d-205">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="9989d-206">La definición de la consulta se reemplaza por métodos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="9989d-206">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="9989d-207">Problema de seguimiento n.º 18903</span><span class="sxs-lookup"><span data-stu-id="9989d-207">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-208">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-208">Old behavior</span></span>

<span data-ttu-id="9989d-209">Los tipos de entidad se asignaban en la definición de consultas en el nivel básico.</span><span class="sxs-lookup"><span data-stu-id="9989d-209">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="9989d-210">Cuando el tipo de entidad se usaba en la raíz de la consulta del tipo de entidad se sustituía por la consulta de definición de cualquier proveedor.</span><span class="sxs-lookup"><span data-stu-id="9989d-210">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-211">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-211">New behavior</span></span>

<span data-ttu-id="9989d-212">Las API de definición de consulta han quedado en desuso.</span><span class="sxs-lookup"><span data-stu-id="9989d-212">APIs for defining query are deprecated.</span></span> <span data-ttu-id="9989d-213">Se introdujeron nuevas API específicas del proveedor.</span><span class="sxs-lookup"><span data-stu-id="9989d-213">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-214">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-214">Why</span></span>

<span data-ttu-id="9989d-215">Aunque la definición de consulta se implementaba como consulta de reemplazo siempre que se usaba la raíz de la consulta en la consulta, tenía algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="9989d-215">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="9989d-216">Si la definición de consulta establece la proyección del tipo de entidad con `new { ... }` en el método `Select`, su identificación como entidad requería trabajo adicional y lo hacía incoherente con el modo en que EF Core trata los tipos nominales en la consulta.</span><span class="sxs-lookup"><span data-stu-id="9989d-216">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="9989d-217">En el caso de los proveedores relacionales `FromSql` todavía hay que pasar la cadena SQL en forma de expresión LINQ.</span><span class="sxs-lookup"><span data-stu-id="9989d-217">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="9989d-218">Las definiciones de consulta se introdujeron inicialmente como vistas del lado cliente para su uso con el proveedor en memoria para las entidades sin clave (similares a las vistas de base de datos de las bases de datos relacionales).</span><span class="sxs-lookup"><span data-stu-id="9989d-218">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="9989d-219">Esta definición facilita la prueba de la aplicación en la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="9989d-219">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="9989d-220">Después, se convirtieron en ampliamente aplicables, lo que resultaba útil pero no era coherente y resultaba difícil de entender.</span><span class="sxs-lookup"><span data-stu-id="9989d-220">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="9989d-221">Por tanto, decidimos simplificar el concepto.</span><span class="sxs-lookup"><span data-stu-id="9989d-221">So we decided to simplify the concept.</span></span> <span data-ttu-id="9989d-222">Hemos creado una consulta de definición basada en LINQ exclusiva para el proveedor en memoria, que tratamos de otra forma.</span><span class="sxs-lookup"><span data-stu-id="9989d-222">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="9989d-223">Para más información, consulte [este problema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="9989d-223">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-224">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-224">Mitigations</span></span>

<span data-ttu-id="9989d-225">En el caso de los proveedores relacionales, use el método `ToSqlQuery` en `OnModelCreating` y pase una cadena SQL que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="9989d-225">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="9989d-226">En el caso del proveedor en memoria, use el método `ToInMemoryQuery` en `OnModelCreating` y pase una consulta LINQ que se utilice como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="9989d-226">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="9989d-227">Las consultas no sobrescriben las navegaciones de referencia no nula</span><span class="sxs-lookup"><span data-stu-id="9989d-227">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="9989d-228">Incidencia de seguimiento n.º 2693</span><span class="sxs-lookup"><span data-stu-id="9989d-228">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-229">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-229">Old behavior</span></span>

<span data-ttu-id="9989d-230">En EF Core 3.1, las instancias de la entidad de la base de datos sobrescribirán las navegaciones de referencia que se inicializan de manera diligente en valores que no sean NULL, con independencia de si coinciden o no los valores de claves.</span><span class="sxs-lookup"><span data-stu-id="9989d-230">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="9989d-231">Sin embargo, en otros casos, EF Core 3.1 haría lo contrario y dejaría el valor no NULL existente.</span><span class="sxs-lookup"><span data-stu-id="9989d-231">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-232">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-232">New behavior</span></span>

<span data-ttu-id="9989d-233">A partir de EF Core 5.0, las instancias devueltas por una consulta nunca sobrescriben las navegaciones de referencia no NULL.</span><span class="sxs-lookup"><span data-stu-id="9989d-233">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="9989d-234">Tenga en cuenta que todavía se admite la inicialización diligente de una navegación de _colección_ en una colección vacía.</span><span class="sxs-lookup"><span data-stu-id="9989d-234">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-235">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-235">Why</span></span>

<span data-ttu-id="9989d-236">La inicialización de una propiedad de navegación de referencia en una instancia de entidad "vacía" da como resultado un estado ambiguo.</span><span class="sxs-lookup"><span data-stu-id="9989d-236">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="9989d-237">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9989d-237">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="9989d-238">Normalmente, una consulta de blogs y autores primero creará instancias `Blog` y, después, establecerá las instancias `Author` apropiadas en función de los datos devueltos por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9989d-238">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="9989d-239">Sin embargo, en este caso, cada propiedad `Blog.Author` ya se ha inicializado en una instancia `Author` vacía.</span><span class="sxs-lookup"><span data-stu-id="9989d-239">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="9989d-240">Excepto EF Core, no tiene ninguna manera de saber que esta instancia está "vacía".</span><span class="sxs-lookup"><span data-stu-id="9989d-240">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="9989d-241">Por lo tanto, si se sobrescribe esta instancia, es posible que se elimine de forma silenciosa una instancia `Author` válida.</span><span class="sxs-lookup"><span data-stu-id="9989d-241">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="9989d-242">Por lo tanto, EF Core 5.0 ahora no sobrescribe de forma coherente una navegación que ya está inicializada.</span><span class="sxs-lookup"><span data-stu-id="9989d-242">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="9989d-243">Este nuevo comportamiento también se alinea con el comportamiento de EF6 en la mayoría de los casos, aunque en la investigación también encontramos algunos casos de incoherencia en EF6.</span><span class="sxs-lookup"><span data-stu-id="9989d-243">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-244">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-244">Mitigations</span></span>

<span data-ttu-id="9989d-245">Si se detecta esta interrupción, la solución consiste en detener la inicialización diligente de las propiedades de navegación de referencia.</span><span class="sxs-lookup"><span data-stu-id="9989d-245">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a><span data-ttu-id="9989d-246">ToView() se trata de forma diferente en las migraciones</span><span class="sxs-lookup"><span data-stu-id="9989d-246">ToView() is treated differently by migrations</span></span>

[<span data-ttu-id="9989d-247">Incidencia de seguimiento n.º 2725</span><span class="sxs-lookup"><span data-stu-id="9989d-247">Tracking Issue #2725</span></span>](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-248">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-248">Old behavior</span></span>

<span data-ttu-id="9989d-249">Las llamadas a `ToView(string)` hacían que las migraciones omitieran el tipo de entidad y lo asignaran a una vista.</span><span class="sxs-lookup"><span data-stu-id="9989d-249">Calling `ToView(string)` made the migrations ignore the entity type in addition to mapping it to a view.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-250">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-250">New behavior</span></span>

<span data-ttu-id="9989d-251">Ahora `ToView(string)` marca el tipo de entidad como no asignado a una tabla y lo asigna a una vista.</span><span class="sxs-lookup"><span data-stu-id="9989d-251">Now `ToView(string)` marks the entity type as not mapped to a table in addition to mapping it to a view.</span></span> <span data-ttu-id="9989d-252">Como resultado, la primera migración después de actualizar a EF Core 5 intenta quitar la tabla predeterminada para este tipo de entidad, puesto que ya no se omite.</span><span class="sxs-lookup"><span data-stu-id="9989d-252">This results in the first migration after upgrading to EF Core 5 to try to drop the default table for this entity type as it's not longer ignored.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-253">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-253">Why</span></span>

<span data-ttu-id="9989d-254">EF Core ahora permite asignar un tipo de entidad a una tabla y una vista simultáneamente, de modo que `ToView` ya no es un indicador válido que las migraciones deben omitir.</span><span class="sxs-lookup"><span data-stu-id="9989d-254">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToView` is no longer a valid indicator that it should be ignored by migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-255">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-255">Mitigations</span></span>

<span data-ttu-id="9989d-256">Use el siguiente código para marcar la tabla asignada como excluida de las migraciones:</span><span class="sxs-lookup"><span data-stu-id="9989d-256">Use the following code to mark the mapped table as excluded from migrations:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a><span data-ttu-id="9989d-257">ToTable(null) marca el tipo de entidad como no asignado a una tabla</span><span class="sxs-lookup"><span data-stu-id="9989d-257">ToTable(null) marks the entity type as not mapped to a table</span></span>

[<span data-ttu-id="9989d-258">Incidencia de seguimiento n.º 21172</span><span class="sxs-lookup"><span data-stu-id="9989d-258">Tracking Issue #21172</span></span>](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-259">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-259">Old behavior</span></span>

<span data-ttu-id="9989d-260">`ToTable(null)` restablecía el nombre de la tabla al valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9989d-260">`ToTable(null)` would reset the table name to the default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-261">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-261">New behavior</span></span>

<span data-ttu-id="9989d-262">Ahora, `ToTable(null)` marca el tipo de entidad como no asignado a ninguna tabla.</span><span class="sxs-lookup"><span data-stu-id="9989d-262">`ToTable(null)` now marks the entity type as not mapped to any table.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-263">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-263">Why</span></span>

<span data-ttu-id="9989d-264">EF Core ahora permite asignar un tipo de entidad a una tabla y una vista simultáneamente, de modo que `ToTable(null)` se usa para indicar que no está asignado a ninguna tabla.</span><span class="sxs-lookup"><span data-stu-id="9989d-264">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToTable(null)` is used to indicate that it isn't mapped to any table.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-265">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-265">Mitigations</span></span>

<span data-ttu-id="9989d-266">Use el siguiente código para restablecer el nombre de la tabla al valor predeterminado si no está asignado a una vista o una función DbFunction:</span><span class="sxs-lookup"><span data-stu-id="9989d-266">Use the following code to reset the table name to the default if it's not mapped to a view or a DbFunction:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a><span data-ttu-id="9989d-267">Cambios de impacto bajo</span><span class="sxs-lookup"><span data-stu-id="9989d-267">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="9989d-268">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="9989d-268">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="9989d-269">Problema de seguimiento n.º 14257</span><span class="sxs-lookup"><span data-stu-id="9989d-269">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-270">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-270">Old behavior</span></span>

<span data-ttu-id="9989d-271">Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría.</span><span class="sxs-lookup"><span data-stu-id="9989d-271">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="9989d-272">Sin embargo, solo ha afectado a la creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9989d-272">However, it only ever affected database creation.</span></span> <span data-ttu-id="9989d-273">No era necesario especificarlo para consultar los valores con dimensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="9989d-273">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="9989d-274">Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="9989d-274">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-275">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-275">New behavior</span></span>

<span data-ttu-id="9989d-276">Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="9989d-276">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="9989d-277">Esta API coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="9989d-277">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-278">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-278">Why</span></span>

<span data-ttu-id="9989d-279">El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.</span><span class="sxs-lookup"><span data-stu-id="9989d-279">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-280">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-280">Mitigations</span></span>

<span data-ttu-id="9989d-281">Use `HasColumnType` para especificar la dimensión:</span><span class="sxs-lookup"><span data-stu-id="9989d-281">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="9989d-282">Cosmos: la clave de partición se ha agregado ahora a la clave principal</span><span class="sxs-lookup"><span data-stu-id="9989d-282">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="9989d-283">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="9989d-283">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-284">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-284">Old behavior</span></span>

<span data-ttu-id="9989d-285">La propiedad de clave de partición solo se agregaba a la clave alternativa que incluye `id`.</span><span class="sxs-lookup"><span data-stu-id="9989d-285">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-286">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-286">New behavior</span></span>

<span data-ttu-id="9989d-287">La propiedad de clave de partición ahora también se agrega por convención a la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9989d-287">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-288">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-288">Why</span></span>

<span data-ttu-id="9989d-289">Este cambio hace que el modelo se alinee mejor con la semántica de Azure Cosmos DB y mejora el rendimiento de `Find` y algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="9989d-289">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-290">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-290">Mitigations</span></span>

<span data-ttu-id="9989d-291">Para evitar que la propiedad de clave de partición se agregue a la clave principal, configúrela en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="9989d-291">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="9989d-292">Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`</span><span class="sxs-lookup"><span data-stu-id="9989d-292">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="9989d-293">Problema de seguimiento n.º 17751</span><span class="sxs-lookup"><span data-stu-id="9989d-293">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-294">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-294">Old behavior</span></span>

<span data-ttu-id="9989d-295">La propiedad Shadow asignada a la propiedad `id` de JSON también se llama `id`.</span><span class="sxs-lookup"><span data-stu-id="9989d-295">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-296">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-296">New behavior</span></span>

<span data-ttu-id="9989d-297">La propiedad Shadow creada por convención se denomina ahora `__id`.</span><span class="sxs-lookup"><span data-stu-id="9989d-297">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-298">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-298">Why</span></span>

<span data-ttu-id="9989d-299">Este cambio hace menos probable que la propiedad `id` entre en conflicto con una propiedad existente en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="9989d-299">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-300">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-300">Mitigations</span></span>

<span data-ttu-id="9989d-301">Para volver al comportamiento de la versión 3.x, configure la propiedad `id` en `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="9989d-301">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="9989d-302">Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números</span><span class="sxs-lookup"><span data-stu-id="9989d-302">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="9989d-303">Problema de seguimiento n.º 17306</span><span class="sxs-lookup"><span data-stu-id="9989d-303">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-304">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-304">Old behavior</span></span>

<span data-ttu-id="9989d-305">Las propiedades de tipo byte[] se almacenaban como matriz de números.</span><span class="sxs-lookup"><span data-stu-id="9989d-305">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-306">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-306">New behavior</span></span>

<span data-ttu-id="9989d-307">Las propiedades de tipo byte[] se almacenan ahora como cadena Base64.</span><span class="sxs-lookup"><span data-stu-id="9989d-307">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-308">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-308">Why</span></span>

<span data-ttu-id="9989d-309">Esta representación de byte [] se alinea mejor con las expectativas y es el comportamiento predeterminado de las principales bibliotecas de serialización de JSON.</span><span class="sxs-lookup"><span data-stu-id="9989d-309">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-310">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-310">Mitigations</span></span>

<span data-ttu-id="9989d-311">Los datos existentes almacenados como matrices de números se seguirán consultando correctamente, pero actualmente no hay ninguna manera compatible de volver a cambiar el comportamiento de inserción.</span><span class="sxs-lookup"><span data-stu-id="9989d-311">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="9989d-312">Si esta limitación bloquea su escenario, haga un comentario en [este problema](https://github.com/dotnet/efcore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="9989d-312">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="9989d-313">Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName</span><span class="sxs-lookup"><span data-stu-id="9989d-313">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="9989d-314">Problema de seguimiento n.º 17874</span><span class="sxs-lookup"><span data-stu-id="9989d-314">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-315">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-315">Old behavior</span></span>

<span data-ttu-id="9989d-316">Anteriormente, se llamaba a los métodos de extensión `GetPropertyName` y `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="9989d-316">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-317">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-317">New behavior</span></span>

<span data-ttu-id="9989d-318">La API antigua se ha quitado y se han agregado métodos nuevos: `GetJsonPropertyName` y `SetJsonPropertyName`.</span><span class="sxs-lookup"><span data-stu-id="9989d-318">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-319">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-319">Why</span></span>

<span data-ttu-id="9989d-320">Este cambio elimina la ambigüedad en torno a lo que estos métodos configuran.</span><span class="sxs-lookup"><span data-stu-id="9989d-320">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-321">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-321">Mitigations</span></span>

<span data-ttu-id="9989d-322">Use la API nueva.</span><span class="sxs-lookup"><span data-stu-id="9989d-322">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="9989d-323">Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado</span><span class="sxs-lookup"><span data-stu-id="9989d-323">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="9989d-324">Incidencia de seguimiento n.º 15289</span><span class="sxs-lookup"><span data-stu-id="9989d-324">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-325">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-325">Old behavior</span></span>

<span data-ttu-id="9989d-326">Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.</span><span class="sxs-lookup"><span data-stu-id="9989d-326">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-327">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-327">New behavior</span></span>

<span data-ttu-id="9989d-328">Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="9989d-328">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-329">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-329">Why</span></span>

<span data-ttu-id="9989d-330">Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.</span><span class="sxs-lookup"><span data-stu-id="9989d-330">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-331">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-331">Mitigations</span></span>

<span data-ttu-id="9989d-332">Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.</span><span class="sxs-lookup"><span data-stu-id="9989d-332">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="9989d-333">IMigrationsModelDiffer ahora usa IRelationalModel.</span><span class="sxs-lookup"><span data-stu-id="9989d-333">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="9989d-334">Incidencia de seguimiento n.º 20305</span><span class="sxs-lookup"><span data-stu-id="9989d-334">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-335">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-335">Old behavior</span></span>

<span data-ttu-id="9989d-336">`IMigrationsModelDiffer` API se definía mediante `IModel`.</span><span class="sxs-lookup"><span data-stu-id="9989d-336">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-337">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-337">New behavior</span></span>

<span data-ttu-id="9989d-338">`IMigrationsModelDiffer` API ahora usa `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="9989d-338">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="9989d-339">Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.</span><span class="sxs-lookup"><span data-stu-id="9989d-339">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-340">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-340">Why</span></span>

<span data-ttu-id="9989d-341">`IRelationalModel` es una representación recién agregada del esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9989d-341">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="9989d-342">Su uso para encontrar diferencias es más rápido y preciso.</span><span class="sxs-lookup"><span data-stu-id="9989d-342">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-343">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-343">Mitigations</span></span>

<span data-ttu-id="9989d-344">Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:</span><span class="sxs-lookup"><span data-stu-id="9989d-344">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="9989d-345">Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="9989d-345">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="9989d-346">Los discriminadores son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="9989d-346">Discriminators are read-only</span></span>

[<span data-ttu-id="9989d-347">Incidencia de seguimiento n.º 21154</span><span class="sxs-lookup"><span data-stu-id="9989d-347">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-348">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-348">Old behavior</span></span>

<span data-ttu-id="9989d-349">Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="9989d-349">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-350">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-350">New behavior</span></span>

<span data-ttu-id="9989d-351">En el caso anterior, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="9989d-351">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-352">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-352">Why</span></span>

<span data-ttu-id="9989d-353">EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.</span><span class="sxs-lookup"><span data-stu-id="9989d-353">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-354">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-354">Mitigations</span></span>

<span data-ttu-id="9989d-355">Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:</span><span class="sxs-lookup"><span data-stu-id="9989d-355">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="9989d-356">Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="9989d-356">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="9989d-357">Incidencia de seguimiento n.º 20294</span><span class="sxs-lookup"><span data-stu-id="9989d-357">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-358">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-358">Old behavior</span></span>

<span data-ttu-id="9989d-359">Los métodos EF.Functions específicos del proveedor incluían la implementación para la ejecución del cliente, lo cual les permitía ejecutarse en el proveedor de InMemory.</span><span class="sxs-lookup"><span data-stu-id="9989d-359">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="9989d-360">Por ejemplo, `EF.Functions.DateDiffDay` es un método específico de SQL Server que funcionaba en el proveedor InMemory.</span><span class="sxs-lookup"><span data-stu-id="9989d-360">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-361">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-361">New behavior</span></span>

<span data-ttu-id="9989d-362">Los métodos específicos del proveedor se han actualizado para producir una excepción en el cuerpo del método a fin de bloquear su evaluación en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="9989d-362">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-363">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-363">Why</span></span>

<span data-ttu-id="9989d-364">Los métodos específicos del proveedor se asignan a una función de base de datos.</span><span class="sxs-lookup"><span data-stu-id="9989d-364">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="9989d-365">El cálculo realizado por la función de base de datos asignada no siempre se puede replicar en el lado cliente en LINQ.</span><span class="sxs-lookup"><span data-stu-id="9989d-365">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="9989d-366">Esto puede provocar que el resultado del servidor sea diferente al ejecutar el mismo método en el cliente.</span><span class="sxs-lookup"><span data-stu-id="9989d-366">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="9989d-367">Dado que estos métodos se usan en LINQ para traducir a funciones específicas de base de datos, no es necesario que se evalúen en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="9989d-367">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="9989d-368">Dado que el proveedor InMemory es una base de datos *diferente*, estos métodos no están disponibles para este proveedor.</span><span class="sxs-lookup"><span data-stu-id="9989d-368">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="9989d-369">Al intentar ejecutarlos para el proveedor InMemory o cualquier otro proveedor que no traduzca estos métodos, se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="9989d-369">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-370">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-370">Mitigations</span></span>

<span data-ttu-id="9989d-371">Dado que no hay forma de imitar el comportamiento de las funciones de base de datos con precisión, debe probar las consultas que las contienen en el mismo tipo de base de datos que en producción.</span><span class="sxs-lookup"><span data-stu-id="9989d-371">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="9989d-372">IndexBuilder.HasName ahora está obsoleto</span><span class="sxs-lookup"><span data-stu-id="9989d-372">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="9989d-373">Incidencia de seguimiento n.º 21089</span><span class="sxs-lookup"><span data-stu-id="9989d-373">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-374">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-374">Old behavior</span></span>

<span data-ttu-id="9989d-375">Anteriormente, solo se podía definir un índice en un conjunto determinado de propiedades.</span><span class="sxs-lookup"><span data-stu-id="9989d-375">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="9989d-376">El nombre de la base de datos de un índice se configuró mediante IndexBuilder.HasName.</span><span class="sxs-lookup"><span data-stu-id="9989d-376">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-377">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-377">New behavior</span></span>

<span data-ttu-id="9989d-378">Ahora se permiten varios índices en el mismo conjunto o las mismas propiedades.</span><span class="sxs-lookup"><span data-stu-id="9989d-378">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="9989d-379">Ahora, estos índices se distinguen por un nombre en el modelo.</span><span class="sxs-lookup"><span data-stu-id="9989d-379">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="9989d-380">Por convención, el nombre del modelo se utiliza como nombre de la base de datos; sin embargo, también se puede configurar de forma independiente utilizando HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="9989d-380">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-381">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-381">Why</span></span>

<span data-ttu-id="9989d-382">En el futuro, nos gustaría habilitar índices ascendentes y descendentes o índices con distintas intercalaciones en el mismo conjunto de propiedades.</span><span class="sxs-lookup"><span data-stu-id="9989d-382">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="9989d-383">Este cambio nos hace dar otro paso en esa dirección.</span><span class="sxs-lookup"><span data-stu-id="9989d-383">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-384">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-384">Mitigations</span></span>

<span data-ttu-id="9989d-385">Cualquier código que llamara anteriormente a IndexBuilder.HasName debe actualizarse para llamar a HasDatabaseName en su lugar.</span><span class="sxs-lookup"><span data-stu-id="9989d-385">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="9989d-386">Si su proyecto incluye migraciones generadas antes de la versión 2.0.0 de EF Core, puede ignorar la advertencia en esos archivos de forma segura y suprimirla agregando `#pragma warning disable 612, 618`.</span><span class="sxs-lookup"><span data-stu-id="9989d-386">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="9989d-387">Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="9989d-387">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="9989d-388">Incidencia de seguimiento n.º 11160</span><span class="sxs-lookup"><span data-stu-id="9989d-388">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-389">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-389">Old behavior</span></span>

<span data-ttu-id="9989d-390">Anteriormente, tenía que instalar un paquete de pluralizador independiente para pluralizar los nombres de navegación de colección y DbSet y singularizar los nombres de tabla al aplicar scaffolding a DbContext y los tipos de entidad mediante la utilización de técnicas de ingeniería inversa en un esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="9989d-390">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-391">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-391">New behavior</span></span>

<span data-ttu-id="9989d-392">EF Core incluye ahora un pluralizador que usa la biblioteca [Humanizer](https://github.com/Humanizr/Humanizer).</span><span class="sxs-lookup"><span data-stu-id="9989d-392">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="9989d-393">Se trata de la misma biblioteca que Visual Studio usa para recomendar nombres de variable.</span><span class="sxs-lookup"><span data-stu-id="9989d-393">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-394">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-394">Why</span></span>

<span data-ttu-id="9989d-395">El uso de formas plurales de palabras para las propiedades de colección y de formas singulares para los tipos y las propiedades de referencia es idiomático en .NET.</span><span class="sxs-lookup"><span data-stu-id="9989d-395">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-396">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-396">Mitigations</span></span>

<span data-ttu-id="9989d-397">Para deshabilitar el pluralizador, use la opción `--no-pluralize` en `dotnet ef dbcontext scaffold` o el modificador `-NoPluralize` en `Scaffold-DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9989d-397">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="9989d-398">INavigationBase reemplaza a INavigation en algunas API para admitir la omisión de navegaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-398">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="9989d-399">Incidencia de seguimiento n.º 2568</span><span class="sxs-lookup"><span data-stu-id="9989d-399">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="9989d-400">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="9989d-400">Old behavior</span></span>

<span data-ttu-id="9989d-401">Las versiones de EF Core anteriores a la 5.0 solo admiten una forma de propiedad de navegación, representada por la interfaz `INavigation`.</span><span class="sxs-lookup"><span data-stu-id="9989d-401">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="9989d-402">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="9989d-402">New behavior</span></span>

<span data-ttu-id="9989d-403">EF Core 5.0 presenta relaciones varios a varios que usan las "navegaciones por omisión".</span><span class="sxs-lookup"><span data-stu-id="9989d-403">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="9989d-404">Se representan mediante la interfaz `ISkipNavigation`, y la mayor parte de la funcionalidad de `INavigation` se ha insertado en una interfaz base común: `INavigationBase`.</span><span class="sxs-lookup"><span data-stu-id="9989d-404">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="9989d-405">Por qué</span><span class="sxs-lookup"><span data-stu-id="9989d-405">Why</span></span>

<span data-ttu-id="9989d-406">La mayor parte de la funcionalidad entre las navegaciones normal y por omisión es la misma.</span><span class="sxs-lookup"><span data-stu-id="9989d-406">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="9989d-407">Sin embargo, las navegaciones por omisión tienen una relación diferente con las claves externas con respecto a las navegaciones normales, ya que las claves externas implicadas no están directamente en ninguno de los extremos de la relación, sino en la entidad join.</span><span class="sxs-lookup"><span data-stu-id="9989d-407">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="9989d-408">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="9989d-408">Mitigations</span></span>

<span data-ttu-id="9989d-409">En muchos casos, las aplicaciones pueden cambiar al uso de la nueva interfaz base sin realizar ningún otro cambio.</span><span class="sxs-lookup"><span data-stu-id="9989d-409">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="9989d-410">Sin embargo, en los casos en los que se usa la navegación para acceder a las propiedades de clave externa, el código de aplicación se debe restringir solo a las navegaciones normales, o bien actualizarse para hacer lo adecuado para las navegaciones normal y por omisión.</span><span class="sxs-lookup"><span data-stu-id="9989d-410">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="9989d-411">Ya no se admiten algunas consultas con colecciones correlacionadas que también usan `Distinct` o `GroupBy`</span><span class="sxs-lookup"><span data-stu-id="9989d-411">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="9989d-412">Incidencia de seguimiento n.º 15873</span><span class="sxs-lookup"><span data-stu-id="9989d-412">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="9989d-413">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="9989d-413">**Old behavior**</span></span>

<span data-ttu-id="9989d-414">Anteriormente, las consultas que implicaban colecciones correlacionadas seguidas de `GroupBy`, así como algunas consultas con `Distinct` que permitimos ejecutar.</span><span class="sxs-lookup"><span data-stu-id="9989d-414">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="9989d-415">Ejemplo de GroupBy:</span><span class="sxs-lookup"><span data-stu-id="9989d-415">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="9989d-416">Ejemplo de `Distinct`: en concreto, las consultas `Distinct` en las que la proyección de la colección interna no contiene la clave principal:</span><span class="sxs-lookup"><span data-stu-id="9989d-416">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="9989d-417">Estas consultas podrían devolver resultados incorrectos si la colección interna contuviera duplicados, pero funcionaría correctamente si todos los elementos de la colección interna fueran únicos.</span><span class="sxs-lookup"><span data-stu-id="9989d-417">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="9989d-418">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="9989d-418">**New behavior**</span></span>

<span data-ttu-id="9989d-419">Estas consultas ya no son compatibles.</span><span class="sxs-lookup"><span data-stu-id="9989d-419">These queries are no longer supported.</span></span> <span data-ttu-id="9989d-420">Se produce una excepción que indica que no hay suficiente información para compilar los resultados correctamente.</span><span class="sxs-lookup"><span data-stu-id="9989d-420">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="9989d-421">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="9989d-421">**Why**</span></span>

<span data-ttu-id="9989d-422">En el caso de los escenarios de colecciones correlacionadas, es necesario conocer la clave principal de la entidad para asignar entidades de colección al elemento primario correcto.</span><span class="sxs-lookup"><span data-stu-id="9989d-422">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="9989d-423">Cuando la colección interna no utiliza `GroupBy` ni `Distinct`, la clave principal que falta se puede agregar simplemente a la proyección.</span><span class="sxs-lookup"><span data-stu-id="9989d-423">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="9989d-424">Sin embargo, en el caso de `GroupBy` y `Distinct`, no se puede hacer porque cambiaría el resultado de la operación `GroupBy` o `Distinct`.</span><span class="sxs-lookup"><span data-stu-id="9989d-424">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="9989d-425">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="9989d-425">**Mitigations**</span></span>

<span data-ttu-id="9989d-426">Vuelva a escribir la consulta para que no use las operaciones `GroupBy` o `Distinct` en la colección interna y, en su lugar, realice estas operaciones en el cliente.</span><span class="sxs-lookup"><span data-stu-id="9989d-426">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="9989d-427">No se admite el uso de una colección de tipo consultable en la proyección</span><span class="sxs-lookup"><span data-stu-id="9989d-427">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="9989d-428">Incidencia de seguimiento n.º 16314</span><span class="sxs-lookup"><span data-stu-id="9989d-428">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="9989d-429">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="9989d-429">**Old behavior**</span></span>

<span data-ttu-id="9989d-430">Anteriormente, era posible usar la colección de un tipo consultable dentro de la proyección en algunos casos, por ejemplo, como un argumento de un constructor `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="9989d-430">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="9989d-431">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="9989d-431">**New behavior**</span></span>

<span data-ttu-id="9989d-432">Estas consultas ya no son compatibles.</span><span class="sxs-lookup"><span data-stu-id="9989d-432">These queries are no longer supported.</span></span> <span data-ttu-id="9989d-433">Se produce una excepción que indica que no se puede crear un objeto de tipo consultable y, además, se sugiere cómo corregir este problema.</span><span class="sxs-lookup"><span data-stu-id="9989d-433">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="9989d-434">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="9989d-434">**Why**</span></span>

<span data-ttu-id="9989d-435">No se puede materializar un objeto de un tipo consultable, por lo que, en su lugar, se creará automáticamente con el tipo `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="9989d-435">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="9989d-436">Esto suele provocar una excepción debido a una falta de coincidencia de tipos que no era muy clara y podría ser sorprendente para algunos usuarios.</span><span class="sxs-lookup"><span data-stu-id="9989d-436">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="9989d-437">Decidimos reconocer el patrón y producir una excepción más significativa.</span><span class="sxs-lookup"><span data-stu-id="9989d-437">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="9989d-438">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="9989d-438">**Mitigations**</span></span>

<span data-ttu-id="9989d-439">Agregue la llamada a `ToList()` después del objeto consultable en la proyección:</span><span class="sxs-lookup"><span data-stu-id="9989d-439">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
