---
title: Proveedor de Azure Cosmos DB-limitaciones-EF Core
description: Limitaciones del proveedor de Azure Cosmos DB de Entity Framework Core en comparación con otros proveedores
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430201"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="65aa4-103">Limitaciones del proveedor de Azure Cosmos DB de EF Core</span><span class="sxs-lookup"><span data-stu-id="65aa4-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="65aa4-104">El proveedor de Cosmos tiene una serie de limitaciones.</span><span class="sxs-lookup"><span data-stu-id="65aa4-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="65aa4-105">Muchas de estas limitaciones son consecuencia de las limitaciones del motor de base de datos de Cosmos subyacente y no son específicas de EF.</span><span class="sxs-lookup"><span data-stu-id="65aa4-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="65aa4-106">Pero la mayoría [no se ha implementado todavía](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="65aa4-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="65aa4-107">Limitaciones temporales</span><span class="sxs-lookup"><span data-stu-id="65aa4-107">Temporary limitations</span></span>

- <span data-ttu-id="65aa4-108">`Include` no se admiten llamadas</span><span class="sxs-lookup"><span data-stu-id="65aa4-108">`Include` calls are not supported</span></span>
- <span data-ttu-id="65aa4-109">`Join` no se admiten llamadas</span><span class="sxs-lookup"><span data-stu-id="65aa4-109">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="65aa4-110">Limitaciones del SDK de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="65aa4-110">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="65aa4-111">Solo se proporcionan métodos asincrónicos</span><span class="sxs-lookup"><span data-stu-id="65aa4-111">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="65aa4-112">Dado que no hay ninguna versión de sincronización de los métodos de nivel bajo EF Core se basa en, la funcionalidad correspondiente se implementa actualmente mediante una llamada a `.Wait()` en el devuelto `Task` .</span><span class="sxs-lookup"><span data-stu-id="65aa4-112">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="65aa4-113">Esto significa que el uso de métodos como `SaveChanges` o `ToList` en lugar de sus homólogos asincrónicos podría provocar un interbloqueo en la aplicación</span><span class="sxs-lookup"><span data-stu-id="65aa4-113">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="65aa4-114">Limitaciones de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="65aa4-114">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="65aa4-115">Puede ver la información general completa de [Azure Cosmos dB características admitidas](/azure/cosmos-db/modeling-data), estas son las diferencias más importantes en comparación con una base de datos relacional:</span><span class="sxs-lookup"><span data-stu-id="65aa4-115">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="65aa4-116">No se admiten las transacciones iniciadas por el cliente</span><span class="sxs-lookup"><span data-stu-id="65aa4-116">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="65aa4-117">Algunas consultas entre particiones son más lentas en función de los operadores implicados (por ejemplo, `Skip/Take` o `OFFSET LIMIT` )</span><span class="sxs-lookup"><span data-stu-id="65aa4-117">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
