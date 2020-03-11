---
title: 'Propiedades de la entidad: EF Core'
description: Cómo configurar y asignar propiedades de entidad mediante Entity Framework Core
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: b67603fbffd1f1c8506bc21f8972c851eb8eef29
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414570"
---
# <a name="entity-properties"></a>Propiedades de entidad

Cada tipo de entidad del modelo tiene un conjunto de propiedades, que EF Core leerán y escribirán en la base de datos. Si utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla.

## <a name="included-and-excluded-properties"></a>Propiedades incluidas y excluidas

Por Convención, todas las propiedades públicas con un captador y un establecedor se incluirán en el modelo.

Las propiedades específicas se pueden excluir de la manera siguiente:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>Nombres de columna

Por Convención, cuando se utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla que tienen el mismo nombre que la propiedad.

Si prefiere configurar las columnas con nombres diferentes, puede hacerlo de la siguiente manera:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>Tipos de datos de columna

Al utilizar una base de datos relacional, el proveedor de base de datos selecciona un tipo de datos basado en el tipo .NET de la propiedad. También tiene en cuenta otros metadatos, como la [longitud máxima](#maximum-length)configurada, si la propiedad forma parte de una clave principal, etc.

Por ejemplo, SQL Server asigna propiedades de `DateTime` a columnas de `datetime2(7)` y `string` propiedades a `nvarchar(max)` columnas (o a `nvarchar(450)` para propiedades que se utilizan como clave).

También puede configurar las columnas para especificar un tipo de datos exacto para una columna. Por ejemplo, el código siguiente configura `Url` como una cadena no Unicode con una longitud máxima de `200` y `Rating` como decimal con precisión de `5` y la escala de `2`:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>Longitud máxima

La configuración de una longitud máxima proporciona una sugerencia al proveedor de base de datos sobre el tipo de datos de columna adecuado que se debe elegir para una propiedad determinada. La longitud máxima solo se aplica a los tipos de datos de matriz, como `string` y `byte[]`.

> [!NOTE]
> Entity Framework no realiza ninguna validación de la longitud máxima antes de pasar datos al proveedor. Depende del proveedor o del almacén de datos que se valide si es necesario. Por ejemplo, cuando el destino es SQL Server, si se supera la longitud máxima, se producirá una excepción, ya que el tipo de datos de la columna subyacente no permitirá que se almacenen los datos sobrantes.

En el ejemplo siguiente, la configuración de una longitud máxima de 500 hará que se cree una columna de tipo `nvarchar(500)` en SQL Server:

#### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a>Propiedades obligatorias y opcionales

Una propiedad se considera opcional si es válida para que contenga `null`. Si `null` no es un valor válido que se va a asignar a una propiedad, se considera que es una propiedad obligatoria. Al asignar a un esquema de base de datos relacional, las propiedades requeridas se crean como columnas que no aceptan valores NULL y las propiedades opcionales se crean como columnas que aceptan valores NULL.

### <a name="conventions"></a>Convenciones

Por Convención, una propiedad cuyo tipo .NET pueda contener NULL se configurará como opcional, mientras que las propiedades cuyo tipo .NET no puede contener valores NULL se configurarán según sea necesario. Por ejemplo, todas las propiedades con tipos de valor .NET (`int`, `decimal`, `bool`, etc.) se configuran según sea necesario y todas las propiedades con tipos de valor .NET que aceptan valores NULL (`int?`, `decimal?`, `bool?`, etc.) se configuran como opcionales.

C#8 presentó una nueva característica denominada [tipos de referencia que aceptan valores NULL](/dotnet/csharp/tutorials/nullable-reference-types), que permite anotar tipos de referencia, lo que indica si es válido para que contengan null o not. Esta característica está deshabilitada de forma predeterminada y, si está habilitada, modifica el comportamiento de EF Core de la siguiente manera:

* Si los tipos de referencia que aceptan valores NULL están deshabilitados (el valor predeterminado), todas las propiedades con tipos de referencia de .NET se configuran como opcionales por Convención (por ejemplo, `string`).
* Si los tipos de referencia que aceptan valores NULL están habilitados, las propiedades C# se configurarán en función de la nulabilidad de su tipo .net: `string?` se configurarán como opcionales, mientras que `string` se configurarán según sea necesario.

En el ejemplo siguiente se muestra un tipo de entidad con propiedades obligatorias y opcionales, con la característica de referencia que acepta valores NULL deshabilitada (valor predeterminado) y habilitada:

#### <a name="without-nullable-reference-types-default"></a>[Sin tipos de referencia que aceptan valores NULL (valor predeterminado)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[Con tipos de referencia que aceptan valores NULL](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

Se recomienda el uso de tipos de referencia que aceptan valores NULL, ya que C# fluye la nulabilidad expresada en el código para EF Core modelo y en la base de datos, e obvia el uso de las anotaciones de datos o la API fluida para expresar el mismo concepto dos veces.

> [!NOTE]
> Tenga cuidado al habilitar los tipos de referencia que aceptan valores NULL en un proyecto existente: las propiedades de tipo de referencia que se configuraron anteriormente como opcional ahora se configurarán según sea necesario, a menos que se anoten explícitamente para que acepten valores NULL. Al administrar un esquema de base de datos relacional, esto puede provocar que se generen migraciones que modifiquen la nulabilidad de la columna de la base de datos.

Para obtener más información sobre los tipos de referencia que aceptan valores NULL y cómo usarlos con EF Core, [consulte la página de documentación dedicada para esta característica](xref:core/miscellaneous/nullable-reference-types).

### <a name="explicit-configuration"></a>Configuración explícita

Una propiedad que sería opcional por Convención se puede configurar para que sea necesaria de la siguiente manera:

#### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***
