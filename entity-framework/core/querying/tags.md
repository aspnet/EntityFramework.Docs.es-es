---
title: Etiquetas de consulta - EF Core
description: Uso de etiquetas de consulta para ayudar a identificar consultas específicas en los mensajes de registro que emite Entity Framework Core
author: divega
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: 27f757f4159a36bec324cce56d74b7860e1c3741
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070996"
---
# <a name="query-tags"></a>Etiquetas de consulta

> [!NOTE]
> Esta característica es nueva en EF Core 2.2.

Esta característica ayuda a establecer la correlación de las consultas LINQ en el código con las consultas SQL generadas capturadas en los registros.
El usuario anota una consulta LINQ con el nuevo método `TagWith()`:

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Esta consulta LINQ se traduce a la siguiente instrucción SQL:

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Es posible llamar a `TagWith()` muchas veces en la misma consulta.
Las etiquetas de consulta son acumulativas.
Por ejemplo, si tenemos los siguientes métodos:

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

La siguiente consulta:

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

Se traduce en:

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

También es posible utilizar cadenas de varias líneas como etiquetas de consulta.
Por ejemplo:

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

Produce el siguiente SQL:

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Restricciones conocidas

**Las etiquetas de consulta no se pueden parametrizar:** EF Core siempre trata las etiquetas de consulta de la consulta LINQ como literales de cadena que se incluyen en el código SQL generado.
Las consultas compiladas que toman las etiquetas de consulta como parámetros no están permitidas.
