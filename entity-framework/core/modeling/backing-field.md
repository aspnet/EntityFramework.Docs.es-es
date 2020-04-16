---
title: Campos de respaldo - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434180"
---
# <a name="backing-fields"></a>Campos de respaldo

Los campos de respaldo permiten a EF leer o escribir en un campo en lugar de en una propiedad. Esto puede ser útil cuando la encapsulación en la clase se utiliza para restringir el uso de y/o mejorar la semántica en torno al acceso a los datos por código de aplicación, pero el valor debe leerse y/o escribirse en la base de datos sin usar esas restricciones/mejoras.

## <a name="basic-configuration"></a>Configuración básica

Por convención, los siguientes campos se detectarán como campos de respaldo para una propiedad determinada (incluidos en orden de prioridad). 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

En el ejemplo `Url` siguiente, la `_url` propiedad está configurada para tener como campo de respaldo:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Tenga en cuenta que los campos de respaldo solo se detectan para las propiedades que se incluyen en el modelo. Para obtener más información sobre qué propiedades se incluyen en el modelo, vea [Incluir & Excluir propiedades](included-properties.md).

También puede configurar campos de respaldo mediante una anotación de datos o la API fluida, por ejemplo, si el nombre del campo no se corresponde con las convenciones anteriores:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Acceso a campos y propiedades

De forma predeterminada, EF siempre leerá y escribirá en el campo de respaldo , suponiendo que se haya configurado correctamente, y nunca usará la propiedad. Sin embargo, EF también admite otros patrones de acceso. Por ejemplo, en el ejemplo siguiente se indica a EF que escriba en el campo de respaldo solo durante la materialización y que utilice la propiedad en todos los demás casos:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Consulte la [enumeración PropertyAccessMode](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) para obtener el conjunto completo de opciones admitidas.

> [!NOTE]
> Con EF Core 3.0, el modo `PreferFieldDuringConstruction` `PreferField`de acceso a propiedades predeterminado cambió de .

## <a name="field-only-properties"></a>Propiedades solo de campo

También puede crear una propiedad conceptual en el modelo que no tenga una propiedad CLR correspondiente en la clase de entidad, sino que use un campo para almacenar los datos en la entidad. Esto es diferente de [Shadow Properties](shadow-properties.md), donde los datos se almacenan en el rastreador de cambios, en lugar de en el tipo CLR de la entidad. Las propiedades de solo campo se usan normalmente cuando la clase de entidad utiliza métodos en lugar de propiedades para obtener o establecer valores, o en casos en los que los campos no se deben exponer en absoluto en el modelo de dominio (por ejemplo, claves principales).

Puede configurar una propiedad de solo campo `Property(...)` proporcionando un nombre en la API:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF intentará encontrar una propiedad CLR con el nombre especificado o un campo si no se encuentra una propiedad. Si no se encuentran ni una propiedad ni un campo, se configurará una propiedad shadow en su lugar.

Es posible que deba hacer referencia a una propiedad de solo campo de consultas LINQ, pero estos campos suelen ser privados. Puede usar `EF.Property(...)` el método en una consulta LINQ para hacer referencia al campo:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
