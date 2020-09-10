---
title: 'Consultas sin seguimiento: EF6'
description: Consultas sin seguimiento en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
uid: ef6/querying/no-tracking
ms.openlocfilehash: eb9e29c219e0cdf1e379cf8bb925f4226b1434a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620288"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="10f2f-103">consultas de no seguimiento</span><span class="sxs-lookup"><span data-stu-id="10f2f-103">No-Tracking Queries</span></span>
<span data-ttu-id="10f2f-104">En ocasiones, es posible que desee obtener las entidades de una consulta, pero el contexto no puede realizar el seguimiento de esas entidades.</span><span class="sxs-lookup"><span data-stu-id="10f2f-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="10f2f-105">Esto puede dar lugar a un mejor rendimiento cuando se consulta un gran número de entidades en escenarios de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="10f2f-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="10f2f-106">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="10f2f-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="10f2f-107">Un nuevo método de extensión AsNoTracking permite ejecutar cualquier consulta de esta manera.</span><span class="sxs-lookup"><span data-stu-id="10f2f-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="10f2f-108">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="10f2f-108">For example:</span></span>  

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
