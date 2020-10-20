---
title: Etiquetas de consulta - EF Core
description: Uso de etiquetas de consulta para ayudar a identificar consultas específicas en los mensajes de registro que emite Entity Framework Core
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: f7cd3558682b1c19e03fc6d04957c7112e870734
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065737"
---
# <a name="query-tags"></a><span data-ttu-id="35e61-103">Etiquetas de consulta</span><span class="sxs-lookup"><span data-stu-id="35e61-103">Query tags</span></span>

<span data-ttu-id="35e61-104">Las etiquetas de consulta ayudan a establecer la correlación de las consultas LINQ en el código con las consultas SQL generadas capturadas en los registros.</span><span class="sxs-lookup"><span data-stu-id="35e61-104">Query tags help correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="35e61-105">El usuario anota una consulta LINQ con el nuevo método `TagWith()`:</span><span class="sxs-lookup"><span data-stu-id="35e61-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

> [!TIP]
> <span data-ttu-id="35e61-106">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="35e61-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) on GitHub.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

<span data-ttu-id="35e61-107">Esta consulta LINQ se traduce a la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="35e61-107">This LINQ query is translated to the following SQL statement:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="35e61-108">Es posible llamar a `TagWith()` muchas veces en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="35e61-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="35e61-109">Las etiquetas de consulta son acumulativas.</span><span class="sxs-lookup"><span data-stu-id="35e61-109">Query tags are cumulative.</span></span>
<span data-ttu-id="35e61-110">Por ejemplo, si tenemos los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="35e61-110">For example, given the following methods:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

<span data-ttu-id="35e61-111">La siguiente consulta:</span><span class="sxs-lookup"><span data-stu-id="35e61-111">The following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

<span data-ttu-id="35e61-112">Se traduce en:</span><span class="sxs-lookup"><span data-stu-id="35e61-112">Translates to:</span></span>

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="35e61-113">También es posible utilizar cadenas de varias líneas como etiquetas de consulta.</span><span class="sxs-lookup"><span data-stu-id="35e61-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="35e61-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="35e61-114">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

<span data-ttu-id="35e61-115">Produce el siguiente SQL:</span><span class="sxs-lookup"><span data-stu-id="35e61-115">Produces the following SQL:</span></span>

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="35e61-116">Restricciones conocidas</span><span class="sxs-lookup"><span data-stu-id="35e61-116">Known limitations</span></span>

<span data-ttu-id="35e61-117">**Las etiquetas de consulta no se pueden parametrizar:** EF Core siempre trata las etiquetas de consulta de la consulta LINQ como literales de cadena que se incluyen en el código SQL generado.</span><span class="sxs-lookup"><span data-stu-id="35e61-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="35e61-118">Las consultas compiladas que toman las etiquetas de consulta como parámetros no están permitidas.</span><span class="sxs-lookup"><span data-stu-id="35e61-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
