---
title: 'Datos locales: EF6'
description: Datos locales en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/local-data
ms.openlocfilehash: 5763424ab295ed6ceec655368fe3565a10c911d3
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073917"
---
# <a name="local-data"></a><span data-ttu-id="7742e-103">Datos locales</span><span class="sxs-lookup"><span data-stu-id="7742e-103">Local Data</span></span>
<span data-ttu-id="7742e-104">La ejecución de una consulta LINQ directamente en una DbSet siempre enviará una consulta a la base de datos, pero se puede tener acceso a los datos que están actualmente en memoria mediante la propiedad DbSet. local.</span><span class="sxs-lookup"><span data-stu-id="7742e-104">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="7742e-105">También puede acceder a la información adicional EF realiza el seguimiento de las entidades mediante los métodos DbContext. entry y DbContext. ChangeTracker. entrys.</span><span class="sxs-lookup"><span data-stu-id="7742e-105">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="7742e-106">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="7742e-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="7742e-107">Usar local para examinar datos locales</span><span class="sxs-lookup"><span data-stu-id="7742e-107">Using Local to look at local data</span></span>  

<span data-ttu-id="7742e-108">La propiedad local de DbSet proporciona acceso sencillo a las entidades del conjunto cuyo seguimiento realiza actualmente el contexto y que no se han marcado como eliminados.</span><span class="sxs-lookup"><span data-stu-id="7742e-108">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="7742e-109">El acceso a la propiedad local nunca hace que se envíe una consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7742e-109">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="7742e-110">Esto significa que normalmente se utiliza después de que ya se haya realizado una consulta.</span><span class="sxs-lookup"><span data-stu-id="7742e-110">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="7742e-111">El método de extensión de carga se puede usar para ejecutar una consulta de modo que el contexto realice un seguimiento de los resultados.</span><span class="sxs-lookup"><span data-stu-id="7742e-111">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="7742e-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7742e-112">For example:</span></span>  

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

<span data-ttu-id="7742e-113">Si teníamos dos blogs en la base de datos: ' ADO.NET blog ' con BlogId de 1 y ' el blog de Visual Studio ' con un BlogId de 2-podríamos esperar el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="7742e-113">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="7742e-114">Esto ilustra tres puntos:</span><span class="sxs-lookup"><span data-stu-id="7742e-114">This illustrates three points:</span></span>  

- <span data-ttu-id="7742e-115">El nuevo blog ' mi nuevo blog ' está incluido en la colección local aunque aún no se ha guardado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7742e-115">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="7742e-116">Este blog tiene una clave principal de cero porque la base de datos aún no ha generado una clave real para la entidad.</span><span class="sxs-lookup"><span data-stu-id="7742e-116">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="7742e-117">El "blog de ADO.NET" no se incluye en la colección local aunque el contexto siga realizando el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="7742e-117">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="7742e-118">Esto se debe a que se ha quitado de DbSet y, por tanto, se ha marcado como eliminado.</span><span class="sxs-lookup"><span data-stu-id="7742e-118">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="7742e-119">Cuando se usa DbSet para realizar una consulta, el blog marcado para su eliminación (ADO.NET blog) se incluye en los resultados y el nuevo blog (mi nuevo blog) que todavía no se ha guardado en la base de datos no se incluye en los resultados.</span><span class="sxs-lookup"><span data-stu-id="7742e-119">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="7742e-120">Esto se debe a que DbSet está realizando una consulta en la base de datos y los resultados devueltos siempre reflejan lo que hay en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7742e-120">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="7742e-121">Usar local para agregar y quitar entidades del contexto</span><span class="sxs-lookup"><span data-stu-id="7742e-121">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="7742e-122">La propiedad local en DbSet devuelve un [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) con eventos enlazados de modo que permanece sincronizado con el contenido del contexto.</span><span class="sxs-lookup"><span data-stu-id="7742e-122">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="7742e-123">Esto significa que se pueden agregar o quitar entidades de la colección local o de DbSet.</span><span class="sxs-lookup"><span data-stu-id="7742e-123">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="7742e-124">También significa que las consultas que incorporan nuevas entidades al contexto darán lugar a que la colección local se actualice con esas entidades.</span><span class="sxs-lookup"><span data-stu-id="7742e-124">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="7742e-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7742e-125">For example:</span></span>  

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

<span data-ttu-id="7742e-126">Suponiendo que tenemos algunas entradas etiquetadas con "Entity-Framework" y "asp.net", la salida puede tener un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="7742e-126">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

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

<span data-ttu-id="7742e-127">Esto ilustra tres puntos:</span><span class="sxs-lookup"><span data-stu-id="7742e-127">This illustrates three points:</span></span>  

