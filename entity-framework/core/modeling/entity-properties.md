---
title: 'Propiedades de la entidad: EF Core'
description: Cómo configurar y asignar propiedades de entidad mediante Entity Framework Core
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 3c64f5ac1c86a83b6456df9e29472dc0b22d8524
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543372"
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

Si prefiere configurar las columnas con nombres diferentes, puede hacerlo como fragmento de código siguiente:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>Tipos de datos de columna

Al utilizar una base de datos relacional, el proveedor de base de datos selecciona un tipo de datos basado en el tipo .NET de la propiedad. También tiene en cuenta otros metadatos, como la [longitud máxima](#maximum-length)configurada, si la propiedad forma parte de una clave principal, etc.

Por ejemplo, SQL Server asigna `DateTime` propiedades a `datetime2(7)` las columnas y `string` las propiedades a `nvarchar(max)` las columnas (o a `nvarchar(450)` para las propiedades que se usan como clave).

También puede configurar las columnas para especificar un tipo de datos exacto para una columna. Por ejemplo, el código siguiente configura `Url` como una cadena no Unicode con una longitud máxima de `200` y `Rating` como decimal con la precisión y la `5` escala de `2` :

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=5,8)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=6-7)]

***

### <a name="maximum-length"></a>Longitud máxima

La configuración de una longitud máxima proporciona una sugerencia al proveedor de base de datos sobre el tipo de datos de columna adecuado que se debe elegir para una propiedad determinada. La longitud máxima solo se aplica a los tipos de datos de matriz, como `string` y `byte[]` .

> [!NOTE]
> Entity Framework no realiza ninguna validación de la longitud máxima antes de pasar datos al proveedor. Depende del proveedor o del almacén de datos que se valide si es necesario. Por ejemplo, cuando el destino es SQL Server, si se supera la longitud máxima, se producirá una excepción, ya que el tipo de datos de la columna subyacente no permitirá que se almacenen los datos sobrantes.

En el ejemplo siguiente, la configuración de una longitud máxima de 500 hará que se cree una columna de tipo `nvarchar(500)` en SQL Server:

#### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=5)]

#### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a>Precisión y escala

A partir de EFCore 5,0, puede usar la API fluida para configurar la precisión y la escala. Indica al proveedor de base de datos cuánto espacio de almacenamiento se necesita para una columna determinada. Solo se aplica a los tipos de datos en los que el proveedor permite que la precisión y la escala varíen, normalmente `decimal` y `DateTime` .

En `decimal` el caso de las propiedades, precisión define el número máximo de dígitos necesarios para expresar cualquier valor que contenga la columna y escala define el número máximo de posiciones decimales necesarias. En `DateTime` el caso de las propiedades, precisión define el número máximo de dígitos necesarios para expresar fracciones de segundos y no se usa la escala.

> [!NOTE]
> Entity Framework no realiza ninguna validación de precisión o escala antes de pasar los datos al proveedor. Depende del proveedor o del almacén de datos que se validen según corresponda. Por ejemplo, cuando el destino es SQL Server, una columna de tipo de datos no `datetime` permite establecer la precisión, mientras que una `datetime2` puede tener una precisión de entre 0 y 7, ambos inclusive.

En el ejemplo siguiente, la configuración de la `Score` propiedad para que tenga la precisión 14 y la escala 2 hará que se cree una columna de tipo `decimal(14,2)` en SQL Server y la configuración de la `LastUpdated` propiedad para que tenga la precisión 3 producirá una columna de tipo `datetime2(3)` :

#### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

La precisión y la escala no se pueden configurar actualmente a través de anotaciones de datos.

#### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> La escala nunca se define sin necesidad de definir primero la precisión, por lo que la API fluida para definir la escala es `HasPrecision(precision, scale)` .

***

## <a name="required-and-optional-properties"></a>Propiedades obligatorias y opcionales

