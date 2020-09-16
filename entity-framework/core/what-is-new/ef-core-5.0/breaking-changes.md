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
# <a name="breaking-changes-in-ef-core-50"></a>Cambios importantes en EF Core 5.0

Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.

## <a name="summary"></a>Resumen

| **Cambio importante**                                                                                                                   | **Impacto** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).                                 | Media     |
| [Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS](#geometric-sqlite)                                                   | Bajo        |
| [Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado](#non-added-generation).  | Bajo        |
| [IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).                                                                 | Bajo        |
| [Los discriminadores son de solo lectura](#read-only-discriminators).                                                                             | Bajo        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS

[Problema de seguimiento n.º 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Comportamiento anterior**

Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría. Sin embargo, solo ha afectado a la creación de la base de datos. No era necesario especificarlo para consultar los valores con dimensiones adicionales. Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Comportamiento nuevo**

Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna. Esto coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.

**Por qué**

El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.

**Mitigaciones**

Use `HasColumnType` para especificar la dimensión:

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.

[Incidencia de seguimiento n.º 17286](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**Comportamiento anterior**

Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias. Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.

**Comportamiento nuevo**

Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como necesaria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.

Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**Por qué**

El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).

**Mitigaciones**

Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado.

[Incidencia de seguimiento n.º 15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Comportamiento anterior**

Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.

**Comportamiento nuevo**

Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.

**Por qué**

Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.

**Mitigaciones**

Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer ahora usa IRelationalModel.

[Incidencia de seguimiento n.º 20305](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**Comportamiento anterior**

`IMigrationsModelDiffer` API se definía mediante `IModel`.

**Comportamiento nuevo**

`IMigrationsModelDiffer` API ahora usa `IRelationalModel`. Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.

**Por qué**

`IRelationalModel` es una representación recién agregada del esquema de la base de datos. Su uso para encontrar diferencias es más rápido y preciso.

**Mitigaciones**

Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:

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

Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a>Los discriminadores son de solo lectura.

[Incidencia de seguimiento n.º 21154](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**Comportamiento anterior**

Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.

**Comportamiento nuevo**

En el caso anterior, se producirá una excepción.

**Por qué**

EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.

**Mitigaciones**

Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
