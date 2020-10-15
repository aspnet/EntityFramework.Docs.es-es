---
title: 'Tabla de historial de migraciones personalizadas: EF Core'
description: Personalización de una tabla de historial que se usará para las migraciones con Entity Framework Core
author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 1f6dcb2fbb20e7dafed17160832ccec453839aa3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062313"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="8a93a-103">Tabla de historial de migraciones personalizadas</span><span class="sxs-lookup"><span data-stu-id="8a93a-103">Custom Migrations History Table</span></span>

<span data-ttu-id="8a93a-104">De forma predeterminada, EF Core realiza un seguimiento de las migraciones que se han aplicado a la base de datos mediante su grabación en una tabla denominada `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="8a93a-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="8a93a-105">Por varias razones, puede que desee personalizar esta tabla para satisfacer mejor sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="8a93a-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a93a-106">Si personaliza la tabla de historial de migraciones *después* de aplicar las migraciones, es responsable de actualizar la tabla existente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a93a-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="8a93a-107">Esquema y nombre de tabla</span><span class="sxs-lookup"><span data-stu-id="8a93a-107">Schema and table name</span></span>

<span data-ttu-id="8a93a-108">Puede cambiar el nombre de esquema y de tabla mediante el `MigrationsHistoryTable()` método de `OnConfiguring()` (o `ConfigureServices()` en ASP.net Core).</span><span class="sxs-lookup"><span data-stu-id="8a93a-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="8a93a-109">Este es un ejemplo del uso del proveedor de EF Core de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8a93a-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="8a93a-110">Otros cambios</span><span class="sxs-lookup"><span data-stu-id="8a93a-110">Other changes</span></span>

<span data-ttu-id="8a93a-111">Para configurar aspectos adicionales de la tabla, invalide y reemplace el servicio específico del proveedor `IHistoryRepository` .</span><span class="sxs-lookup"><span data-stu-id="8a93a-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="8a93a-112">Este es un ejemplo de cómo cambiar el nombre de la columna MigrationId a *ID* en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8a93a-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="8a93a-113">`SqlServerHistoryRepository` está dentro de un espacio de nombres interno y puede cambiar en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="8a93a-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
