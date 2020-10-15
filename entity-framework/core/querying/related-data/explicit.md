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
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="a18b7-103">Carga explícita de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="a18b7-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="a18b7-104">Carga explícita</span><span class="sxs-lookup"><span data-stu-id="a18b7-104">Explicit loading</span></span>

<span data-ttu-id="a18b7-105">Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="a18b7-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#Eager)]

<span data-ttu-id="a18b7-106">También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="a18b7-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="a18b7-107">Si está habilitado el seguimiento de cambios, cuando la consulta materializa una entidad, EF Core establece automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada. También establece las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recientemente cargada.</span><span class="sxs-lookup"><span data-stu-id="a18b7-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="a18b7-108">Consulta de las entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="a18b7-108">Querying related entities</span></span>

<span data-ttu-id="a18b7-109">También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="a18b7-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="a18b7-110">Esto permite aplicar operadores adicionales en la consulta.</span><span class="sxs-lookup"><span data-stu-id="a18b7-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="a18b7-111">Por ejemplo, aplicar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.</span><span class="sxs-lookup"><span data-stu-id="a18b7-111">For example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryAggregate)]

<span data-ttu-id="a18b7-112">También puede filtrar las entidades relacionadas que se cargan en la memoria.</span><span class="sxs-lookup"><span data-stu-id="a18b7-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryFiltered)]
