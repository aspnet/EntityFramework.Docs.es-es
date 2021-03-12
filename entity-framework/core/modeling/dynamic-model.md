---
title: Alternar entre varios modelos con el mismo tipo de DbContext EF Core
description: Alternar entre varios modelos con el mismo tipo DbContext mediante Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/dynamic-model
ms.openlocfilehash: 0c418000b85c508569b8146af63bff205ae6d222
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024035"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="d423a-103">Alternar entre varios modelos con el mismo tipo DbContext</span><span class="sxs-lookup"><span data-stu-id="d423a-103">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="d423a-104">El modelo integrado `OnModelCreating` puede utilizar una propiedad en el contexto para cambiar el modo en que se compila el modelo.</span><span class="sxs-lookup"><span data-stu-id="d423a-104">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="d423a-105">Por ejemplo, supongamos que desea configurar una entidad de forma diferente en función de alguna propiedad:</span><span class="sxs-lookup"><span data-stu-id="d423a-105">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="d423a-106">Desafortunadamente, este código no funcionaría tal cual, ya que EF crea el modelo y se ejecuta `OnModelCreating` solo una vez, con lo que se almacena en caché el resultado por motivos de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="d423a-106">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="d423a-107">Sin embargo, puede enlazar con el mecanismo de almacenamiento en caché del modelo para que EF tenga en cuenta la propiedad que genera distintos modelos.</span><span class="sxs-lookup"><span data-stu-id="d423a-107">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="d423a-108">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="d423a-108">IModelCacheKeyFactory</span></span>

<span data-ttu-id="d423a-109">EF utiliza `IModelCacheKeyFactory` para generar claves de caché para los modelos; de forma predeterminada, EF supone que para un tipo de contexto determinado el modelo será el mismo, por lo que la implementación predeterminada de este servicio devuelve una clave que solo contiene el tipo de contexto.</span><span class="sxs-lookup"><span data-stu-id="d423a-109">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="d423a-110">Para generar modelos diferentes a partir del mismo tipo de contexto, debe reemplazar el `IModelCacheKeyFactory` servicio con la implementación correcta; la clave generada se comparará con otras claves del modelo mediante el `Equals` método, teniendo en cuenta todas las variables que afectan al modelo.</span><span class="sxs-lookup"><span data-stu-id="d423a-110">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="d423a-111">La siguiente implementación tiene `UseIntProperty` en cuenta al generar una clave de caché del modelo:</span><span class="sxs-lookup"><span data-stu-id="d423a-111">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="d423a-112">Por último, registre el nuevo `IModelCacheKeyFactory` en su contexto `OnConfiguring` :</span><span class="sxs-lookup"><span data-stu-id="d423a-112">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="d423a-113">Vea el [proyecto de ejemplo completo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/DynamicModel) para obtener más contexto.</span><span class="sxs-lookup"><span data-stu-id="d423a-113">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/DynamicModel) for more context.</span></span>