- <span data-ttu-id="7742e-128">La nueva publicación ' What's New in EF ' que se agregó a la colección local se hace seguimiento por el contexto en el estado Added.</span><span class="sxs-lookup"><span data-stu-id="7742e-128">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="7742e-129">Por lo tanto, se insertará en la base de datos cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="7742e-129">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="7742e-130">La publicación que se quitó de la colección local (guía para principiantes de EF) ahora está marcada como eliminada en el contexto.</span><span class="sxs-lookup"><span data-stu-id="7742e-130">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="7742e-131">Por lo tanto, se eliminará de la base de datos cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="7742e-131">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="7742e-132">La publicación adicional (guía para principiantes de ASP.NET) cargada en el contexto con la segunda consulta se agrega automáticamente a la colección local.</span><span class="sxs-lookup"><span data-stu-id="7742e-132">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="7742e-133">Una cuestión final que hay que tener en cuenta sobre local es que, dado que es un rendimiento de ObservableCollection no es excelente para un gran número de entidades.</span><span class="sxs-lookup"><span data-stu-id="7742e-133">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="7742e-134">Por lo tanto, si está tratando con miles de entidades en el contexto, puede que no sea aconsejable usar local.</span><span class="sxs-lookup"><span data-stu-id="7742e-134">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="7742e-135">Usar local para el enlace de datos de WPF</span><span class="sxs-lookup"><span data-stu-id="7742e-135">Using Local for WPF data binding</span></span>  

<span data-ttu-id="7742e-136">La propiedad local en DbSet se puede usar directamente para el enlace de datos en una aplicación WPF porque es una instancia de ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="7742e-136">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="7742e-137">Tal como se describe en las secciones anteriores, esto significa que permanecerá sincronizada automáticamente con el contenido del contexto y el contenido del contexto permanecerá sincronizado automáticamente con él.</span><span class="sxs-lookup"><span data-stu-id="7742e-137">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="7742e-138">Tenga en cuenta que debe rellenar previamente la colección local con datos para que sea todo lo que se va a enlazar a, ya que local nunca genera una consulta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7742e-138">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="7742e-139">No es un lugar adecuado para un ejemplo de enlace de datos de WPF completo, pero los elementos clave son:</span><span class="sxs-lookup"><span data-stu-id="7742e-139">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="7742e-140">Configuración de un origen de enlace</span><span class="sxs-lookup"><span data-stu-id="7742e-140">Setup a binding source</span></span>  
- <span data-ttu-id="7742e-141">Enlazarlo a la propiedad local del conjunto</span><span class="sxs-lookup"><span data-stu-id="7742e-141">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="7742e-142">Rellene el local mediante una consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7742e-142">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="7742e-143">Enlace de WPF a propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="7742e-143">WPF binding to navigation properties</span></span>  

<span data-ttu-id="7742e-144">Si está realizando el enlace de datos principal/detalle, puede enlazar la vista de detalle a una propiedad de navegación de una de las entidades.</span><span class="sxs-lookup"><span data-stu-id="7742e-144">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="7742e-145">Una manera fácil de realizar este trabajo es usar ObservableCollection para la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="7742e-145">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="7742e-146">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7742e-146">For example:</span></span>  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="7742e-147">Usar local para limpiar entidades en SaveChanges</span><span class="sxs-lookup"><span data-stu-id="7742e-147">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="7742e-148">En la mayoría de los casos, las entidades quitadas de una propiedad de navegación no se marcarán automáticamente como eliminadas en el contexto.</span><span class="sxs-lookup"><span data-stu-id="7742e-148">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="7742e-149">Por ejemplo, si quita un objeto post de la colección blog. posts, esa publicación no se eliminará automáticamente cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="7742e-149">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="7742e-150">Si necesita que se elimine, puede que necesite encontrar estas entidades pendientes y marcarlas como eliminadas antes de llamar a SaveChanges o como parte de un SaveChanges invalidado.</span><span class="sxs-lookup"><span data-stu-id="7742e-150">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="7742e-151">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7742e-151">For example:</span></span>  

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