Una propiedad se considera opcional si es válida para que la contenga `null` . Si `null` no es un valor válido que se va a asignar a una propiedad, se considera que es una propiedad obligatoria. Al asignar a un esquema de base de datos relacional, las propiedades requeridas se crean como columnas que no aceptan valores NULL y las propiedades opcionales se crean como columnas que aceptan valores NULL.

### <a name="conventions"></a>Convenciones

Por Convención, una propiedad cuyo tipo .NET pueda contener NULL se configurará como opcional, mientras que las propiedades cuyo tipo .NET no puede contener valores NULL se configurarán según sea necesario. Por ejemplo, todas las propiedades con tipos de valor .net ( `int` , `decimal` , `bool` , etc.) se configuran como necesario y todas las propiedades con tipos de valor de .net que aceptan valores NULL ( `int?` , `decimal?` , `bool?` , etc.) se configuran como opcionales.

C# 8 presentó una nueva característica denominada [tipos de referencia que aceptan valores NULL (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), que permite anotar tipos de referencia, lo que indica si es válido para que contengan null o not. Esta característica está deshabilitada de forma predeterminada y afecta al comportamiento del EF Core de la siguiente manera:

* Si los tipos de referencia que aceptan valores NULL están deshabilitados (el valor predeterminado), todas las propiedades con tipos de referencia de .NET se configuran como opcionales por Convención (por ejemplo, `string` ).
* Si los tipos de referencia que aceptan valores NULL están habilitados, las propiedades se configurarán según la nulabilidad de C# de su tipo .NET: se `string?` configurarán como opcionales, pero se `string` configurarán según sea necesario.

En el ejemplo siguiente se muestra un tipo de entidad con propiedades obligatorias y opcionales, con la característica de referencia que acepta valores NULL deshabilitada (valor predeterminado) y habilitada:

#### <a name="without-nrt-default"></a>[Sin NRT (valor predeterminado)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=5,8)]

#### <a name="with-nrt"></a>[Con NRT](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

Se recomienda el uso de tipos de referencia que aceptan valores NULL, ya que fluye la nulabilidad expresada en el código de C# para EF Core modelo de y a la base de datos, e obvia el uso de las anotaciones de datos o la API fluida para expresar el mismo concepto dos veces.

> [!NOTE]
> Tenga cuidado al habilitar los tipos de referencia que aceptan valores NULL en un proyecto existente: las propiedades de tipo de referencia que se configuraron anteriormente como opcional ahora se configurarán según sea necesario, a menos que se anoten explícitamente para que acepten valores NULL. Al administrar un esquema de base de datos relacional, esto puede provocar que se generen migraciones que modifiquen la nulabilidad de la columna de la base de datos.

Para obtener más información sobre los tipos de referencia que aceptan valores NULL y cómo usarlos con EF Core, [consulte la página de documentación dedicada para esta característica](xref:core/miscellaneous/nullable-reference-types).

### <a name="explicit-configuration"></a>Configuración explícita

Una propiedad que sería opcional por Convención se puede configurar para que sea necesaria de la siguiente manera:

#### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=5)]

#### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a>Intercalaciones de columna

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 5.0.

Una intercalación se puede definir en columnas de texto, determinando cómo se comparan y ordenan. Por ejemplo, el siguiente fragmento de código configura una columna de SQL Server para que no distinga entre mayúsculas y minúsculas:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=ColumnCollation)]

Si todas las columnas de una base de datos necesitan usar una intercalación determinada, defina la intercalación en el nivel de base de datos en su lugar.

Puede encontrar información general sobre la compatibilidad de EF Core con las intercalaciones en la página de documentación de la [Intercalación](xref:core/miscellaneous/collations-and-case-sensitivity).

## <a name="column-comments"></a>Comentarios de columna

Puede establecer un Comentario de texto arbitrario que se establece en la columna de base de datos, lo que le permite documentar el esquema en la base de datos:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

> [!NOTE]
> La configuración de comentarios a través de anotaciones de datos se presentó en EF Core 5,0.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnComment.cs?name=ColumnComment&highlight=5)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnComment.cs?name=ColumnComment&highlight=5)]

***
