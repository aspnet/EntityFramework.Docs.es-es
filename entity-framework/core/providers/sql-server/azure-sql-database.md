---
title: Opciones de Azure SQL Database de proveedor de base de datos Microsoft SQL Server-EF Core
description: Cómo especificar el nivel de servicio y de rendimiento para Azure SQL Database con el proveedor de base de datos de SQL Server Entity Framework Core
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: 9b2cc5e0790beb41295dcea6ae30c4ad15b537fa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065373"
---
# <a name="specifying-azure-sql-database-options"></a>Especificación de opciones de Azure SQL Database

>[!NOTE]
> Esta API es nueva en EF Core 3,1.

Azure SQL Database proporciona [una variedad de opciones de precios](https://azure.microsoft.com/pricing/details/sql-database/single/) que normalmente se configuran a través de Azure portal. Sin embargo, si administra el esquema mediante [migraciones de EF Core](xref:core/managing-schemas/migrations/index) , puede especificar las opciones deseadas en el propio modelo.

Puede especificar el nivel de servicio de la base de datos (edición) mediante [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

Puede especificar el tamaño máximo de la base de datos mediante [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

Puede especificar el nivel de rendimiento de la base de datos (SERVICE_OBJECTIVE) mediante [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

Use [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) para configurar el grupo elástico, ya que el valor no es un literal de cadena:

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> Puede encontrar todos los valores admitidos en la [documentación de ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).
