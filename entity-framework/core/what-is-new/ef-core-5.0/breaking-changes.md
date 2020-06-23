---
title: 'Cambios importantes en EF Core 5.0: EF Core'
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666172"
---
# <a name="breaking-changes-in-ef-core-50"></a>Cambios importantes en EF Core 5.0

Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.

## <a name="summary"></a>Resumen

| **Cambio importante**                                                                                                               | **Impacto** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | Bajo        |

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
