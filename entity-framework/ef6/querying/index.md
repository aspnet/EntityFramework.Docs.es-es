---
title: 'Consulta y búsqueda de entidades: EF6'
description: Consulta y búsqueda de entidades en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: ec0239ed54099226d009031af79388f5ae00cdc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065905"
---
# <a name="querying-and-finding-entities"></a>Consulta y búsqueda de entidades
En este tema se tratan las distintas formas de consultar datos mediante Entity Framework, incluidos LINQ y el método Find. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

## <a name="finding-entities-using-a-query"></a>Búsqueda de entidades mediante una consulta  

DbSet e IDbSet implementan IQueryable, por lo que pueden usarse como punto de partida para escribir una consulta LINQ en la base de datos. Este no es el lugar adecuado para una explicación detallada sobre LINQ, pero aquí tiene un par de ejemplos sencillos:  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

Tenga en cuenta que DbSet e IDbSet siempre crean las consultas en la base de datos, lo que siempre conlleva un viaje de ida y vuelta a la base de datos, aun cuando las entidades devueltas ya existan en el contexto. Una consulta se ejecuta en la base de datos cuando:  

- Se enumera mediante una instrucción **foreach** (C#) o **For Each** (Visual Basic).  
- Se enumera mediante una operación de recopilación como [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) o [ToList](https://msdn.microsoft.com/library/bb342261).  
- Los operadores LINQ, como [First](https://msdn.microsoft.com/library/bb291976) o [Any](https://msdn.microsoft.com/library/bb337697), se especifican en la parte más externa de la consulta.  
- Se llama a los métodos siguientes: el método de extensión [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) en DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) y Database.ExecuteSqlCommand.  

Cuando se devuelven los resultados de la base de datos, los objetos que no existen en el contexto se adjuntan a él. Si un objeto ya está en el contexto, se devuelve el objeto existente (los valores actual y original de las propiedades del objeto en la entrada **no** se sobrescriben con valores de la base de datos).  

Cuando se realiza una consulta, las entidades que se han agregado al contexto pero aún no se han guardado en la base de datos no se devuelven como parte del conjunto de resultados. Para obtener los datos que se encuentran en el contexto, vea [Datos locales](xref:ef6/querying/local-data).  

Si una consulta no devuelve filas de la base de datos, el resultado será una colección vacía, en lugar de **null**.  

## <a name="finding-entities-using-primary-keys"></a>Búsqueda de entidades mediante claves principales  

El método Find de DbSet usa el valor de clave principal para intentar buscar una entidad cuyo seguimiento realiza el contexto. Si la entidad no se encuentra en el contexto, se envía una consulta a la base de datos para buscar la entidad allí. Si la entidad no se encuentra en el contexto ni en la base de datos, se devuelve null.  

Find se diferencia del uso de una consulta en dos aspectos importantes:  

- Solo se realiza un viaje de ida y vuelta a la base de datos si la entidad con la clave especificada no se encuentra en el contexto.  
- Find devuelve entidades que están en estado Added. Es decir, Find devuelve entidades que se han agregado al contexto pero que aún no se han guardado en la base de datos.  
### <a name="finding-an-entity-by-primary-key"></a>Búsqueda de una entidad por clave principal  

En el código siguiente se muestran algunos usos de Find:  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a>Búsqueda de una entidad por clave principal compuesta  

Entity Framework permite que las entidades tengan claves compuestas, que son claves que se componen de más de una propiedad. Por ejemplo, podría tener una entidad BlogSettings que representara la configuración de un usuario para un blog determinado. Dado que un usuario solo tendría una entidad BlogSettings para cada blog, podría optar por convertir la clave principal de BlogSettings en una combinación de BlogId y Username. El código siguiente intenta encontrar la entidad BlogSettings con BlogId = 3 y Username = "johndoe1987":  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

Observe que con las claves compuestas hay que usar ColumnAttribute o la API fluida para especificar un orden de las propiedades de la clave compuesta. La llamada a Find debe usar este orden al especificar los valores que forman la clave.  
