---
title: 'Trabajar con Estados de entidad: EF6'
description: Trabajar con Estados de entidad en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 981bdbca982403338f3f65a41f601641d59d74d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619981"
---
# <a name="working-with-entity-states"></a>Trabajar con Estados de entidad
En este tema se explica cómo agregar y adjuntar entidades a un contexto y cómo Entity Framework procesa estas durante el SaveChanges.
Entity Framework se encarga del seguimiento del estado de las entidades mientras están conectadas a un contexto, pero en escenarios desconectados o de N niveles, puede permitir que EF sepa en qué estado deben estar las entidades.
Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

## <a name="entity-states-and-savechanges"></a>Estados de la entidad y SaveChanges

Una entidad puede estar en uno de cinco Estados, tal y como se define en la enumeración EntityState. Estos estados son:  

- Agregado: el contexto está realizando el seguimiento de la entidad, pero aún no existe en la base de datos  
- Unchanged: el contexto realiza un seguimiento de la entidad y existe en la base de datos, y sus valores de propiedad no han cambiado con respecto a los valores de la base de datos.  
- Modificado: el contexto está realizando el seguimiento de la entidad y existe en la base de datos, y se han modificado algunos o todos sus valores de propiedad.  
- Eliminado: el contexto realiza un seguimiento de la entidad y existe en la base de datos, pero se ha marcado para su eliminación de la base de datos la próxima vez que se llame a SaveChanges.  
- Detached: el contexto no está realizando el seguimiento de la entidad  

SaveChanges realiza diferentes acciones para entidades en diferentes Estados:  

- SaveChanges no toca las entidades sin modificar. Las actualizaciones no se envían a la base de datos para entidades en el estado sin cambios.  
- Las entidades agregadas se insertan en la base de datos y, a continuación, se vuelven sin cambios cuando se devuelve SaveChanges.  
- Las entidades modificadas se actualizan en la base de datos y, a continuación, se vuelven sin cambios cuando se devuelve SaveChanges.  
- Las entidades eliminadas se eliminan de la base de datos y, a continuación, se desasocian del contexto.  

En los ejemplos siguientes se muestran las maneras en las que se puede cambiar el estado de una entidad o un gráfico de entidades.  

## <a name="adding-a-new-entity-to-the-context"></a>Agregar una nueva entidad al contexto  

Se puede Agregar una nueva entidad al contexto llamando al método Add en DbSet.
Esto coloca la entidad en el estado agregado, lo que significa que se insertará en la base de datos la próxima vez que se llame a SaveChanges.
Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

Otra manera de agregar una nueva entidad al contexto es cambiar su estado a agregado. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

Por último, puede Agregar una nueva entidad al contexto al enlazarla a otra entidad de la que ya se está realizando el seguimiento.
Esto podría ser agregando la nueva entidad a la propiedad de navegación de colección de otra entidad o estableciendo una propiedad de navegación de referencia de otra entidad para que apunte a la nueva entidad. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

Tenga en cuenta que, para todos estos ejemplos, si la entidad que se va a agregar tiene referencias a otras entidades de las que todavía no se ha realizado un seguimiento, estas nuevas entidades también se agregarán al contexto y se insertarán en la base de datos la próxima vez que se llame a SaveChanges.  

## <a name="attaching-an-existing-entity-to-the-context"></a>Asociar una entidad existente al contexto  

Si tiene una entidad que sabe que ya existe en la base de datos pero en la que no se realiza el seguimiento actualmente por el contexto, puede indicar al contexto que realice el seguimiento de la entidad mediante el método Attach en DbSet. La entidad estará en el estado Unchanged en el contexto. Por ejemplo:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

Tenga en cuenta que no se realizarán cambios en la base de datos si se llama a SaveChanges sin realizar ninguna otra manipulación de la entidad adjunta. Esto se debe a que la entidad está en el estado Unchanged.  

Otra manera de asociar una entidad existente al contexto es cambiar su estado a sin cambios. Por ejemplo:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

Tenga en cuenta que para ambos ejemplos, si la entidad que se va a asociar tiene referencias a otras entidades de las que todavía no se ha realizado un seguimiento, estas nuevas entidades también se adjuntarán al contexto en el estado sin cambios.  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>Adjuntar una entidad existente pero modificada al contexto  

Si tiene una entidad que sabe que ya existe en la base de datos, pero en la que se pueden realizar cambios, puede indicar al contexto que adjunte la entidad y establezca su estado en modificado.
Por ejemplo:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

Al cambiar el estado a modificado, todas las propiedades de la entidad se marcarán como modificadas y todos los valores de propiedad se enviarán a la base de datos cuando se llame a SaveChanges.  

Tenga en cuenta que si la entidad que se va a asociar tiene referencias a otras entidades de las que todavía no se ha realizado un seguimiento, estas nuevas entidades se adjuntarán al contexto en el estado Unchanged (no se modificarán automáticamente).
Si tiene varias entidades que deben marcarse como modificadas, debe establecer el estado de cada una de estas entidades de forma individual.  

## <a name="changing-the-state-of-a-tracked-entity"></a>Cambiar el estado de una entidad de la que se ha realizado un seguimiento  

Puede cambiar el estado de una entidad de la que ya se realiza el seguimiento estableciendo la propiedad State en su entrada. Por ejemplo:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

Tenga en cuenta que la llamada a Add o attach para una entidad de la que ya se ha realizado un seguimiento también se puede usar para cambiar el estado de la entidad. Por ejemplo, al llamar a attach para una entidad que está actualmente en el estado agregado, se cambiará su estado a sin cambios.  

## <a name="insert-or-update-pattern"></a>Insertar o actualizar patrón  

Un patrón común para algunas aplicaciones es agregar una entidad como nueva (lo que da como resultado una inserción de base de datos) o adjuntar una entidad como existente y marcarla como modificada (lo que da como resultado una actualización de la base de datos) en función del valor de la clave principal.
Por ejemplo, al utilizar las claves principales de enteros generados por la base de datos, es habitual tratar una entidad con una clave cero como nueva y una entidad con una clave distinta de cero como existente.
Este patrón se puede lograr estableciendo el estado de la entidad en función de una comprobación del valor de la clave principal. Por ejemplo:  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

Tenga en cuenta que al cambiar el estado a modificado, todas las propiedades de la entidad se marcarán como modificadas y todos los valores de propiedad se enviarán a la base de datos cuando se llame a SaveChanges.  
