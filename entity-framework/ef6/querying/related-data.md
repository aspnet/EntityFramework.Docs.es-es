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
# <a name="loading-related-entities"></a><span data-ttu-id="c2374-103">Carga de entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="c2374-103">Loading Related Entities</span></span>

<span data-ttu-id="c2374-104">Entity Framework admite tres maneras de cargar datos relacionados: carga diligente, carga diferida y carga explícita.</span><span class="sxs-lookup"><span data-stu-id="c2374-104">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="c2374-105">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="c2374-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>

## <a name="eagerly-loading"></a><span data-ttu-id="c2374-106">Cargando diligentemente</span><span class="sxs-lookup"><span data-stu-id="c2374-106">Eagerly Loading</span></span>

<span data-ttu-id="c2374-107">La carga diligente es el proceso por el cual una consulta para un tipo de entidad también carga las entidades relacionadas como parte de la consulta.</span><span class="sxs-lookup"><span data-stu-id="c2374-107">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="c2374-108">La carga diligente se logra mediante el uso del método include.</span><span class="sxs-lookup"><span data-stu-id="c2374-108">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="c2374-109">Por ejemplo, las consultas siguientes cargarán blogs y todas las entradas relacionadas con cada blog.</span><span class="sxs-lookup"><span data-stu-id="c2374-109">For example, the queries below will load blogs and all the posts related to each blog.</span></span>

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
> <span data-ttu-id="c2374-110">Include es un método de extensión en el espacio de nombres System. Data. Entity, por lo que debe asegurarse de que está usando ese espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="c2374-110">Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="c2374-111">Carga de varios niveles diligentemente</span><span class="sxs-lookup"><span data-stu-id="c2374-111">Eagerly loading multiple levels</span></span>

<span data-ttu-id="c2374-112">También es posible cargar diligentemente varios niveles de entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="c2374-112">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="c2374-113">En las consultas siguientes se muestran ejemplos de cómo hacerlo para las propiedades de navegación de colección y de referencia.</span><span class="sxs-lookup"><span data-stu-id="c2374-113">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

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
> <span data-ttu-id="c2374-114">Actualmente no es posible filtrar las entidades relacionadas que se cargan.</span><span class="sxs-lookup"><span data-stu-id="c2374-114">It is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="c2374-115">Incluir siempre incluirá todas las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="c2374-115">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="c2374-116">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="c2374-116">Lazy Loading</span></span>

<span data-ttu-id="c2374-117">La carga diferida es el proceso por el que una entidad o colección de entidades se carga automáticamente desde la base de datos la primera vez que se tiene acceso a una propiedad que hace referencia a la entidad o entidades.</span><span class="sxs-lookup"><span data-stu-id="c2374-117">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="c2374-118">Al usar tipos de entidad POCO, la carga diferida se consigue creando instancias de tipos de proxy derivados y, a continuación, reemplazando las propiedades virtuales para agregar el enlace de carga.</span><span class="sxs-lookup"><span data-stu-id="c2374-118">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="c2374-119">Por ejemplo, al usar la clase de entidad de blog que se define a continuación, se cargarán las publicaciones relacionadas la primera vez que se tenga acceso a la propiedad de navegación posts:</span><span class="sxs-lookup"><span data-stu-id="c2374-119">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>

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

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="c2374-120">Desactivación de la carga diferida para la serialización</span><span class="sxs-lookup"><span data-stu-id="c2374-120">Turn lazy loading off for serialization</span></span>

<span data-ttu-id="c2374-121">La carga diferida y la serialización no se combinan bien y, si no tiene cuidado, puede finalizar la consulta de toda la base de datos, solo porque está habilitada la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="c2374-121">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="c2374-122">La mayoría de los serializadores funcionan mediante el acceso a cada propiedad en una instancia de un tipo.</span><span class="sxs-lookup"><span data-stu-id="c2374-122">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="c2374-123">El acceso de propiedad desencadena la carga diferida, por lo que se serializan más entidades.</span><span class="sxs-lookup"><span data-stu-id="c2374-123">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="c2374-124">En esas entidades se tiene acceso a las propiedades de, e incluso se cargan más entidades.</span><span class="sxs-lookup"><span data-stu-id="c2374-124">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="c2374-125">Se recomienda desactivar la carga diferida antes de serializar una entidad.</span><span class="sxs-lookup"><span data-stu-id="c2374-125">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="c2374-126">Se muestra cómo hacerlo en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="c2374-126">The following sections show how to do this.</span></span>

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="c2374-127">Desactivar la carga diferida para propiedades de navegación específicas</span><span class="sxs-lookup"><span data-stu-id="c2374-127">Turning off lazy loading for specific navigation properties</span></span>

