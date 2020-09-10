---
title: 'Proveedor de Azure Cosmos DB: trabajar con datos no estructurados EF Core'
description: Cómo trabajar con Azure Cosmos DB datos no estructurados mediante Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 9f96af00e8fcb012c33fc7528787560ea3a5e481
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619045"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="e96a3-103">Trabajar con datos no estructurados en EF Core proveedor de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="e96a3-103">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="e96a3-104">EF Core se diseñó para facilitar el trabajo con datos que siguen un esquema definido en el modelo.</span><span class="sxs-lookup"><span data-stu-id="e96a3-104">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="e96a3-105">Sin embargo, uno de los puntos fuertes de Azure Cosmos DB es la flexibilidad en la forma de los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="e96a3-105">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="e96a3-106">Acceso a JSON sin formato</span><span class="sxs-lookup"><span data-stu-id="e96a3-106">Accessing the raw JSON</span></span>

<span data-ttu-id="e96a3-107">Es posible obtener acceso a las propiedades de las que no realiza un seguimiento EF Core a través de una propiedad especial en el [Estado de sombra](xref:core/modeling/shadow-properties) denominado `"__jObject"` que contiene un `JObject` que representa los datos recibidos del almacén y los datos que se van a almacenar:</span><span class="sxs-lookup"><span data-stu-id="e96a3-107">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](xref:core/modeling/shadow-properties) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

``` json
{
    "Id": 1,
    "PartitionKey": "1",
    "TrackingNumber": null,
    "id": "1",
    "Address": {
        "ShipsToCity": "London",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "eLMaAK8TzkIBAAAAAAAAAA==",
    "_self": "dbs/eLMaAA==/colls/eLMaAK8TzkI=/docs/eLMaAK8TzkIBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683e-0a12bf8d01d5\"",
    "_attachments": "attachments/",
    "BillingAddress": "Clarence House",
    "_ts": 1568164374
}
```

> [!WARNING]
> <span data-ttu-id="e96a3-108">La `"__jObject"` propiedad forma parte de la infraestructura de EF Core y solo se debe utilizar como último recurso, ya que es probable que tenga un comportamiento diferente en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="e96a3-108">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="e96a3-109">Los cambios en la entidad invalidarán los valores almacenados en `"__jObject"` durante `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="e96a3-109">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="e96a3-110">Usar CosmosClient</span><span class="sxs-lookup"><span data-stu-id="e96a3-110">Using CosmosClient</span></span>

<span data-ttu-id="e96a3-111">Para desacoplar completamente de EF Core obtenga el objeto [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) que forma [parte del SDK de Azure Cosmos dB](/azure/cosmos-db/sql-api-get-started) de `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="e96a3-111">To decouple completely from EF Core get the [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) object that is [part of the Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="e96a3-112">Faltan valores de propiedad</span><span class="sxs-lookup"><span data-stu-id="e96a3-112">Missing property values</span></span>

<span data-ttu-id="e96a3-113">En el ejemplo anterior, se ha quitado la `"TrackingNumber"` propiedad del pedido.</span><span class="sxs-lookup"><span data-stu-id="e96a3-113">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="e96a3-114">Debido a cómo funciona la indexación en Cosmos DB, las consultas que hacen referencia a la propiedad que falta en otro lugar y en la proyección podrían devolver resultados inesperados.</span><span class="sxs-lookup"><span data-stu-id="e96a3-114">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="e96a3-115">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e96a3-115">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="e96a3-116">La consulta ordenada realmente no devuelve ningún resultado.</span><span class="sxs-lookup"><span data-stu-id="e96a3-116">The sorted query actually returns no results.</span></span> <span data-ttu-id="e96a3-117">Esto significa que debe tener cuidado de rellenar siempre las propiedades asignadas por EF Core al trabajar directamente con el almacén.</span><span class="sxs-lookup"><span data-stu-id="e96a3-117">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="e96a3-118">Este comportamiento puede cambiar en versiones futuras de Cosmos.</span><span class="sxs-lookup"><span data-stu-id="e96a3-118">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="e96a3-119">Por ejemplo, actualmente, si la Directiva de indexación define el índice compuesto {ID/?</span><span class="sxs-lookup"><span data-stu-id="e96a3-119">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="e96a3-120">ASC, TrackingNumber/?</span><span class="sxs-lookup"><span data-stu-id="e96a3-120">ASC, TrackingNumber/?</span></span> <span data-ttu-id="e96a3-121">ASC)}, una consulta que tiene ' ORDER BY c.Id ASC, c. Discriminator ASC ' devolvería elementos a los __que les falta__ la `"TrackingNumber"` propiedad.</span><span class="sxs-lookup"><span data-stu-id="e96a3-121">ASC)}, then a query that has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>
