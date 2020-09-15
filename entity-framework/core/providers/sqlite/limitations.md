---
title: Proveedor de base de datos de SQLite-limitaciones-EF Core
description: Limitaciones del proveedor de base de datos de Entity Framework Core SQLite en comparación con otros proveedores
author: bricelam
ms.date: 07/16/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 546910afb9c97a93a7cc471bb813be0b9874a4bd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071230"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>Limitaciones del proveedor de base de datos SQLite para EF Core

El proveedor de SQLite tiene varias limitaciones de migración. La mayoría de estas limitaciones son consecuencia de las limitaciones del motor de base de datos SQLite subyacente y no son específicas de EF.

## <a name="modeling-limitations"></a>Limitaciones del modelado

La biblioteca relacional común (compartida por Entity Framework proveedores de bases de datos relacionales) define las API para los conceptos de modelado que son comunes a la mayoría de los motores de bases de datos relacionales. Un par de estos conceptos no es compatible con el proveedor de SQLite.

* Esquemas
* Secuencias

## <a name="query-limitations"></a>Limitaciones de las consultas

SQLite no admite de forma nativa los siguientes tipos de datos. EF Core puede leer y escribir valores de estos tipos, y también se admite la consulta de igualdad ( `where e.Property == value` ). Sin embargo, otras operaciones, como la comparación y la ordenación, requerirán la evaluación en el cliente.

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

En lugar de `DateTimeOffset` , se recomienda usar valores DateTime. Al controlar varias zonas horarias, recomendamos convertir los valores a UTC antes de guardar y, a continuación, volver a convertirlos a la zona horaria adecuada.

El `Decimal` tipo proporciona un alto nivel de precisión. Sin embargo, si no necesita ese nivel de precisión, se recomienda usar Double en su lugar. Puede usar un [convertidor de valores](xref:core/modeling/value-conversions) para seguir usando decimal en las clases.

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Limitaciones de las migraciones

El motor de base de datos de SQLite no es compatible con una serie de operaciones de esquema que son compatibles con la mayoría de las demás bases de datos relacionales. Si intenta aplicar una de las operaciones no admitidas a una base de datos de SQLite, se `NotSupportedException` iniciará una.

Se intentará volver a generar para realizar determinadas operaciones. Las recompilaciones solo son posibles para los artefactos de base de datos que forman parte del modelo de EF Core. Si un artefacto de base de datos no forma parte del modelo (por ejemplo, si se creó manualmente dentro de una migración), `NotSupportedException` se sigue produciendo una.

| Operación            | Se admite?  | Requiere versión |
|:---------------------|:------------|:-----------------|
| AddCheckConstraint   | ✔ (recompilar) | 5.0              |
| AddColumn            | ✔           | 1.0              |
| AddForeignKey        | ✔ (recompilar) | 5.0              |
| AddPrimaryKey        | ✔ (recompilar) | 5.0              |
| AddUniqueConstraint  | ✔ (recompilar) | 5.0              |
| AlterColumn          | ✔ (recompilar) | 5.0              |
| CreateIndex          | ✔           | 1.0              |
| CreateTable          | ✔           | 1.0              |
| DropCheckConstraint  | ✔ (recompilar) | 5.0              |
| DropColumn           | ✔ (recompilar) | 5.0              |
| DropForeignKey       | ✔ (recompilar) | 5.0              |
| DropIndex            | ✔           | 1.0              |
| DropPrimaryKey       | ✔ (recompilar) | 5.0              |
| DropTable            | ✔           | 1.0              |
| DropUniqueConstraint | ✔ (recompilar) | 5.0              |
| RenameColumn         | ✔           | 2.2.2            |
| RenameIndex          | ✔ (recompilar) | 2.1              |
| RenameTable          | ✔           | 1.0              |
| EnsureSchema         | ✔ (no OP)   | 2.0              |
| DropSchema           | ✔ (no OP)   | 2.0              |
| Insertar               | ✔           | 2.0              |
| Actualizar               | ✔           | 2.0              |
| Eliminar               | ✔           | 2.0              |

## <a name="migrations-limitations-workaround"></a>Solución de limitaciones de migraciones

Puede solucionar algunas de estas limitaciones escribiendo manualmente el código en las migraciones para realizar una recompilación. Las recompilaciones de tablas implican la creación de una nueva tabla, la copia de datos en la nueva tabla, la eliminación de la tabla anterior y el cambio de nombre de la nueva tabla. Tendrá que usar el `Sql(string)` método para realizar algunos de estos pasos.

Vea [realizar otros tipos de cambios de esquema de tabla](https://sqlite.org/lang_altertable.html#otheralter) en la documentación de SQLite para obtener más detalles.
