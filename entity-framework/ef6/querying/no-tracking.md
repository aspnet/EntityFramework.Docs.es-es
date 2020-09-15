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
# <a name="no-tracking-queries"></a>consultas de no seguimiento
En ocasiones, es posible que desee obtener las entidades de una consulta, pero el contexto no puede realizar el seguimiento de esas entidades. Esto puede dar lugar a un mejor rendimiento cuando se consulta un gran número de entidades en escenarios de solo lectura. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

Un nuevo método de extensión AsNoTracking permite ejecutar cualquier consulta de esta manera. Por ejemplo:  

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
