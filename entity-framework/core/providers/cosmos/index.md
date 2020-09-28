---
title: 'Proveedor de Azure Cosmos DB: EF Core'
description: Documentación del proveedor de bases de datos que permite usar Entity Framework Core con SQL API de Azure Cosmos DB
author: AndriySvyryd
ms.date: 09/14/2020
uid: core/providers/cosmos/index
ms.openlocfilehash: 94ba29f3f2643e8f563a460e17dce9d15cb7c2df
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210346"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="0fd5c-103">Proveedor de Azure Cosmos DB para EF Core</span><span class="sxs-lookup"><span data-stu-id="0fd5c-103">EF Core Azure Cosmos DB Provider</span></span>

> [!NOTE]
> <span data-ttu-id="0fd5c-104">Este proveedor es nuevo en EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-104">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="0fd5c-105">Este proveedor de base de datos permite usar Entity Framework Core con Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-105">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="0fd5c-106">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="0fd5c-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="0fd5c-107">Se recomienda encarecidamente que se familiarice con la [documentación sobre Azure Cosmos DB](/azure/cosmos-db/introduction) antes de leer esta sección.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-107">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](/azure/cosmos-db/introduction) before reading this section.</span></span>

> [!NOTE]
> <span data-ttu-id="0fd5c-108">Este proveedor solo funciona con SQL API de Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-108">This provider only works with the SQL API of Azure Cosmos DB.</span></span>

## <a name="install"></a><span data-ttu-id="0fd5c-109">Instalar</span><span class="sxs-lookup"><span data-stu-id="0fd5c-109">Install</span></span>

<span data-ttu-id="0fd5c-110">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span><span class="sxs-lookup"><span data-stu-id="0fd5c-110">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fd5c-111">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0fd5c-111">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studio"></a>[<span data-ttu-id="0fd5c-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fd5c-112">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a><span data-ttu-id="0fd5c-113">Introducción</span><span class="sxs-lookup"><span data-stu-id="0fd5c-113">Get started</span></span>

> [!TIP]  
> <span data-ttu-id="0fd5c-114">Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-114">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="0fd5c-115">Al igual que para otros proveedores, el primer paso es llamar a [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span><span class="sxs-lookup"><span data-stu-id="0fd5c-115">As for other providers the first step is to call [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="0fd5c-116">El punto de conexión y la clave se codifican aquí de forma rígida por motivos de simplicidad pero, en una aplicación de producción, se deben [almacenar de manera segura](/aspnet/core/security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="0fd5c-116">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securely](/aspnet/core/security/app-secrets#secret-manager).</span></span>

<span data-ttu-id="0fd5c-117">En este ejemplo, `Order` es una entidad sencilla con una referencia al [tipo owned](xref:core/modeling/owned-entities) `StreetAddress`.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-117">In this example `Order` is a simple entity with a reference to the [owned type](xref:core/modeling/owned-entities) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="0fd5c-118">La acción de guardar y consultar datos sigue el patrón de EF normal:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-118">Saving and querying data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="0fd5c-119">Llamar a [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) es necesario para crear los contenedores necesarios e insertar los [datos de inicialización](xref:core/modeling/data-seeding) si están presentes en el modelo.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-119">Calling [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) is necessary to create the required containers and insert the [seed data](xref:core/modeling/data-seeding) if present in the model.</span></span> <span data-ttu-id="0fd5c-120">Sin embargo, se debe llamar a `EnsureCreatedAsync` solo durante la implementación y no durante la operación normal, porque podría provocar problemas de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-120">However `EnsureCreatedAsync` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-options"></a><span data-ttu-id="0fd5c-121">Opciones de Cosmos</span><span class="sxs-lookup"><span data-stu-id="0fd5c-121">Cosmos options</span></span>

<span data-ttu-id="0fd5c-122">El proveedor de Cosmos DB también se puede configurar con una sola cadena de conexión y especificar otras opciones para personalizar la conexión:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-122">It is also possible to configure the Cosmos DB provider with a single connection string and to specify other options to customize the connection:</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OptionsContext.cs?name=Configuration)]

> [!NOTE]
> <span data-ttu-id="0fd5c-123">La mayoría de estas opciones son nuevas en EF Core Cosmos 5.0.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-123">Most of these options are new in EF Core Cosmos 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="0fd5c-124">Consulte la [documentación de las opciones de Azure Cosmos DB](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) para ver una descripción detallada del efecto que tiene cada opción arriba mencionada.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-124">See the [Azure Cosmos DB Options documentation](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) for a detailed description of the effect of each option mentioned above.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="0fd5c-125">Personalización del modelo específico de Cosmos</span><span class="sxs-lookup"><span data-stu-id="0fd5c-125">Cosmos-specific model customization</span></span>

<span data-ttu-id="0fd5c-126">De manera predeterminada, todos los tipos de entidad están asignados al mismo contenedor, con un nombre que depende del contexto derivado (`"OrderContext"` en este caso).</span><span class="sxs-lookup"><span data-stu-id="0fd5c-126">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="0fd5c-127">Para cambiar el nombre de contenedor predeterminado, use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span><span class="sxs-lookup"><span data-stu-id="0fd5c-127">To change the default container name use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="0fd5c-128">Para asignar un tipo de entidad a un contenedor distinto, use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span><span class="sxs-lookup"><span data-stu-id="0fd5c-128">To map an entity type to a different container use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="0fd5c-129">Para identificar el tipo de entidad que un elemento determinado representa, EF Core agrega un valor de discriminador incluso si no hay tipos de entidad derivados.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-129">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="0fd5c-130">El nombre y el valor del discriminador [se pueden modificar](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="0fd5c-130">The name and value of the discriminator [can be changed](xref:core/modeling/inheritance).</span></span>

