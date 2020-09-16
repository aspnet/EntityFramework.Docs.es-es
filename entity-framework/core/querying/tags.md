---
title: Etiquetas de consulta - EF Core
description: Uso de etiquetas de consulta para ayudar a identificar consultas específicas en los mensajes de registro que emite Entity Framework Core
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: bf80057d29160bd5ef6ae26ee6d447fc3c3f0fec
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617489"
---
# <a name="query-tags"></a><span data-ttu-id="28862-103">Etiquetas de consulta</span><span class="sxs-lookup"><span data-stu-id="28862-103">Query tags</span></span>

> [!NOTE]
> <span data-ttu-id="28862-104">Esta característica es nueva en EF Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="28862-104">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="28862-105">Esta característica ayuda a establecer la correlación de las consultas LINQ en el código con las consultas SQL generadas capturadas en los registros.</span><span class="sxs-lookup"><span data-stu-id="28862-105">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="28862-106">El usuario anota una consulta LINQ con el nuevo método `TagWith()`:</span><span class="sxs-lookup"><span data-stu-id="28862-106">You annotate a LINQ query using the new `TagWith()` method:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="28862-107">Esta consulta LINQ se traduce a la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="28862-107">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="28862-108">Es posible llamar a `TagWith()` muchas veces en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="28862-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="28862-109">Las etiquetas de consulta son acumulativas.</span><span class="sxs-lookup"><span data-stu-id="28862-109">Query tags are cumulative.</span></span>
<span data-ttu-id="28862-110">Por ejemplo, si tenemos los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="28862-110">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="28862-111">La siguiente consulta:</span><span class="sxs-lookup"><span data-stu-id="28862-111">The following query:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="28862-112">Se traduce en:</span><span class="sxs-lookup"><span data-stu-id="28862-112">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="28862-113">También es posible utilizar cadenas de varias líneas como etiquetas de consulta.</span><span class="sxs-lookup"><span data-stu-id="28862-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="28862-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="28862-114">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="28862-115">Produce el siguiente SQL:</span><span class="sxs-lookup"><span data-stu-id="28862-115">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="28862-116">Restricciones conocidas</span><span class="sxs-lookup"><span data-stu-id="28862-116">Known limitations</span></span>

<span data-ttu-id="28862-117">**Las etiquetas de consulta no se pueden parametrizar:** EF Core siempre trata las etiquetas de consulta de la consulta LINQ como literales de cadena que se incluyen en el código SQL generado.</span><span class="sxs-lookup"><span data-stu-id="28862-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="28862-118">Las consultas compiladas que toman las etiquetas de consulta como parámetros no están permitidas.</span><span class="sxs-lookup"><span data-stu-id="28862-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
