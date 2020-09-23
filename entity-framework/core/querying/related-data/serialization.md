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
# <a name="related-data-and-serialization"></a><span data-ttu-id="ad996-103">Datos relacionados y serialización</span><span class="sxs-lookup"><span data-stu-id="ad996-103">Related data and serialization</span></span>

<span data-ttu-id="ad996-104">Puesto que EF Core corregirá automáticamente las propiedades de navegación, puede terminar con ciclos en el gráfico de objetos.</span><span class="sxs-lookup"><span data-stu-id="ad996-104">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="ad996-105">Por ejemplo, cargar un blog y sus entradas relacionadas dará lugar a un objeto de blog que hará referencia a una colección de entradas.</span><span class="sxs-lookup"><span data-stu-id="ad996-105">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="ad996-106">Cada una de esas entradas tendrá una referencia de vuelta al blog.</span><span class="sxs-lookup"><span data-stu-id="ad996-106">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="ad996-107">Algunos marcos de serialización no permiten estos ciclos.</span><span class="sxs-lookup"><span data-stu-id="ad996-107">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="ad996-108">Por ejemplo, Json.NET generará la excepción siguiente si se encuentra un ciclo.</span><span class="sxs-lookup"><span data-stu-id="ad996-108">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="ad996-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: se detectó un bucle con autorreferencia para la propiedad "Blog" con el tipo "MyApplication.Models.Blog").</span><span class="sxs-lookup"><span data-stu-id="ad996-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="ad996-110">Si usa ASP.NET Core, puede configurar Json.NET para que omita los ciclos que encuentre en el gráfico de objetos.</span><span class="sxs-lookup"><span data-stu-id="ad996-110">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="ad996-111">Esta configuración se lleva a cabo en el método `ConfigureServices(...)` en `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="ad996-111">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

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

<span data-ttu-id="ad996-112">Otra alternativa consiste en decorar una de las propiedades de navegación con el atributo `[JsonIgnore]`, que indica a JSON.NET que no recorra esa propiedad de navegación mientras se serializa.</span><span class="sxs-lookup"><span data-stu-id="ad996-112">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
