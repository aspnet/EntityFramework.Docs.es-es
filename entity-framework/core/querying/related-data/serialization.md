---
title: 'Datos relacionados y serialización: EF Core'
description: Información sobre la forma en que los ciclos de los datos relacionados con Entity Framework Core pueden afectar a los marcos de serialización
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 6b28a1fc79a8c23e5249d36386c8ed06805dbe30
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078891"
---
# <a name="related-data-and-serialization"></a>Datos relacionados y serialización

Puesto que EF Core corregirá automáticamente las propiedades de navegación, puede terminar con ciclos en el gráfico de objetos. Por ejemplo, cargar un blog y sus entradas relacionadas dará lugar a un objeto de blog que hará referencia a una colección de entradas. Cada una de esas entradas tendrá una referencia de vuelta al blog.

Algunos marcos de serialización no permiten estos ciclos. Por ejemplo, Json.NET generará la excepción siguiente si se encuentra un ciclo.

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: se detectó un bucle con autorreferencia para la propiedad "Blog" con el tipo "MyApplication.Models.Blog").

Si usa ASP.NET Core, puede configurar Json.NET para que omita los ciclos que encuentre en el gráfico de objetos. Esta configuración se lleva a cabo en el método `ConfigureServices(...)` en `Startup.cs`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

Otra alternativa consiste en decorar una de las propiedades de navegación con el atributo `[JsonIgnore]`, que indica a JSON.NET que no recorra esa propiedad de navegación mientras se serializa.
