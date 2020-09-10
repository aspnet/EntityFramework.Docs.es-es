---
title: 'Claves: EF Core'
description: Cómo configurar claves para tipos de entidad al utilizar Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: ed4600258de460877ee4f94586a2fbe794e82ed1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619337"
---
# <a name="keys"></a>Claves

Una clave actúa como identificador único para cada instancia de la entidad. La mayoría de las entidades de EF tienen una sola clave, que se asigna al concepto de una *clave principal* en las bases de datos relacionales (para entidades sin claves, vea [entidades](xref:core/modeling/keyless-entity-types)sin clave). Las entidades pueden tener claves adicionales más allá de la clave principal (consulte [claves alternativas](#alternate-keys) para obtener más información).

Por Convención, se `Id` configurará una propiedad denominada o `<type name>Id` como la clave principal de una entidad.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> Los [tipos de entidad de propiedad](xref:core/modeling/owned-entities) usan reglas diferentes para definir claves.

Puede configurar una única propiedad para que sea la clave principal de una entidad, como se indica a continuación:

## <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

También puede configurar varias propiedades para que sean la clave de una entidad, lo que se conoce como clave compuesta. Las claves compuestas solo se pueden configurar mediante la API fluida; las convenciones nunca configurarán una clave compuesta y no se pueden usar anotaciones de datos para configurar una.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a>Nombre de clave principal

Por Convención, en las bases de datos relacionales, las claves principales se crean con el nombre `PK_<type name>` . Puede configurar el nombre de la restricción Primary Key de la manera siguiente:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a>Tipos de clave y valores

Aunque EF Core admite el uso de propiedades de cualquier tipo primitivo como clave principal, `string` incluidos `Guid` , `byte[]` y otros, no todas las bases de datos admiten todos los tipos como claves. En algunos casos, los valores de clave se pueden convertir automáticamente a un tipo compatible, de lo contrario, la conversión se debe [especificar manualmente](xref:core/modeling/value-conversions).

Las propiedades de clave deben tener siempre un valor no predeterminado al agregar una nueva entidad al contexto, pero [la base de datos generará](xref:core/modeling/generated-properties)algunos tipos. En ese caso, EF intentará generar un valor temporal cuando la entidad se agregue con fines de seguimiento. Después de llamar a [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) , el valor temporal se reemplazará por el valor generado por la base de datos.

> [!Important]
> Si una propiedad de clave tiene su valor generado por la base de datos y se especifica un valor no predeterminado al agregar una entidad, EF asumirá que la entidad ya existe en la base de datos e intentará actualizarla en lugar de insertar una nueva. Para evitar esto, desactive la generación de valores o vea [Cómo especificar valores explícitos para las propiedades generadas](xref:core/saving/explicit-values-generated-properties).

## <a name="alternate-keys"></a>Claves alternativas

Una clave alternativa actúa como identificador único alternativo para cada instancia de entidad además de la clave principal; se puede usar como destino de una relación. Al utilizar una base de datos relacional, se asigna al concepto de un índice o una restricción únicos en las columnas de clave alternativas y una o varias restricciones de clave externa que hacen referencia a las columnas.

> [!TIP]
> Si solo desea exigir la unicidad en una columna, defina un índice único en lugar de una clave alternativa (consulte [índices](xref:core/modeling/indexes)). En EF, las claves alternativas son de solo lectura y proporcionan una semántica adicional sobre índices únicos, ya que se pueden usar como destino de una clave externa.

Normalmente, se introducen claves alternativas cuando sea necesario y no es necesario configurarlas manualmente. Por Convención, se introduce una clave alternativa cuando se identifica una propiedad que no es la clave principal como destino de una relación.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

También puede configurar una sola propiedad para que sea una clave alternativa:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

También puede configurar varias propiedades para que sean una clave alternativa (conocida como clave alternativa compuesta):

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

Por último, por Convención, el índice y la restricción que se introducen para una clave alternativa se denominarán `AK_<type name>_<property name>` (para las claves alternativas compuestas `<property name>` se convierte en una lista de nombres de propiedad separados por guiones bajos). Puede configurar el nombre del índice de la clave alternativa y la restricción UNIQUE:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
