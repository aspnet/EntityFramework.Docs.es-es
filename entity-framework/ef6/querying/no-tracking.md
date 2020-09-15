---
title: 'Consultas sin seguimiento: EF6'
description: Consultas sin seguimiento en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: ea4f05eb7a9b95fba55f70f249876bc9c5630f18
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073904"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="f97ec-103">consultas de no seguimiento</span><span class="sxs-lookup"><span data-stu-id="f97ec-103">No-Tracking Queries</span></span>
<span data-ttu-id="f97ec-104">En ocasiones, es posible que desee obtener las entidades de una consulta, pero el contexto no puede realizar el seguimiento de esas entidades.</span><span class="sxs-lookup"><span data-stu-id="f97ec-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="f97ec-105">Esto puede dar lugar a un mejor rendimiento cuando se consulta un gran número de entidades en escenarios de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="f97ec-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="f97ec-106">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="f97ec-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="f97ec-107">Un nuevo método de extensión AsNoTracking permite ejecutar cualquier consulta de esta manera.</span><span class="sxs-lookup"><span data-stu-id="f97ec-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="f97ec-108">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f97ec-108">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs without tracking them
    var blogs1 = context.Blogs.AsNoTracking();

    // Query for some blogs without tracking them
    var blogs2 = context.Blogs
                        .Where(b => b.Name.Contains(".NET"))
                        .AsNoTracking()
                        .ToList();
}
```  
