---
title: Agrupación de DbContext
description: Agrupación de DbContext en Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: fd5f53ff97a73895f0c4239439730dd8cb3ecc29
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2020
ms.locfileid: "91215589"
---
# <a name="dbcontext-pooling"></a>Agrupación de DbContext

`AddDbContextPool` habilita la agrupación de `DbContext` instancias de. La agrupación de contexto puede aumentar el rendimiento en escenarios de gran escala, como servidores Web, mediante la reutilización de instancias de contexto, en lugar de crear nuevas instancias para cada solicitud.

El patrón típico en una aplicación ASP.NET Core con EF Core implica registrar un tipo personalizado en <xref:Microsoft.EntityFrameworkCore.DbContext> el contenedor de [inserción de dependencias](/aspnet/core/fundamentals/dependency-injection) y obtener instancias de ese tipo a través de parámetros de constructor en controladores o Razor pages. Mediante la inserción de constructores, se crea una nueva instancia de contexto para cada solicitud.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> habilita un grupo de instancias de contexto reutilizables. Para usar la agrupación de contexto, use el `AddDbContextPool` método en lugar de `AddDbContext` durante el registro del servicio:

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Cuando `AddDbContextPool` se usa, en el momento en que se solicita una instancia de contexto, EF comprueba primero si hay una instancia disponible en el grupo. Una vez que termina el procesamiento de la solicitud, se restablece cualquier estado en la instancia y la propia instancia se devuelve al grupo.

Esto es conceptualmente similar a la forma en que funciona la agrupación de conexiones en los proveedores de ADO.NET y tiene la ventaja de ahorrar parte del costo de inicialización de la instancia de contexto.

El `poolSize` parámetro de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> establece el número máximo de instancias retenidas por el grupo. Una vez que `poolSize` se supera, las nuevas instancias de contexto no se almacenan en caché y EF recurre al comportamiento de no agrupación de la creación de instancias a petición.

## <a name="limitations"></a>Limitaciones

Se debe crear un perfiles de las aplicaciones y probarlas para mostrar que la inicialización del contexto supone un costo significativo.

`AddDbContextPool` tiene algunas limitaciones en lo que se puede hacer en el `OnConfiguring` método del contexto.

> [!WARNING]  
> Evite el uso de la agrupación de contexto en aplicaciones que mantienen el estado. Por ejemplo, los campos privados en el contexto que no se deberían compartir entre las solicitudes. EF Core solo restablece el estado que se conoce antes de agregar una instancia de contexto al grupo.

La agrupación de contexto funciona mediante la reutilización de la misma instancia de contexto en todas las solicitudes. Esto significa que se registra de forma eficaz como [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) en lo que se refiere a la propia instancia para que pueda persistir.

La agrupación de contexto está pensada para escenarios en los que la configuración de contexto, que incluye los servicios resueltos, se fija entre solicitudes. En los casos en los que se requieren los servicios de [ámbito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) o es necesario cambiar la configuración, no utilice la agrupación. La ganancia de rendimiento de la agrupación suele ser despreciable, excepto en escenarios muy optimizados.
