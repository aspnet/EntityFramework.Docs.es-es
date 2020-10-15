---
title: Tabla de Memory-Optimized de Microsoft SQL Server proveedor de base de datos-EF Core
description: Cómo usar Memory-Optimized tablas con el proveedor de base de datos de SQL Server Entity Framework Core
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 42b2a2ffafb2234e1ce7a6d0844234509a4b5b94
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063925"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>Compatibilidad de tablas Memory-Optimized en SQL Server proveedor de bases de datos EF Core

[Las tablas optimizadas para memoria](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) son una característica de SQL Server en la que toda la tabla reside en memoria. Una segunda copia de los datos de la tabla se conserva en el disco pero solo por la durabilidad. Los datos de las tablas optimizadas para memoria solo se leen del disco durante la recuperación de la base de datos. Por ejemplo, después de reiniciar el servidor.

## <a name="configuring-a-memory-optimized-table"></a>Configurar una tabla optimizada para memoria

Puede especificar que la tabla a la que está asignada una entidad está optimizada para memoria. Al usar EF Core para crear y mantener una base de datos basada en el modelo (ya sea con [migraciones](xref:core/managing-schemas/migrations/index) o [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), se creará una tabla optimizada para memoria para estas entidades.

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