<span data-ttu-id="0fd5c-131">Si ningún otro tipo de entidad se va a almacenar en el mismo contenedor, se puede quitar el discriminador mediante la llamada a [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span><span class="sxs-lookup"><span data-stu-id="0fd5c-131">If no other entity type will ever be stored in the same container the discriminator can be removed by calling [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span></span>

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a><span data-ttu-id="0fd5c-132">Claves de partición</span><span class="sxs-lookup"><span data-stu-id="0fd5c-132">Partition keys</span></span>

<span data-ttu-id="0fd5c-133">De forma predeterminada, EF Core creará contenedores con la clave de partición establecida en `"__partitionKey"` sin proporcionarle ningún valor al insertar elementos.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-133">By default EF Core will create containers with the partition key set to `"__partitionKey"` without supplying any value for it when inserting items.</span></span> <span data-ttu-id="0fd5c-134">Sin embargo, para sacar el máximo partido a las funcionalidades de rendimiento de Azure Cosmos, se debe usar una [clave de partición seleccionada cuidadosamente](/azure/cosmos-db/partition-data).</span><span class="sxs-lookup"><span data-stu-id="0fd5c-134">But to fully leverage the performance capabilities of Azure Cosmos a [carefully selected partition key](/azure/cosmos-db/partition-data) should be used.</span></span> <span data-ttu-id="0fd5c-135">Se puede configurar mediante la llamada a [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span><span class="sxs-lookup"><span data-stu-id="0fd5c-135">It can be configured by calling [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
><span data-ttu-id="0fd5c-136">La propiedad de clave de partición puede ser de cualquier tipo, siempre y cuando se [convierta en una cadena](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="0fd5c-136">The partition key property can be of any type as long as it is [converted to string](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="0fd5c-137">Una vez configurada, la propiedad de clave de partición siempre debe tener un valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-137">Once configured the partition key property should always have a non-null value.</span></span> <span data-ttu-id="0fd5c-138">Al emitir una consulta, se puede agregar una condición para que sea una partición única.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-138">When issuing a query a condition can be added to make it single-partition.</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a><span data-ttu-id="0fd5c-139">Entidades insertadas</span><span class="sxs-lookup"><span data-stu-id="0fd5c-139">Embedded entities</span></span>

<span data-ttu-id="0fd5c-140">En Cosmos, las entidades en propiedad se insertan en el mismo elemento que el propietario.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-140">For Cosmos, owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="0fd5c-141">Para cambiar el nombre de una propiedad, use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span><span class="sxs-lookup"><span data-stu-id="0fd5c-141">To change a property name use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="0fd5c-142">Con esta configuración, el pedido del ejemplo anterior se almacena de esta manera:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-142">With this configuration the order from the example above is stored like this:</span></span>

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
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="0fd5c-143">Las colecciones de entidades en propiedad también se insertan.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-143">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="0fd5c-144">En el ejemplo siguiente, usaremos la clase `Distributor` con una colección de `StreetAddress`:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-144">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="0fd5c-145">No es necesario que las entidades en propiedad proporcionen valores de clave explícitos que se deban almacenar:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-145">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="0fd5c-146">Se conservarán de esta manera:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-146">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Street": "5650 Dolly Ave"
        }
    ],
    "_rid": "6QEKANzISj0BAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKANzISj0=/docs/6QEKANzISj0BAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-7b2b439701d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163705
}
```

<span data-ttu-id="0fd5c-147">De manera interna, EF Core siempre debe tener valores de clave únicos para todas las entidades sometidas a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-147">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="0fd5c-148">La clave principal creada de manera predeterminada para las colecciones de tipos en propiedad consta de las propiedades de clave externa que apuntan al propietario y una propiedad `int` correspondiente al índice de la matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-148">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="0fd5c-149">Para recuperar estos valores, se podría usar la API de entrada:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-149">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="0fd5c-150">Cuando sea necesario, se puede cambiar la clave principal predeterminada para los tipos de entidad en propiedad, pero los valores de clave se deben proporcionar de manera explícita.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-150">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="0fd5c-151">Trabajo con entidades desconectadas</span><span class="sxs-lookup"><span data-stu-id="0fd5c-151">Working with disconnected entities</span></span>

<span data-ttu-id="0fd5c-152">Cada elemento debe tener un valor `id` único para la clave de partición específica.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-152">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="0fd5c-153">De manera predeterminada, EF Core genera el valor mediante la concatenación de los valores de discriminador y de clave principal, con "|" como delimitador.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-153">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="0fd5c-154">Los valores de clave solo se generan cuando una entidad entra en el estado `Added`.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-154">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="0fd5c-155">Esto podría suponer un problema al [adjuntar entidades](xref:core/saving/disconnected-entities) si no tienen una propiedad `id` en el tipo .NET para almacenar el valor.</span><span class="sxs-lookup"><span data-stu-id="0fd5c-155">This might pose a problem when [attaching entities](xref:core/saving/disconnected-entities) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="0fd5c-156">Para evitar esta limitación, se podría crear y establecer el valor `id` de manera manual o marcar la entidad como agregada y, luego, cambiarla al estado deseado:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-156">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="0fd5c-157">Este es el JSON resultante:</span><span class="sxs-lookup"><span data-stu-id="0fd5c-157">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        }
    ],
    "_rid": "JBwtAN8oNYEBAAAAAAAAAA==",
    "_self": "dbs/JBwtAA==/colls/JBwtAN8oNYE=/docs/JBwtAN8oNYEBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-9377-d7a1ae7c01d5\"",
    "_attachments": "attachments/",
    "_ts": 1572917100
}
```
