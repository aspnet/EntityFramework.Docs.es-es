---
title: 'Carga explícita de datos relacionados: EF Core'
description: Carga explícita de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: def1c8c2736c64bc6f39af0d344fd1330f8a06f5
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210459"
---
# <a name="explicit-loading-of-related-data"></a>Carga explícita de datos relacionados

## <a name="explicit-loading"></a>Carga explícita

Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas. Si está habilitado el seguimiento de cambios, cuando la consulta materializa una entidad, EF Core establece automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada. También establece las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recientemente cargada.

## <a name="querying-related-entities"></a>Consulta de las entidades relacionadas

También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.

Esto permite aplicar operadores adicionales en la consulta. Por ejemplo, aplicar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

También puede filtrar las entidades relacionadas que se cargan en la memoria.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
