---
title: 'Asignaciones de función: proveedor de Azure Cosmos DB-EF Core'
description: Asignaciones de función del proveedor de EF Core de Azure Cosmos DB
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543593"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="252e0-103">Asignaciones de función del proveedor de EF Core de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="252e0-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="252e0-104">En esta página se muestran los miembros de .NET que se traducen en SQL Functions cuando se usa el proveedor de Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="252e0-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="252e0-105">.NET</span><span class="sxs-lookup"><span data-stu-id="252e0-105">.NET</span></span>                          | <span data-ttu-id="252e0-106">SQL</span><span class="sxs-lookup"><span data-stu-id="252e0-106">SQL</span></span>                              | <span data-ttu-id="252e0-107">Agregado en</span><span class="sxs-lookup"><span data-stu-id="252e0-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="252e0-108">Collection. Contains (Item)</span><span class="sxs-lookup"><span data-stu-id="252e0-108">collection.Contains(item)</span></span>     | <span data-ttu-id="252e0-109">@item DE @collection</span><span class="sxs-lookup"><span data-stu-id="252e0-109">@item IN @collection</span></span>
<span data-ttu-id="252e0-110">EF. Functions. RANDOM ()</span><span class="sxs-lookup"><span data-stu-id="252e0-110">EF.Functions.Random()</span></span>         | <span data-ttu-id="252e0-111">RAND ()</span><span class="sxs-lookup"><span data-stu-id="252e0-111">RAND()</span></span>                           | <span data-ttu-id="252e0-112">EF Core 6.0</span><span class="sxs-lookup"><span data-stu-id="252e0-112">EF Core 6.0</span></span>
<span data-ttu-id="252e0-113">Valorstring. Contains (valor)</span><span class="sxs-lookup"><span data-stu-id="252e0-113">stringValue.Contains(value)</span></span>   | <span data-ttu-id="252e0-114">Contains ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="252e0-114">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="252e0-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="252e0-115">EF Core 5.0</span></span>
<span data-ttu-id="252e0-116">Valorstring. EndsWith (valor)</span><span class="sxs-lookup"><span data-stu-id="252e0-116">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="252e0-117">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="252e0-117">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="252e0-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="252e0-118">EF Core 5.0</span></span>
<span data-ttu-id="252e0-119">Valorstring. FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="252e0-119">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="252e0-120">IZQUIERDA ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="252e0-120">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="252e0-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="252e0-121">EF Core 5.0</span></span>
<span data-ttu-id="252e0-122">Valorstring. LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="252e0-122">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="252e0-123">RIGHT ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="252e0-123">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="252e0-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="252e0-124">EF Core 5.0</span></span>
<span data-ttu-id="252e0-125">Valorstring. StartsWith (valor)</span><span class="sxs-lookup"><span data-stu-id="252e0-125">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="252e0-126">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="252e0-126">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="252e0-127">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="252e0-127">EF Core 5.0</span></span>
