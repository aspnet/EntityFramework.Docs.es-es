---
title: Consulta de datos - EF Core
description: Información general sobre la realización de consultas en Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 7cf53d59a577c5bc45f3ea4ea0cfbe257437b44e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062568"
---
# <a name="querying-data"></a>Consulta de datos

Entity Framework Core usa Language Integrated Query (LINQ) para consultar datos de la base de datos. LINQ permite usar C# (o el lenguaje .NET que prefiera) para escribir consultas fuertemente tipadas. Usa el contexto derivado y las clases de entidad para hacer referencia a los objetos de base de datos. EF Core pasa una representación de la consulta LINQ al proveedor de la base de datos. A su vez, los proveedores de la base de datos la traducen al lenguaje de la consulta específico para la base de datos (por ejemplo, SQL para una base de datos relacional).

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) de este artículo en GitHub.

Los fragmentos de código siguientes muestran algunos ejemplos de cómo realizar tareas comunes con Entity Framework Core.

## <a name="loading-all-data"></a>Carga de todos los datos

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>Carga de una sola entidad

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a>Filtrado

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a>Lecturas adicionales

- Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).
- Para más información sobre cómo se procesa una consulta en EF Core, consulte [Cómo funcionan las consultas](xref:core/querying/how-query-works).
