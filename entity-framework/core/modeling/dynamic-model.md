---
title: Alternar entre varios modelos con el mismo tipo de DbContext EF Core
description: Alternar entre varios modelos con el mismo tipo DbContext mediante Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/dynamic-model
ms.openlocfilehash: 0e0af67eab5262ab2b26edadea470c753b6349a0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071529"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>Alternar entre varios modelos con el mismo tipo DbContext

El modelo integrado `OnModelCreating` puede utilizar una propiedad en el contexto para cambiar el modo en que se compila el modelo. Por ejemplo, supongamos que desea configurar una entidad de forma diferente en función de alguna propiedad:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

Desafortunadamente, este código no funcionaría tal cual, ya que EF crea el modelo y se ejecuta `OnModelCreating` solo una vez, con lo que se almacena en caché el resultado por motivos de rendimiento. Sin embargo, puede enlazar con el mecanismo de almacenamiento en caché del modelo para que EF tenga en cuenta la propiedad que genera distintos modelos.

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF utiliza `IModelCacheKeyFactory` para generar claves de caché para los modelos; de forma predeterminada, EF supone que para un tipo de contexto determinado el modelo será el mismo, por lo que la implementación predeterminada de este servicio devuelve una clave que solo contiene el tipo de contexto. Para generar modelos diferentes a partir del mismo tipo de contexto, debe reemplazar el `IModelCacheKeyFactory` servicio con la implementación correcta; la clave generada se comparará con otras claves del modelo mediante el `Equals` método, teniendo en cuenta todas las variables que afectan al modelo.

La siguiente implementación tiene `UseIntProperty` en cuenta al generar una clave de caché del modelo:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

Por último, registre el nuevo `IModelCacheKeyFactory` en su contexto `OnConfiguring` :

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

Vea el [proyecto de ejemplo completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) para obtener más contexto.
