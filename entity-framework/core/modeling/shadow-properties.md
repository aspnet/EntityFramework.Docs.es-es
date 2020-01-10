---
title: 'Propiedades de sombra: EF Core'
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 229cfd83f75b01dff9ac9ad30ee55c7cc727c19e
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781188"
---
# <a name="shadow-properties"></a>Propiedades reemplazadas

Las propiedades de sombra son propiedades que no están definidas en la clase de entidad de .NET pero que se definen para ese tipo de entidad en el modelo de EF Core. El valor y el estado de estas propiedades se mantienen únicamente en el seguimiento de cambios. Las propiedades Shadow son útiles cuando hay datos en la base de datos que no se deben exponer en los tipos de entidad asignados.

## <a name="foreign-key-shadow-properties"></a>Propiedades de sombra de clave externa

Las propiedades de sombra se utilizan con más frecuencia para las propiedades de clave externa, donde la relación entre dos entidades se representa mediante un valor de clave externa en la base de datos, pero la relación se administra en los tipos de entidad mediante las propiedades de navegación entre la entidad. distintos. Por Convención, EF introducirá una propiedad Shadow cuando se detecte una relación, pero no se encuentra ninguna propiedad de clave externa en la clase de entidad dependiente.

La propiedad se denominará `<navigation property name><principal key property name>` (la navegación en la entidad dependiente, que apunta a la entidad principal, se usa para la nomenclatura). Si el nombre de la propiedad de clave principal incluye el nombre de la propiedad de navegación, el nombre simplemente se `<principal key property name>`. Si no hay ninguna propiedad de navegación en la entidad dependiente, el nombre del tipo de entidad de seguridad se usa en su lugar.

Por ejemplo, la siguiente lista de código dará como resultado la inclusión de una `BlogId` propiedad Shadow en la entidad `Post`:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Configurar propiedades de instantáneas

Puede usar la API fluida para configurar las propiedades de las instantáneas. Una vez que haya llamado a la sobrecarga de la cadena de `Property`, puede encadenar cualquiera de las llamadas de configuración que desee para otras propiedades. En el ejemplo siguiente, puesto que `Blog` no tiene ninguna propiedad CLR denominada `LastUpdated`, se crea una propiedad Shadow:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Si el nombre proporcionado al método `Property` coincide con el nombre de una propiedad existente (una propiedad Shadow o una definida en la clase de entidad), el código configurará esa propiedad existente en lugar de introducir una nueva propiedad Shadow.

## <a name="accessing-shadow-properties"></a>Obtener acceso a las propiedades de sombra

Los valores de las propiedades Shadow se pueden obtener y cambiar a través de la API de `ChangeTracker`:

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

Se puede hacer referencia a las propiedades Shadow en consultas LINQ a través del método estático `EF.Property`:

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```
