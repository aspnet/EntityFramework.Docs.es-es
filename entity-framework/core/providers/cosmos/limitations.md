---
title: Proveedor de Azure Cosmos DB-limitaciones-EF Core
description: Limitaciones del proveedor de Azure Cosmos DB de Entity Framework Core en comparación con otros proveedores
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 088f593dddd9b5691d87566d7e31a699ba90d7c5
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635723"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="85f62-103">Limitaciones del proveedor de Azure Cosmos DB de EF Core</span><span class="sxs-lookup"><span data-stu-id="85f62-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="85f62-104">El proveedor de Cosmos tiene una serie de limitaciones.</span><span class="sxs-lookup"><span data-stu-id="85f62-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="85f62-105">Muchas de estas limitaciones son consecuencia de las limitaciones del motor de base de datos de Cosmos subyacente y no son específicas de EF.</span><span class="sxs-lookup"><span data-stu-id="85f62-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="85f62-106">Pero la mayoría [no se ha implementado todavía](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="85f62-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

<span data-ttu-id="85f62-107">Estas son algunas de las características solicitadas comúnmente:</span><span class="sxs-lookup"><span data-stu-id="85f62-107">These are some of the commonly requested features:</span></span>

- [<span data-ttu-id="85f62-108">`Include` ser</span><span class="sxs-lookup"><span data-stu-id="85f62-108">`Include` support</span></span>](https://github.com/dotnet/efcore/issues/16920)
- [<span data-ttu-id="85f62-109">`Join` ser</span><span class="sxs-lookup"><span data-stu-id="85f62-109">`Join` support</span></span>](https://github.com/dotnet/efcore/issues/17314)
- [<span data-ttu-id="85f62-110">Colecciones de tipos primitivos compatibles</span><span class="sxs-lookup"><span data-stu-id="85f62-110">Collections of primitive types support</span></span>](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="85f62-111">Limitaciones del SDK de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="85f62-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="85f62-112">Solo se proporcionan métodos asincrónicos</span><span class="sxs-lookup"><span data-stu-id="85f62-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="85f62-113">Dado que no hay ninguna versión de sincronización de los métodos de nivel bajo EF Core se basa en, la funcionalidad correspondiente se implementa actualmente mediante una llamada a `.Wait()` en el devuelto `Task` .</span><span class="sxs-lookup"><span data-stu-id="85f62-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="85f62-114">Esto significa que el uso de métodos como `SaveChanges` o `ToList` en lugar de sus homólogos asincrónicos podría provocar un interbloqueo en la aplicación</span><span class="sxs-lookup"><span data-stu-id="85f62-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="85f62-115">Limitaciones de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="85f62-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="85f62-116">Puede ver la información general completa de [Azure Cosmos dB características admitidas](/azure/cosmos-db/modeling-data), estas son las diferencias más importantes en comparación con una base de datos relacional:</span><span class="sxs-lookup"><span data-stu-id="85f62-116">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="85f62-117">No se admiten las transacciones iniciadas por el cliente</span><span class="sxs-lookup"><span data-stu-id="85f62-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="85f62-118">Algunas consultas entre particiones son más lentas en función de los operadores implicados (por ejemplo, `Skip/Take` o `OFFSET LIMIT` )</span><span class="sxs-lookup"><span data-stu-id="85f62-118">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
