---
title: 'Datos locales: EF6'
description: Datos locales en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/local-data
ms.openlocfilehash: cff3612072ad9caa057d8589d47cc75ae32be8db
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065880"
---
# <a name="local-data"></a>Datos locales
La ejecución de una consulta LINQ directamente en una DbSet siempre enviará una consulta a la base de datos, pero se puede tener acceso a los datos que están actualmente en memoria mediante la propiedad DbSet. local. También puede acceder a la información adicional EF realiza el seguimiento de las entidades mediante los métodos DbContext. entry y DbContext. ChangeTracker. entrys. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

## <a name="using-local-to-look-at-local-data"></a>Usar local para examinar datos locales  

La propiedad local de DbSet proporciona acceso sencillo a las entidades del conjunto cuyo seguimiento realiza actualmente el contexto y que no se han marcado como eliminados. El acceso a la propiedad local nunca hace que se envíe una consulta a la base de datos. Esto significa que normalmente se utiliza después de que ya se haya realizado una consulta. El método de extensión de carga se puede usar para ejecutar una consulta de modo que el contexto realice un seguimiento de los resultados. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

Si teníamos dos blogs en la base de datos: ' ADO.NET blog ' con BlogId de 1 y ' el blog de Visual Studio ' con un BlogId de 2-podríamos esperar el siguiente resultado:  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

Esto ilustra tres puntos:  

- El nuevo blog ' mi nuevo blog ' está incluido en la colección local aunque aún no se ha guardado en la base de datos. Este blog tiene una clave principal de cero porque la base de datos aún no ha generado una clave real para la entidad.  
- El "blog de ADO.NET" no se incluye en la colección local aunque el contexto siga realizando el seguimiento. Esto se debe a que se ha quitado de DbSet y, por tanto, se ha marcado como eliminado.  
- Cuando se usa DbSet para realizar una consulta, el blog marcado para su eliminación (ADO.NET blog) se incluye en los resultados y el nuevo blog (mi nuevo blog) que todavía no se ha guardado en la base de datos no se incluye en los resultados. Esto se debe a que DbSet está realizando una consulta en la base de datos y los resultados devueltos siempre reflejan lo que hay en la base de datos.  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>Usar local para agregar y quitar entidades del contexto  

La propiedad local en DbSet devuelve un [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) con eventos enlazados de modo que permanece sincronizado con el contenido del contexto. Esto significa que se pueden agregar o quitar entidades de la colección local o de DbSet. También significa que las consultas que incorporan nuevas entidades al contexto darán lugar a que la colección local se actualice con esas entidades. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

Suponiendo que tenemos algunas entradas etiquetadas con "Entity-Framework" y "asp.net", la salida puede tener un aspecto similar al siguiente:  

