---
title: Consulta de datos - EF Core
description: Información general sobre la realización de consultas en Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: da5177dda4f2df6537ee9133edf4f1240a4b5e94
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430123"
---
# <a name="querying-data"></a><span data-ttu-id="0a28f-103">Consulta de datos</span><span class="sxs-lookup"><span data-stu-id="0a28f-103">Querying Data</span></span>

<span data-ttu-id="0a28f-104">Entity Framework Core usa Language Integrated Query (LINQ) para consultar datos de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a28f-104">Entity Framework Core uses Language-Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="0a28f-105">LINQ permite usar C# (o el lenguaje .NET que prefiera) para escribir consultas fuertemente tipadas.</span><span class="sxs-lookup"><span data-stu-id="0a28f-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="0a28f-106">Usa el contexto derivado y las clases de entidad para hacer referencia a los objetos de base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a28f-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="0a28f-107">EF Core pasa una representación de la consulta LINQ al proveedor de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a28f-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="0a28f-108">A su vez, los proveedores de la base de datos la traducen al lenguaje de la consulta específico para la base de datos (por ejemplo, SQL para una base de datos relacional).</span><span class="sxs-lookup"><span data-stu-id="0a28f-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="0a28f-109">Las consultas siempre se ejecutan en la base de datos incluso si las entidades devueltas en el resultado ya existen en el contexto.</span><span class="sxs-lookup"><span data-stu-id="0a28f-109">Queries are always executed against the database even if the entities returned in the result already exist in the context.</span></span>

> [!TIP]
> <span data-ttu-id="0a28f-110">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a28f-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="0a28f-111">Los fragmentos de código siguientes muestran algunos ejemplos de cómo realizar tareas comunes con Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0a28f-111">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="0a28f-112">Carga de todos los datos</span><span class="sxs-lookup"><span data-stu-id="0a28f-112">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="0a28f-113">Carga de una sola entidad</span><span class="sxs-lookup"><span data-stu-id="0a28f-113">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="0a28f-114">Filtrado</span><span class="sxs-lookup"><span data-stu-id="0a28f-114">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="0a28f-115">Lecturas adicionales</span><span class="sxs-lookup"><span data-stu-id="0a28f-115">Further readings</span></span>

- <span data-ttu-id="0a28f-116">Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).</span><span class="sxs-lookup"><span data-stu-id="0a28f-116">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="0a28f-117">Para más información sobre cómo se procesa una consulta en EF Core, consulte [Cómo funcionan las consultas](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="0a28f-117">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
