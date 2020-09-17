---
title: 'Guardado de datos relacionados: EF Core'
description: Información sobre cómo guardar grafos de entidades relacionadas y administrar relaciones en Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/related-data
ms.openlocfilehash: 0ae6477032109787e63cae498bda7148dcaa5abe
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070983"
---
# <a name="saving-related-data"></a>Guardado de datos relacionados

Además de las entidades aisladas, también puede usar las relaciones definidas en el modelo.

> [!TIP]  
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) de este artículo en GitHub.

## <a name="adding-a-graph-of-new-entities"></a>Incorporación de un grafo de entidades nuevas

Si crea varias entidades relacionadas, agregar una de ellas al contexto hará que las otras también se agreguen.

En el ejemplo siguiente, el blog y tres entradas relacionadas se insertan en la base de datos. Las entradas se buscan y agregan, porque son accesibles a través de la propiedad de navegación `Blog.Posts`.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> Use la propiedad EntityEntry.State para establecer el estado de una sola unidad. Por ejemplo, `context.Entry(blog).State = EntityState.Modified`.

## <a name="adding-a-related-entity"></a>Incorporación de una entidad relacionada

Si hace referencia a una entidad nueva desde la propiedad de navegación de una entidad a la que el contexto ya hace seguimiento, se detectará la entidad y se insertará en la base de datos.

En el ejemplo siguiente, la entidad `post` se inserta porque se agrega a la propiedad `Posts` de la entidad `blog` que se capturó de la base de datos.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>Cambio de las relaciones

Si cambia la propiedad de navegación de una entidad, los cambios correspondientes se harán en la columna de clave externa de la base de datos.

En el ejemplo siguiente, la entidad `post` se actualiza para que pertenezca a la entidad `blog` nueva, porque su propiedad de navegación `Blog` está establecida para que apunte a `blog`. Tenga en cuenta que `blog` también se insertará en la base de datos porque se trata de una entidad nueva a la que hace referencia la propiedad de navegación de una entidad de la que el contexto (`post`) ya hace seguimiento.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>Eliminación de relaciones

Para quitar una relación, establezca una navegación de referencia en `null` o quite la entidad relacionada de una navegación de colección.

Quitar una relación puede tener efectos secundarios en la entidad dependiente, según el comportamiento de eliminación en cascada que esté configurado en la relación.

De manera predeterminada, en el caso de las relaciones obligatorias, hay configurado un comportamiento de eliminación en cascada y la entidad secundaria o dependiente se eliminará de la base de datos. En el caso de las relaciones opcionales, no hay configurada una eliminación en cascada de manera predeterminada, pero la propiedad de clave externa se establecerá en NULL.

Consulte la sección sobre las [relaciones obligatorias y opcionales](xref:core/modeling/relationships#required-and-optional-relationships) para más información sobre cómo se puede configurar la obligatoriedad de las relaciones.

Consulte el artículo sobre la [eliminación en cascada](xref:core/saving/cascade-delete) para más detalles sobre el funcionamiento de los comportamientos de eliminación en cascada, cómo se pueden configurar de manera explícita y cómo se seleccionan por convención.

En el ejemplo siguiente, se configura una eliminación en cascada en la relación entre `Blog` y `Post`, por lo que la entidad `post` se elimina de la base de datos.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
