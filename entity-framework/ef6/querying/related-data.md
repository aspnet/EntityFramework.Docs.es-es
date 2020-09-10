---
title: 'Carga de entidades relacionadas: EF6'
description: Carga de entidades relacionadas en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
uid: ef6/querying/related-data
ms.openlocfilehash: 9b4e8ecda618e11e87cd595502210234b1f3e27d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620260"
---
# <a name="loading-related-entities"></a>Carga de entidades relacionadas

Entity Framework admite tres maneras de cargar datos relacionados: carga diligente, carga diferida y carga explícita. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.

## <a name="eagerly-loading"></a>Cargando diligentemente

La carga diligente es el proceso por el cual una consulta para un tipo de entidad también carga las entidades relacionadas como parte de la consulta. La carga diligente se logra mediante el uso del método include. Por ejemplo, las consultas siguientes cargarán blogs y todas las entradas relacionadas con cada blog.

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> Include es un método de extensión en el espacio de nombres System. Data. Entity, por lo que debe asegurarse de que está usando ese espacio de nombres.

### <a name="eagerly-loading-multiple-levels"></a>Carga de varios niveles diligentemente

También es posible cargar diligentemente varios niveles de entidades relacionadas. En las consultas siguientes se muestran ejemplos de cómo hacerlo para las propiedades de navegación de colección y de referencia.  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> Actualmente no es posible filtrar las entidades relacionadas que se cargan. Incluir siempre incluirá todas las entidades relacionadas.  

## <a name="lazy-loading"></a>Carga diferida

La carga diferida es el proceso por el que una entidad o colección de entidades se carga automáticamente desde la base de datos la primera vez que se tiene acceso a una propiedad que hace referencia a la entidad o entidades. Al usar tipos de entidad POCO, la carga diferida se consigue creando instancias de tipos de proxy derivados y, a continuación, reemplazando las propiedades virtuales para agregar el enlace de carga. Por ejemplo, al usar la clase de entidad de blog que se define a continuación, se cargarán las publicaciones relacionadas la primera vez que se tenga acceso a la propiedad de navegación posts:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a>Desactivación de la carga diferida para la serialización

La carga diferida y la serialización no se combinan bien y, si no tiene cuidado, puede finalizar la consulta de toda la base de datos, solo porque está habilitada la carga diferida. La mayoría de los serializadores funcionan mediante el acceso a cada propiedad en una instancia de un tipo. El acceso de propiedad desencadena la carga diferida, por lo que se serializan más entidades. En esas entidades se tiene acceso a las propiedades de, e incluso se cargan más entidades. Se recomienda desactivar la carga diferida antes de serializar una entidad. Se muestra cómo hacerlo en las secciones siguientes.

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>Desactivar la carga diferida para propiedades de navegación específicas

La carga diferida de la colección de publicaciones se puede desactivar haciendo que la propiedad postes no sea virtual:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

La carga de la colección de publicaciones se puede seguir usando la carga diligente (consulte la *carga* rápida anterior) o el método Load (vea *carga explícita* a continuación).

### <a name="turn-off-lazy-loading-for-all-entities"></a>Desactivar la carga diferida para todas las entidades

La carga diferida se puede desactivar para todas las entidades en el contexto estableciendo una marca en la propiedad de configuración. Por ejemplo:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

La carga de entidades relacionadas todavía se puede lograr mediante la carga diligente (consulte la *carga* rápida anterior) o el método Load (vea *carga explícita* a continuación).

## <a name="explicitly-loading"></a>Cargar explícitamente

Incluso con la carga diferida deshabilitada, todavía es posible cargar de forma diferida las entidades relacionadas, pero debe realizarse con una llamada explícita. Para ello, use el método Load en la entrada de la entidad relacionada. Por ejemplo:

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> El método de referencia debe usarse cuando una entidad tiene una propiedad de navegación a otra entidad única. Por otro lado, el método de colección debe usarse cuando una entidad tiene una propiedad de navegación a una colección de otras entidades.

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>Aplicar filtros al cargar explícitamente entidades relacionadas

El método de consulta proporciona acceso a la consulta subyacente que utilizará Entity Framework al cargar las entidades relacionadas. Después, puede usar LINQ para aplicar filtros a la consulta antes de ejecutarlo con una llamada a un método de extensión LINQ como ToList, Load, etc. El método de consulta se puede utilizar con propiedades de navegación de referencia y de colección, pero es muy útil para las colecciones en las que se puede usar para cargar solo parte de la colección. Por ejemplo:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

Cuando se usa el método de consulta, suele ser mejor desactivar la carga diferida para la propiedad de navegación. Esto se debe a que, de lo contrario, es posible que el mecanismo de carga diferida cargue automáticamente toda la colección antes o después de que se haya ejecutado la consulta filtrada.

> [!NOTE]
> Aunque la relación se puede especificar como una cadena en lugar de una expresión lambda, el IQueryable devuelto no es genérico cuando se usa una cadena y, por lo tanto, el método Cast suele ser necesario antes de que se pueda hacer nada útil con él.

## <a name="using-query-to-count-related-entities-without-loading-them"></a>Usar Query para contar entidades relacionadas sin cargarlas

A veces resulta útil saber el número de entidades relacionadas con otra entidad de la base de datos sin incurrir realmente en el costo de cargar todas esas entidades. Se puede utilizar el método de consulta con el método Count de LINQ para hacerlo. Por ejemplo:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
