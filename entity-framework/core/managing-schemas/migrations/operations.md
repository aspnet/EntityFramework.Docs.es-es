---
title: 'Operaciones de migración personalizadas: EF Core'
description: Administrar migraciones de SQL personalizadas y sin formato para la administración de esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429837"
---
# <a name="custom-migrations-operations"></a>Operaciones de migración personalizadas

La API de MigrationBuilder permite realizar muchos tipos diferentes de operaciones durante una migración, pero está lejos de ser exhaustiva. Sin embargo, la API también es extensible, lo que le permite definir sus propias operaciones. Hay dos maneras de extender la API: mediante el `Sql()` método o mediante la definición de `MigrationOperation` objetos personalizados.

Para ilustrar, echemos un vistazo a la implementación de una operación que crea un usuario de base de datos mediante cada enfoque. En nuestras migraciones, queremos habilitar la escritura del código siguiente:

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Usar MigrationBuilder. SQL ()

La forma más fácil de implementar una operación personalizada es definir un método de extensión que llame a `MigrationBuilder.Sql()` . Este es un ejemplo que genera el correspondiente Transact-SQL.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> Utilice la `EXEC` función cuando una instrucción debe ser la primera o solo una en un lote de SQL. También podría ser necesario para solucionar los errores del analizador en scripts de migración idempotente que se pueden producir cuando las columnas a las que se hace referencia no existen actualmente en una tabla.

Si las migraciones necesitan admitir varios proveedores de bases de datos, puede utilizar la `MigrationBuilder.ActiveProvider` propiedad. Este es un ejemplo que admite tanto Microsoft SQL Server como PostgreSQL.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

Este enfoque solo funciona si conoce todos los proveedores en los que se va a aplicar la operación personalizada.

## <a name="using-a-migrationoperation"></a>Uso de un MigrationOperation

Para desacoplar la operación personalizada de SQL, puede definir la suya propia `MigrationOperation` para representarla. A continuación, la operación se pasa al proveedor para que pueda determinar el SQL adecuado que se va a generar.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

Con este enfoque, el método de extensión solo tiene que agregar una de estas operaciones a `MigrationBuilder.Operations` .

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

Este enfoque requiere que cada proveedor sepa cómo generar SQL para esta operación en su `IMigrationsSqlGenerator` servicio. Este es un ejemplo invalidando el generador del SQL Server para administrar la nueva operación.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

Reemplace el servicio de generador de SQL de migraciones predeterminado por el actualizado.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
