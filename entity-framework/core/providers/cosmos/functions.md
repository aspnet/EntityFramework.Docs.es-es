---
title: 'Asignaciones de función: proveedor de Azure Cosmos DB-EF Core'
description: Asignaciones de función del proveedor de EF Core de Azure Cosmos DB
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066537"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="05b75-103">Asignaciones de función del proveedor de EF Core de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="05b75-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="05b75-104">En esta página se muestran los miembros de .NET que se traducen en SQL Functions cuando se usa el proveedor de Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="05b75-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="05b75-105">.NET</span><span class="sxs-lookup"><span data-stu-id="05b75-105">.NET</span></span>                          | <span data-ttu-id="05b75-106">SQL</span><span class="sxs-lookup"><span data-stu-id="05b75-106">SQL</span></span>                              | <span data-ttu-id="05b75-107">Agregado en</span><span class="sxs-lookup"><span data-stu-id="05b75-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="05b75-108">Collection. Contains (Item)</span><span class="sxs-lookup"><span data-stu-id="05b75-108">collection.Contains(item)</span></span>     | <span data-ttu-id="05b75-109">@item DE @collection</span><span class="sxs-lookup"><span data-stu-id="05b75-109">@item IN @collection</span></span>
<span data-ttu-id="05b75-110">Valorstring. Contains (valor)</span><span class="sxs-lookup"><span data-stu-id="05b75-110">stringValue.Contains(value)</span></span>   | <span data-ttu-id="05b75-111">Contains ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="05b75-111">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="05b75-112">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05b75-112">EF Core 5.0</span></span>
<span data-ttu-id="05b75-113">Valorstring. EndsWith (valor)</span><span class="sxs-lookup"><span data-stu-id="05b75-113">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="05b75-114">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="05b75-114">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="05b75-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05b75-115">EF Core 5.0</span></span>
<span data-ttu-id="05b75-116">Valorstring. FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="05b75-116">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="05b75-117">IZQUIERDA ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="05b75-117">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="05b75-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05b75-118">EF Core 5.0</span></span>
<span data-ttu-id="05b75-119">Valorstring. LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="05b75-119">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="05b75-120">RIGHT ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="05b75-120">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="05b75-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05b75-121">EF Core 5.0</span></span>
<span data-ttu-id="05b75-122">Valorstring. StartsWith (valor)</span><span class="sxs-lookup"><span data-stu-id="05b75-122">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="05b75-123">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="05b75-123">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="05b75-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05b75-124">EF Core 5.0</span></span>