<span data-ttu-id="c2374-128">La carga diferida de la colección de publicaciones se puede desactivar haciendo que la propiedad postes no sea virtual:</span><span class="sxs-lookup"><span data-stu-id="c2374-128">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>

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

<span data-ttu-id="c2374-129">La carga de la colección de publicaciones se puede seguir usando la carga diligente (consulte la *carga* rápida anterior) o el método Load (vea *carga explícita* a continuación).</span><span class="sxs-lookup"><span data-stu-id="c2374-129">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="c2374-130">Desactivar la carga diferida para todas las entidades</span><span class="sxs-lookup"><span data-stu-id="c2374-130">Turn off lazy loading for all entities</span></span>

<span data-ttu-id="c2374-131">La carga diferida se puede desactivar para todas las entidades en el contexto estableciendo una marca en la propiedad de configuración.</span><span class="sxs-lookup"><span data-stu-id="c2374-131">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="c2374-132">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2374-132">For example:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

<span data-ttu-id="c2374-133">La carga de entidades relacionadas todavía se puede lograr mediante la carga diligente (consulte la *carga* rápida anterior) o el método Load (vea *carga explícita* a continuación).</span><span class="sxs-lookup"><span data-stu-id="c2374-133">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

## <a name="explicitly-loading"></a><span data-ttu-id="c2374-134">Cargar explícitamente</span><span class="sxs-lookup"><span data-stu-id="c2374-134">Explicitly Loading</span></span>

<span data-ttu-id="c2374-135">Incluso con la carga diferida deshabilitada, todavía es posible cargar de forma diferida las entidades relacionadas, pero debe realizarse con una llamada explícita.</span><span class="sxs-lookup"><span data-stu-id="c2374-135">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="c2374-136">Para ello, use el método Load en la entrada de la entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="c2374-136">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="c2374-137">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2374-137">For example:</span></span>

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
> <span data-ttu-id="c2374-138">El método de referencia debe usarse cuando una entidad tiene una propiedad de navegación a otra entidad única.</span><span class="sxs-lookup"><span data-stu-id="c2374-138">The Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="c2374-139">Por otro lado, el método de colección debe usarse cuando una entidad tiene una propiedad de navegación a una colección de otras entidades.</span><span class="sxs-lookup"><span data-stu-id="c2374-139">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="c2374-140">Aplicar filtros al cargar explícitamente entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="c2374-140">Applying filters when explicitly loading related entities</span></span>

<span data-ttu-id="c2374-141">El método de consulta proporciona acceso a la consulta subyacente que utilizará Entity Framework al cargar las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="c2374-141">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="c2374-142">Después, puede usar LINQ para aplicar filtros a la consulta antes de ejecutarlo con una llamada a un método de extensión LINQ como ToList, Load, etc. El método de consulta se puede utilizar con propiedades de navegación de referencia y de colección, pero es muy útil para las colecciones en las que se puede usar para cargar solo parte de la colección.</span><span class="sxs-lookup"><span data-stu-id="c2374-142">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="c2374-143">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2374-143">For example:</span></span>

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

<span data-ttu-id="c2374-144">Cuando se usa el método de consulta, suele ser mejor desactivar la carga diferida para la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="c2374-144">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="c2374-145">Esto se debe a que, de lo contrario, es posible que el mecanismo de carga diferida cargue automáticamente toda la colección antes o después de que se haya ejecutado la consulta filtrada.</span><span class="sxs-lookup"><span data-stu-id="c2374-145">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>

> [!NOTE]
> <span data-ttu-id="c2374-146">Aunque la relación se puede especificar como una cadena en lugar de una expresión lambda, el IQueryable devuelto no es genérico cuando se usa una cadena y, por lo tanto, el método Cast suele ser necesario antes de que se pueda hacer nada útil con él.</span><span class="sxs-lookup"><span data-stu-id="c2374-146">While the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="c2374-147">Usar Query para contar entidades relacionadas sin cargarlas</span><span class="sxs-lookup"><span data-stu-id="c2374-147">Using Query to count related entities without loading them</span></span>

<span data-ttu-id="c2374-148">A veces resulta útil saber el número de entidades relacionadas con otra entidad de la base de datos sin incurrir realmente en el costo de cargar todas esas entidades.</span><span class="sxs-lookup"><span data-stu-id="c2374-148">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="c2374-149">Se puede utilizar el método de consulta con el método Count de LINQ para hacerlo.</span><span class="sxs-lookup"><span data-stu-id="c2374-149">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="c2374-150">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2374-150">For example:</span></span>

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