<span data-ttu-id="7742e-152">El código anterior usa la colección local para buscar todas las publicaciones y marca cualquier que no tenga una referencia de blog como eliminada.</span><span class="sxs-lookup"><span data-stu-id="7742e-152">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="7742e-153">La llamada a ToList es necesaria porque, de lo contrario, se modificará la colección mediante la llamada a Remove mientras se está enumerando.</span><span class="sxs-lookup"><span data-stu-id="7742e-153">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="7742e-154">En la mayoría de las demás situaciones, puede realizar consultas directamente en la propiedad local sin usar ToList primero.</span><span class="sxs-lookup"><span data-stu-id="7742e-154">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="7742e-155">Usar local y ToBindingList para el enlace de datos Windows Forms</span><span class="sxs-lookup"><span data-stu-id="7742e-155">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="7742e-156">Windows Forms no admite el enlace de datos de plena fidelidad mediante ObservableCollection directamente.</span><span class="sxs-lookup"><span data-stu-id="7742e-156">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="7742e-157">Sin embargo, todavía puede usar la propiedad local DbSet para el enlace de datos con el fin de obtener todas las ventajas descritas en las secciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="7742e-157">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="7742e-158">Esto se logra mediante el método de extensión ToBindingList, que crea una implementación de [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) respaldada por el ObservableCollection local.</span><span class="sxs-lookup"><span data-stu-id="7742e-158">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="7742e-159">No es un lugar adecuado para un ejemplo de enlace de datos completo Windows Forms pero los elementos clave son:</span><span class="sxs-lookup"><span data-stu-id="7742e-159">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="7742e-160">Configuración de un origen de enlace de objeto</span><span class="sxs-lookup"><span data-stu-id="7742e-160">Setup an object binding source</span></span>  
- <span data-ttu-id="7742e-161">Enlácelo a la propiedad local del conjunto mediante local. ToBindingList ()</span><span class="sxs-lookup"><span data-stu-id="7742e-161">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="7742e-162">Rellenar local mediante una consulta a la base de datos</span><span class="sxs-lookup"><span data-stu-id="7742e-162">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="7742e-163">Obtención de información detallada sobre las entidades sometidas a seguimiento</span><span class="sxs-lookup"><span data-stu-id="7742e-163">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="7742e-164">Muchos de los ejemplos de esta serie usan el método entry para devolver una instancia de DbEntityEntry para una entidad.</span><span class="sxs-lookup"><span data-stu-id="7742e-164">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="7742e-165">Este objeto de entrada actúa como punto de partida para recopilar información sobre la entidad, como su estado actual, así como para realizar operaciones en la entidad, como cargar explícitamente una entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="7742e-165">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="7742e-166">Los métodos de entrada devuelven objetos DbEntityEntry para muchas o todas las entidades de las que el contexto realiza un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="7742e-166">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="7742e-167">Esto le permite recopilar información o realizar operaciones en muchas entidades en lugar de en una sola entrada.</span><span class="sxs-lookup"><span data-stu-id="7742e-167">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="7742e-168">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7742e-168">For example:</span></span>  

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

<span data-ttu-id="7742e-169">Observamos que estamos introduciendo una clase Author y Reader en el ejemplo. ambas clases implementan la interfaz IPerson.</span><span class="sxs-lookup"><span data-stu-id="7742e-169">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

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

<span data-ttu-id="7742e-170">Supongamos que tenemos los siguientes datos en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="7742e-170">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="7742e-171">Blog con BlogId = 1 y name = ' ADO.NET blog '</span><span class="sxs-lookup"><span data-stu-id="7742e-171">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="7742e-172">Blog con BlogId = 2 y name = "blog de Visual Studio"</span><span class="sxs-lookup"><span data-stu-id="7742e-172">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="7742e-173">Blog con BlogId = 3 y name = ' .NET Framework blog '</span><span class="sxs-lookup"><span data-stu-id="7742e-173">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="7742e-174">Autor con AuthorId = 1 y name = ' Joe Bloggs '</span><span class="sxs-lookup"><span data-stu-id="7742e-174">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="7742e-175">Lector con ReaderId = 1 y name = ' John Doe '</span><span class="sxs-lookup"><span data-stu-id="7742e-175">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="7742e-176">La salida de la ejecución del código sería:</span><span class="sxs-lookup"><span data-stu-id="7742e-176">The output from running the code would be:</span></span>  

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

<span data-ttu-id="7742e-177">En estos ejemplos se muestran varios puntos:</span><span class="sxs-lookup"><span data-stu-id="7742e-177">These examples illustrate several points:</span></span>  

- <span data-ttu-id="7742e-178">Los métodos de entrada devuelven entradas para entidades en todos los Estados, incluido Deleted.</span><span class="sxs-lookup"><span data-stu-id="7742e-178">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="7742e-179">Compare esto con el local, que excluye las entidades eliminadas.</span><span class="sxs-lookup"><span data-stu-id="7742e-179">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="7742e-180">Se devuelven las entradas de todos los tipos de entidad cuando se usa el método de entradas no genéricas.</span><span class="sxs-lookup"><span data-stu-id="7742e-180">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="7742e-181">Cuando se usa el método de entradas genéricas, las entradas solo se devuelven para las entidades que son instancias del tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="7742e-181">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="7742e-182">Se usó anteriormente para obtener entradas de todos los blogs.</span><span class="sxs-lookup"><span data-stu-id="7742e-182">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="7742e-183">También se usaba para obtener entradas para todas las entidades que implementan IPerson.</span><span class="sxs-lookup"><span data-stu-id="7742e-183">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="7742e-184">Esto demuestra que el tipo genérico no tiene que ser un tipo de entidad real.</span><span class="sxs-lookup"><span data-stu-id="7742e-184">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="7742e-185">LINQ to Objects se puede utilizar para filtrar los resultados devueltos.</span><span class="sxs-lookup"><span data-stu-id="7742e-185">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="7742e-186">Se usó anteriormente para buscar entidades de cualquier tipo, siempre y cuando se modifiquen.</span><span class="sxs-lookup"><span data-stu-id="7742e-186">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="7742e-187">Tenga en cuenta que las instancias de DbEntityEntry siempre contienen una entidad que no es NULL.</span><span class="sxs-lookup"><span data-stu-id="7742e-187">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="7742e-188">Las entradas de relación y las entradas de código auxiliar no se representan como instancias de DbEntityEntry, por lo que no es necesario filtrarlas.</span><span class="sxs-lookup"><span data-stu-id="7742e-188">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
