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
# <a name="query-tags"></a>Etiquetas de consulta

Las etiquetas de consulta ayudan a establecer la correlación de las consultas LINQ en el código con las consultas SQL generadas capturadas en los registros.
El usuario anota una consulta LINQ con el nuevo método `TagWith()`:

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) de este artículo en GitHub.

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

Esta consulta LINQ se traduce a la siguiente instrucción SQL:

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

Es posible llamar a `TagWith()` muchas veces en la misma consulta.
Las etiquetas de consulta son acumulativas.
Por ejemplo, si tenemos los siguientes métodos:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

La siguiente consulta:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

Se traduce en:

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

También es posible utilizar cadenas de varias líneas como etiquetas de consulta.
Por ejemplo:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

Produce el siguiente SQL:

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Restricciones conocidas

**Las etiquetas de consulta no se pueden parametrizar:** EF Core siempre trata las etiquetas de consulta de la consulta LINQ como literales de cadena que se incluyen en el código SQL generado.
Las consultas compiladas que toman las etiquetas de consulta como parámetros no están permitidas.
