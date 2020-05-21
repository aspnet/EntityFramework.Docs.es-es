---
title: Proveedor de base de datos de SQLite-limitaciones-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 17e97da9dfffefeb507fde744b710e6936bff69b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672781"
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

El `Decimal` tipo proporciona un alto nivel de precisión. Sin embargo, si no necesita ese nivel de precisión, se recomienda usar Double en su lugar. Puede usar un [convertidor de valores](../../modeling/value-conversions.md) para seguir usando decimal en las clases.

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Limitaciones de las migraciones

El motor de base de datos de SQLite no es compatible con una serie de operaciones de esquema que son compatibles con la mayoría de las demás bases de datos relacionales. Si intenta aplicar una de las operaciones no admitidas a una base de datos de SQLite, se `NotSupportedException` iniciará una.

| Operación            | Se admite? | Requiere versión |
|:---------------------|:-----------|:-----------------|
| AddColumn            | ✔          | 1.0              |
| AddForeignKey        | ✗          |                  |
| AddPrimaryKey        | ✗          |                  |
| AddUniqueConstraint  | ✗          |                  |
| AlterColumn          | ✗          |                  |
| CreateIndex          | ✔          | 1.0              |
| CreateTable          | ✔          | 1.0              |
| DropColumn           | ✗          |                  |
| DropForeignKey       | ✗          |                  |
| DropIndex            | ✔          | 1.0              |
| DropPrimaryKey       | ✗          |                  |
| DropTable            | ✔          | 1.0              |
| DropUniqueConstraint | ✗          |                  |
| RenameColumn         | ✔          | 2.2.2            |
| RenameIndex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1.0              |
| EnsureSchema         | ✔ (no OP)  | 2.0              |
| DropSchema           | ✔ (no OP)  | 2.0              |
| Insertar               | ✔          | 2.0              |
| Actualizar               | ✔          | 2.0              |
| Eliminar               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>Solución de limitaciones de migraciones

Puede solucionar algunas de estas limitaciones escribiendo manualmente el código en las migraciones para realizar una recompilación de la tabla. Una recompilación de la tabla implica cambiar el nombre de la tabla existente, crear otra tabla, copiar los datos en la tabla nueva y eliminar la anterior. Tendrá que usar el `Sql(string)` método para realizar algunos de estos pasos.

Vea [realizar otros tipos de cambios de esquema de tabla](https://sqlite.org/lang_altertable.html#otheralter) en la documentación de SQLite para obtener más detalles.

En el futuro, EF puede admitir algunas de estas operaciones mediante el enfoque de regeneración de tablas en segundo plano. Puede [realizar un seguimiento de esta característica en nuestro proyecto de github](https://github.com/aspnet/EntityFrameworkCore/issues/329).
