---
title: 'Campos de respaldo: EF Core'
description: Configurar campos de respaldo para las propiedades de un modelo de Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/modeling/backing-field
ms.openlocfilehash: 4f5680b14c7f0e1fa5128d366f2960c4ae9b3735
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063691"
---
# <a name="backing-fields"></a>Campos de respaldo

Los campos de respaldo permiten a EF leer o escribir en un campo en lugar de una propiedad. Esto puede ser útil cuando se usa la encapsulación en la clase para restringir el uso de y/o mejorar la semántica en torno al acceso a los datos por código de aplicación, pero el valor debe leerse o escribirse en la base de datos sin usar esas restricciones o mejoras.

## <a name="basic-configuration"></a>Configuración básica

Por Convención, se detectarán los campos siguientes como campos de respaldo para una propiedad determinada (que se muestra en orden de prioridad).

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

En el ejemplo siguiente, la `Url` propiedad se configura para tener `_url` como su campo de respaldo:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Tenga en cuenta que los campos de respaldo solo se detectan para las propiedades que se incluyen en el modelo. Para obtener más información sobre las propiedades que se incluyen en el modelo, vea [incluir & excluyendo las propiedades](xref:core/modeling/entity-properties).

También puede configurar los campos de respaldo mediante una anotación de datos (disponible en EFCore 5,0) o la API fluida, por ejemplo, si el nombre del campo no se corresponde con las convenciones anteriores:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Acceso a campos y propiedades

De forma predeterminada, EF siempre leerá y escribirá en el campo de respaldo, suponiendo que se haya configurado correctamente, y que nunca usará la propiedad. Sin embargo, EF también admite otros patrones de acceso. Por ejemplo, en el ejemplo siguiente se indica a EF que escriba en el campo de respaldo solo mientras materializa y use la propiedad en todos los demás casos:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Vea la [enumeración PropertyAccessMode](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) para obtener el conjunto completo de opciones admitidas.

> [!NOTE]
> Con EF Core 3,0, el modo de acceso de propiedad predeterminado cambió de `PreferFieldDuringConstruction` a `PreferField` .

## <a name="field-only-properties"></a>Propiedades de solo campo

También puede crear una propiedad conceptual en el modelo que no tiene una propiedad de CLR correspondiente en la clase de entidad, sino que usa un campo para almacenar los datos en la entidad. Esto es diferente de [las propiedades de las instantáneas](xref:core/modeling/shadow-properties), donde los datos se almacenan en el seguimiento de cambios, en lugar de en el tipo CLR de la entidad. Las propiedades de solo campo se utilizan normalmente cuando la clase de entidad usa métodos en lugar de propiedades para obtener o establecer valores, o en casos en los que los campos no se deben exponer en absoluto en el modelo de dominio (por ejemplo, las claves principales).

Puede configurar una propiedad de solo campo proporcionando un nombre en la `Property(...)` API:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF intentará buscar una propiedad CLR con el nombre especificado o un campo si no se encuentra una propiedad. Si no se encuentra ninguna propiedad ni un campo, se configurará una propiedad Shadow en su lugar.

Es posible que tenga que hacer referencia a una propiedad de solo campo desde las consultas LINQ, pero estos campos suelen ser privados. Puede usar el `EF.Property(...)` método en una consulta LINQ para hacer referencia al campo:

```csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
