---
title: 'Carga explícita de datos relacionados: EF Core'
description: Carga explícita de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 0cfc4b5304d81d5a58497857474804946d3f85d7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065724"
---
# <a name="explicit-loading-of-related-data"></a>Carga explícita de datos relacionados

## <a name="explicit-loading"></a>Carga explícita

Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#Eager)]

También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas. Si está habilitado el seguimiento de cambios, cuando la consulta materializa una entidad, EF Core establece automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada. También establece las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recientemente cargada.

## <a name="querying-related-entities"></a>Consulta de las entidades relacionadas

También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.

Esto permite aplicar operadores adicionales en la consulta. Por ejemplo, aplicar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryAggregate)]

También puede filtrar las entidades relacionadas que se cargan en la memoria.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryFiltered)]
