---
title: 'Guardado básico: EF Core'
description: Información básica sobre cómo agregar, actualizar y eliminar datos con Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: c6288fc8ca79b78b6c377f25e3874211b38059b2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063639"
---
# <a name="basic-save"></a>Guardado básico

Obtenga información sobre cómo agregar, modificar y quitar datos mediante las clases de entidad y contexto.

> [!TIP]  
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) de este artículo en GitHub.

## <a name="adding-data"></a>Agregar datos

Use el método *DbSet.Add* para agregar instancias nuevas de las clases de entidad. Los datos se insertarán en la base de datos cuando llame a *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> Los métodos Add, Attach y Update funcionan en todo el grafo de entidades que se pasaron a ellos, tal como se describe en la sección de [datos relacionados](xref:core/saving/related-data). También puede usar la propiedad EntityEntry.State para establecer el estado de una sola unidad. Por ejemplo, `context.Entry(blog).State = EntityState.Modified`.

## <a name="updating-data"></a>Actualización de datos

EF detectará automáticamente los cambios hechos en una entidad existente de la que hace seguimiento el contexto. Esto incluye entidades que se cargan o consultan desde la base de datos y las entidades que se agregaron y guardaron anteriormente en la base de datos.

Solo debe modificar los valores asignados a las propiedades y llamar a *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>Eliminar datos

Use el método *DbSet.Remove* para eliminar las instancias de las clases de entidad.

Si la entidad ya existe en la base de datos, se eliminará durante *SaveChanges*. Si la entidad todavía no se guarda en la base de datos (es decir, si se hace seguimiento cuando se agrega), se quitará del contexto y ya no se insertará cuando se llame a *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>Varias operaciones en una única llamada a SaveChanges

Puede combinar varias operaciones Add, Update y Remove en una sola llamada a *SaveChanges*.

> [!NOTE]  
> Para la mayoría de los proveedores de base de datos, *SaveChanges* es transaccional. Esto significa que todas las operaciones se realizarán correctamente o presentarán un error y que nunca se aplicarán de manera parcial.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
