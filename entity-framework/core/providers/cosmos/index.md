---
title: 'Proveedor de Azure Cosmos DB: EF Core'
description: Documentación del proveedor de bases de datos que permite usar Entity Framework Core con SQL API de Azure Cosmos DB
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/providers/cosmos/index
ms.openlocfilehash: 825517e79891378a61f9564c90dbf4522459e9d0
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430329"
---
# <a name="ef-core-azure-cosmos-db-provider"></a>Proveedor de Azure Cosmos DB para EF Core

> [!NOTE]
> Este proveedor es nuevo en EF Core 3.0.

Este proveedor de base de datos permite usar Entity Framework Core con Azure Cosmos DB. Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/dotnet/efcore).

Se recomienda encarecidamente que se familiarice con la [documentación sobre Azure Cosmos DB](/azure/cosmos-db/introduction) antes de leer esta sección.

> [!NOTE]
> Este proveedor solo funciona con SQL API de Azure Cosmos DB.

## <a name="install"></a>Instalar

Instale el [paquete NuGet Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a>Introducción

> [!TIP]
> Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) de este artículo.

Al igual que para otros proveedores, el primer paso es llamar a [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> El punto de conexión y la clave se codifican aquí de forma rígida por motivos de simplicidad pero, en una aplicación de producción, se deben [almacenar de manera segura](/aspnet/core/security/app-secrets#secret-manager).

En este ejemplo, `Order` es una entidad sencilla con una referencia al [tipo owned](xref:core/modeling/owned-entities) `StreetAddress`.

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

La acción de guardar y consultar datos sigue el patrón de EF normal:

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> Llamar a [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) es necesario para crear los contenedores necesarios e insertar los [datos de inicialización](xref:core/modeling/data-seeding) si están presentes en el modelo. Sin embargo, se debe llamar a `EnsureCreatedAsync` solo durante la implementación y no durante la operación normal, porque podría provocar problemas de rendimiento.

## <a name="cosmos-options"></a>Opciones de Cosmos

El proveedor de Cosmos DB también se puede configurar con una sola cadena de conexión y especificar otras opciones para personalizar la conexión:

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OptionsContext.cs?name=Configuration)]

> [!NOTE]
> La mayoría de estas opciones son nuevas en EF Core Cosmos 5.0.

> [!TIP]
> Consulte la [documentación de las opciones de Azure Cosmos DB](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) para ver una descripción detallada del efecto que tiene cada opción arriba mencionada.

## <a name="cosmos-specific-model-customization"></a>Personalización del modelo específico de Cosmos

De manera predeterminada, todos los tipos de entidad están asignados al mismo contenedor, con un nombre que depende del contexto derivado (`"OrderContext"` en este caso). Para cambiar el nombre de contenedor predeterminado, use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

Para asignar un tipo de entidad a un contenedor distinto, use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

Para identificar el tipo de entidad que un elemento determinado representa, EF Core agrega un valor de discriminador incluso si no hay tipos de entidad derivados. El nombre y el valor del discriminador [se pueden modificar](xref:core/modeling/inheritance).

Si ningún otro tipo de entidad se va a almacenar en el mismo contenedor, se puede quitar el discriminador mediante la llamada a [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a>Claves de partición

De forma predeterminada, EF Core creará contenedores con la clave de partición establecida en `"__partitionKey"` sin proporcionarle ningún valor al insertar elementos. Sin embargo, para sacar el máximo partido a las funcionalidades de rendimiento de Azure Cosmos, se debe usar una [clave de partición seleccionada cuidadosamente](/azure/cosmos-db/partition-data). Se puede configurar mediante la llamada a [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
>La propiedad de clave de partición puede ser de cualquier tipo, siempre y cuando se [convierta en una cadena](xref:core/modeling/value-conversions).

Una vez configurada, la propiedad de clave de partición siempre debe tener un valor distinto de NULL. Una consulta se puede convertir en partición única agregando una llamada `WithPartitionKey`.

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey&highlight=15)]

> [!NOTE]
> `WithPartitionKey` se agregó en EF Core 5.0.

Con carácter general, se recomienda agregar la clave de partición a la clave principal, ya que es lo que mejor refleja la semántica de servidor y permite algunas optimizaciones, por ejemplo, en `FindAsync`.

## <a name="embedded-entities"></a>Entidades insertadas

En Cosmos, las entidades en propiedad se insertan en el mismo elemento que el propietario. Para cambiar el nombre de una propiedad, use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

Con esta configuración, el pedido del ejemplo anterior se almacena de esta manera:

```json
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

Las colecciones de entidades en propiedad también se insertan. En el ejemplo siguiente, usaremos la clase `Distributor` con una colección de `StreetAddress`:

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

No es necesario que las entidades en propiedad proporcionen valores de clave explícitos que se deban almacenar:

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

Se conservarán de esta manera:

```json
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

De manera interna, EF Core siempre debe tener valores de clave únicos para todas las entidades sometidas a seguimiento. La clave principal creada de manera predeterminada para las colecciones de tipos en propiedad consta de las propiedades de clave externa que apuntan al propietario y una propiedad `int` correspondiente al índice de la matriz JSON. Para recuperar estos valores, se podría usar la API de entrada:

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> Cuando sea necesario, se puede cambiar la clave principal predeterminada para los tipos de entidad en propiedad, pero los valores de clave se deben proporcionar de manera explícita.

## <a name="working-with-disconnected-entities"></a>Trabajo con entidades desconectadas

Cada elemento debe tener un valor `id` único para la clave de partición específica. De manera predeterminada, EF Core genera el valor mediante la concatenación de los valores de discriminador y de clave principal, con "|" como delimitador. Los valores de clave solo se generan cuando una entidad entra en el estado `Added`. Esto podría suponer un problema al [adjuntar entidades](xref:core/saving/disconnected-entities) si no tienen una propiedad `id` en el tipo .NET para almacenar el valor.

Para evitar esta limitación, se podría crear y establecer el valor `id` de manera manual o marcar la entidad como agregada y, luego, cambiarla al estado deseado:

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

Este es el JSON resultante:

```json
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

## <a name="optimistic-concurrency-with-etags"></a>Simultaneidad optimista con mecanismos ETag

> [!NOTE]
> Se ha agregado compatibilidad con la simultaneidad de ETag en EF Core 5.0.

Si necesita configurar un tipo de entidad para que use la [simultaneidad optimista](xref:core/modeling/concurrency), llame a `UseETagConcurrency`. Esta llamada creará una propiedad `_etag` en [estado de propiedad reemplazada](xref:core/modeling/shadow-properties) y la establecerá como el token de simultaneidad.

[!code-csharp[Main](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=ETag)]

Para facilitar la resolución de errores de simultaneidad, puede asignar el ETag a una propiedad de CLR mediante `IsETagConcurrency`.

[!code-csharp[Main](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=ETagProperty)]
