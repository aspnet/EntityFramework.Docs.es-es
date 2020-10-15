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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="40c17-103">Compatibilidad de tablas Memory-Optimized en SQL Server proveedor de bases de datos EF Core</span><span class="sxs-lookup"><span data-stu-id="40c17-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="40c17-104">[Las tablas optimizadas para memoria](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) son una característica de SQL Server en la que toda la tabla reside en memoria.</span><span class="sxs-lookup"><span data-stu-id="40c17-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="40c17-105">Una segunda copia de los datos de la tabla se conserva en el disco pero solo por la durabilidad.</span><span class="sxs-lookup"><span data-stu-id="40c17-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="40c17-106">Los datos de las tablas optimizadas para memoria solo se leen del disco durante la recuperación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="40c17-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="40c17-107">Por ejemplo, después de reiniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="40c17-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="40c17-108">Configurar una tabla optimizada para memoria</span><span class="sxs-lookup"><span data-stu-id="40c17-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="40c17-109">Puede especificar que la tabla a la que está asignada una entidad está optimizada para memoria.</span><span class="sxs-lookup"><span data-stu-id="40c17-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="40c17-110">Al usar EF Core para crear y mantener una base de datos basada en el modelo (ya sea con [migraciones](xref:core/managing-schemas/migrations/index) o [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), se creará una tabla optimizada para memoria para estas entidades.</span><span class="sxs-lookup"><span data-stu-id="40c17-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
