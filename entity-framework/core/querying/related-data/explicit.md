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
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="e5a61-103">Carga explícita de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="e5a61-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="e5a61-104">Carga explícita</span><span class="sxs-lookup"><span data-stu-id="e5a61-104">Explicit loading</span></span>

<span data-ttu-id="e5a61-105">Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="e5a61-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="e5a61-106">También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e5a61-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="e5a61-107">Si está habilitado el seguimiento de cambios, cuando la consulta materializa una entidad, EF Core establece automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada. También establece las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recientemente cargada.</span><span class="sxs-lookup"><span data-stu-id="e5a61-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="e5a61-108">Consulta de las entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="e5a61-108">Querying related entities</span></span>

<span data-ttu-id="e5a61-109">También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="e5a61-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="e5a61-110">Esto permite aplicar operadores adicionales en la consulta.</span><span class="sxs-lookup"><span data-stu-id="e5a61-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="e5a61-111">Por ejemplo, aplicar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.</span><span class="sxs-lookup"><span data-stu-id="e5a61-111">For example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="e5a61-112">También puede filtrar las entidades relacionadas que se cargan en la memoria.</span><span class="sxs-lookup"><span data-stu-id="e5a61-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