```console
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

Esto ilustra tres puntos:  

- La nueva publicación ' What's New in EF ' que se agregó a la colección local se hace seguimiento por el contexto en el estado Added. Por lo tanto, se insertará en la base de datos cuando se llame a SaveChanges.  
- La publicación que se quitó de la colección local (guía para principiantes de EF) ahora está marcada como eliminada en el contexto. Por lo tanto, se eliminará de la base de datos cuando se llame a SaveChanges.  
- La publicación adicional (guía para principiantes de ASP.NET) cargada en el contexto con la segunda consulta se agrega automáticamente a la colección local.  

Una cuestión final que hay que tener en cuenta sobre local es que, dado que es un rendimiento de ObservableCollection no es excelente para un gran número de entidades. Por lo tanto, si está tratando con miles de entidades en el contexto, puede que no sea aconsejable usar local.  

## <a name="using-local-for-wpf-data-binding"></a>Usar local para el enlace de datos de WPF  

La propiedad local en DbSet se puede usar directamente para el enlace de datos en una aplicación WPF porque es una instancia de ObservableCollection. Tal como se describe en las secciones anteriores, esto significa que permanecerá sincronizada automáticamente con el contenido del contexto y el contenido del contexto permanecerá sincronizado automáticamente con él. Tenga en cuenta que debe rellenar previamente la colección local con datos para que sea todo lo que se va a enlazar a, ya que local nunca genera una consulta de base de datos.  

No es un lugar adecuado para un ejemplo de enlace de datos de WPF completo, pero los elementos clave son:  

- Configuración de un origen de enlace  
- Enlazarlo a la propiedad local del conjunto  
- Rellene el local mediante una consulta a la base de datos.  

## <a name="wpf-binding-to-navigation-properties"></a>Enlace de WPF a propiedades de navegación  

Si está realizando el enlace de datos principal/detalle, puede enlazar la vista de detalle a una propiedad de navegación de una de las entidades. Una manera fácil de realizar este trabajo es usar ObservableCollection para la propiedad de navegación. Por ejemplo:  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>Usar local para limpiar entidades en SaveChanges  

En la mayoría de los casos, las entidades quitadas de una propiedad de navegación no se marcarán automáticamente como eliminadas en el contexto. Por ejemplo, si quita un objeto post de la colección blog. posts, esa publicación no se eliminará automáticamente cuando se llame a SaveChanges. Si necesita que se elimine, puede que necesite encontrar estas entidades pendientes y marcarlas como eliminadas antes de llamar a SaveChanges o como parte de un SaveChanges invalidado. Por ejemplo:  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

El código anterior usa la colección local para buscar todas las publicaciones y marca cualquier que no tenga una referencia de blog como eliminada. La llamada a ToList es necesaria porque, de lo contrario, se modificará la colección mediante la llamada a Remove mientras se está enumerando. En la mayoría de las demás situaciones, puede realizar consultas directamente en la propiedad local sin usar ToList primero.  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>Usar local y ToBindingList para el enlace de datos Windows Forms  

Windows Forms no admite el enlace de datos de plena fidelidad mediante ObservableCollection directamente. Sin embargo, todavía puede usar la propiedad local DbSet para el enlace de datos con el fin de obtener todas las ventajas descritas en las secciones anteriores. Esto se logra mediante el método de extensión ToBindingList, que crea una implementación de [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) respaldada por el ObservableCollection local.  

No es un lugar adecuado para un ejemplo de enlace de datos completo Windows Forms pero los elementos clave son:  

- Configuración de un origen de enlace de objeto  
- Enlácelo a la propiedad local del conjunto mediante local. ToBindingList ()  
- Rellenar local mediante una consulta a la base de datos  

## <a name="getting-detailed-information-about-tracked-entities"></a>Obtención de información detallada sobre las entidades sometidas a seguimiento  

Muchos de los ejemplos de esta serie usan el método entry para devolver una instancia de DbEntityEntry para una entidad. Este objeto de entrada actúa como punto de partida para recopilar información sobre la entidad, como su estado actual, así como para realizar operaciones en la entidad, como cargar explícitamente una entidad relacionada.  

Los métodos de entrada devuelven objetos DbEntityEntry para muchas o todas las entidades de las que el contexto realiza un seguimiento. Esto le permite recopilar información o realizar operaciones en muchas entidades en lugar de en una sola entrada. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

Observamos que estamos introduciendo una clase Author y Reader en el ejemplo. ambas clases implementan la interfaz IPerson.  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

Supongamos que tenemos los siguientes datos en la base de datos:

Blog con BlogId = 1 y name = ' ADO.NET blog '  
Blog con BlogId = 2 y name = "blog de Visual Studio"  
Blog con BlogId = 3 y name = ' .NET Framework blog '  
Autor con AuthorId = 1 y name = ' Joe Bloggs '  
Lector con ReaderId = 1 y name = ' John Doe '  

La salida de la ejecución del código sería:  

```console
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

En estos ejemplos se muestran varios puntos:  

- Los métodos de entrada devuelven entradas para entidades en todos los Estados, incluido Deleted. Compare esto con el local, que excluye las entidades eliminadas.  
- Se devuelven las entradas de todos los tipos de entidad cuando se usa el método de entradas no genéricas. Cuando se usa el método de entradas genéricas, las entradas solo se devuelven para las entidades que son instancias del tipo genérico. Se usó anteriormente para obtener entradas de todos los blogs. También se usaba para obtener entradas para todas las entidades que implementan IPerson. Esto demuestra que el tipo genérico no tiene que ser un tipo de entidad real.  
- LINQ to Objects se puede utilizar para filtrar los resultados devueltos. Se usó anteriormente para buscar entidades de cualquier tipo, siempre y cuando se modifiquen.  

Tenga en cuenta que las instancias de DbEntityEntry siempre contienen una entidad que no es NULL. Las entradas de relación y las entradas de código auxiliar no se representan como instancias de DbEntityEntry, por lo que no es necesario filtrarlas.
