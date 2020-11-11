---
title: 'Propiedades de sombra e indexador: EF Core'
description: Configurar las propiedades de sombra e indexador en un modelo de Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 180478212b683a271d2519cc1a4c79be5d3f11b9
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503194"
---
# <a name="shadow-and-indexer-properties"></a>Propiedades de sombra e indexador

Las propiedades de sombra son propiedades que no se definen en la clase de entidad de .NET pero que se definen para ese tipo de entidad en el modelo de EF Core. El valor y el estado de estas propiedades se mantienen únicamente en el seguimiento de cambios. Las propiedades de sombra son útiles cuando hay datos en la base de datos que no deben exponerse en los tipos de entidad asignados.

Las propiedades del indexador son propiedades de tipo de entidad, que están respaldadas por un [indexador](/dotnet/csharp/programming-guide/indexers/) en la clase de entidad de .net. Se puede tener acceso a ellos mediante el indizador en las instancias de clase .NET. También permite agregar propiedades adicionales al tipo de entidad sin cambiar la clase de CLR.

## <a name="foreign-key-shadow-properties"></a>Propiedades de sombra de clave externa

Las propiedades de sombra se utilizan con más frecuencia para las propiedades de clave externa, donde la relación entre dos entidades se representa mediante un valor de clave externa en la base de datos, pero la relación se administra en los tipos de entidad mediante propiedades de navegación entre los tipos de entidad. Por Convención, EF introducirá una propiedad Shadow cuando se detecte una relación, pero no se encuentra ninguna propiedad de clave externa en la clase de entidad dependiente.

La propiedad se denominará `<navigation property name><principal key property name>` (la navegación en la entidad dependiente, que apunta a la entidad principal, se usa para la nomenclatura). Si el nombre de la propiedad de clave principal incluye el nombre de la propiedad de navegación, el nombre será simplemente `<principal key property name>` . Si no hay ninguna propiedad de navegación en la entidad dependiente, el nombre del tipo de entidad de seguridad se usa en su lugar.

Por ejemplo, la siguiente lista de código dará como resultado la inclusión de una `BlogId` propiedad Shadow en la `Post` entidad:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Configurar propiedades de instantáneas

Puede usar la API fluida para configurar las propiedades de las instantáneas. Una vez que haya llamado a la sobrecarga de la cadena de `Property` , puede encadenar cualquiera de las llamadas de configuración que desee para otras propiedades. En el ejemplo siguiente, puesto que `Blog` no tiene ninguna propiedad CLR denominada `LastUpdated` , se crea una propiedad Shadow:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Si el nombre proporcionado al `Property` método coincide con el nombre de una propiedad existente (una propiedad Shadow o una definida en la clase de entidad), el código configurará esa propiedad existente en lugar de introducir una nueva propiedad Shadow.

## <a name="accessing-shadow-properties"></a>Obtener acceso a las propiedades de sombra

Los valores de las propiedades Shadow se pueden obtener y cambiar a través de la `ChangeTracker` API:

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

Se puede hacer referencia a las propiedades Shadow en consultas LINQ a través del `EF.Property` método estático:

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

No se puede tener acceso a las propiedades de sombra después de una consulta sin seguimiento, ya que el seguimiento de cambios no realiza un seguimiento de las entidades devueltas.

## <a name="configuring-indexer-properties"></a>Configuración de las propiedades del indexador

Puede usar la API fluida para configurar las propiedades del indexador. Una vez que haya llamado al método `IndexerProperty` , puede encadenar cualquiera de las llamadas de configuración que desee para otras propiedades. En el ejemplo siguiente, `Blog` tiene un indexador definido y se utilizará para crear una propiedad de indizador.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

Si el nombre proporcionado al `IndexerProperty` método coincide con el nombre de una propiedad de indizador existente, el código configurará esa propiedad existente. Si el tipo de entidad tiene una propiedad, que está respaldada por una propiedad en la clase de entidad, se produce una excepción, ya que solo se debe tener acceso a las propiedades del indexador a través del indexador.

## <a name="property-bag-entity-types"></a>Tipos de entidad de contenedor de propiedades

> [!NOTE]
> Se ha agregado compatibilidad con los tipos de entidad del contenedor de propiedades en EF Core 5,0.

Los tipos de entidad que contienen solo propiedades de indizador se conocen como tipos de entidad de contenedor de propiedades. Estos tipos de entidad no tienen propiedades de sombra; en su lugar, EF creará las propiedades del indexador. Actualmente solo `Dictionary<string, object>` se admite como un tipo de entidad de contenedor de propiedades. Debe configurarse como un tipo de entidad compartida con un nombre único y la `DbSet` propiedad correspondiente debe implementarse mediante una `Set` llamada.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
