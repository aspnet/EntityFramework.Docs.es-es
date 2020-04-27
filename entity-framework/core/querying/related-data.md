---
title: 'Carga de datos relacionados: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: bfd6e161ed7f7bf96e61946f94c8eeadd24a72f5
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434193"
---
# <a name="loading-related-data"></a><span data-ttu-id="eb3ac-102">Carga de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="eb3ac-102">Loading Related Data</span></span>

<span data-ttu-id="eb3ac-103">Entity Framework Core permite usar las propiedades de navegación del modelo para cargar las entidades relacionados.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="eb3ac-104">Existen tres patrones de O/RM comunes que se usan para cargar los datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="eb3ac-105">**Carga diligente** significa que los datos relacionados se cargan desde la base de datos como parte de la consulta inicial.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="eb3ac-106">**Carga explícita** significa que los datos relacionados se cargan de manera explícita desde la base de datos más adelante.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="eb3ac-107">**Carga diferida** significa que los datos relacionados se cargan de manera transparente desde la base de datos cuando se accede a la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="eb3ac-108">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="eb3ac-109">Carga diligente</span><span class="sxs-lookup"><span data-stu-id="eb3ac-109">Eager loading</span></span>

<span data-ttu-id="eb3ac-110">Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="eb3ac-111">En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="eb3ac-112">Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="eb3ac-113">Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="eb3ac-114">Puede incluir los datos relacionados de varias relaciones en una sola consulta.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="eb3ac-115">Inclusión de varios niveles</span><span class="sxs-lookup"><span data-stu-id="eb3ac-115">Including multiple levels</span></span>

<span data-ttu-id="eb3ac-116">Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="eb3ac-117">En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="eb3ac-118">Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="eb3ac-119">Puede combinar todo esto para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="eb3ac-120">Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="eb3ac-121">Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="eb3ac-122">Para hacerlo, debe especificar cada inicio de ruta de acceso de inclusión en la raíz.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="eb3ac-123">Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="eb3ac-124">Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF consolidará las combinaciones al generar código SQL.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> <span data-ttu-id="eb3ac-125">Desde la versión 3.0.0, todas las instancias de `Include` producirán que se agregue una combinación JOIN adicional a las consultas SQL generadas por los proveedores relacionales, mientras que las versiones anteriores generaban consultas SQL adicionales.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-125">Since version 3.0.0, each `Include` will cause an additional JOIN to be added to SQL queries produced by relational providers, whereas previous versions generated additional SQL queries.</span></span> <span data-ttu-id="eb3ac-126">Esto puede cambiar significativamente el rendimiento de las consultas, tanto para bien como para mal.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-126">This can significantly change the performance of your queries, for better or worse.</span></span> <span data-ttu-id="eb3ac-127">En concreto, es posible que las consultas LINQ con un número excesivamente alto de operadores `Include` deban dividirse en varias consultas LINQ independientes con el fin de evitar el problema de explosión cartesiana.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-127">In particular, LINQ queries with an exceedingly high number of `Include` operators may need to be broken down into multiple separate LINQ queries in order to avoid the cartesian explosion problem.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="eb3ac-128">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="eb3ac-128">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="eb3ac-129">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-129">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="eb3ac-130">Al aplicar Include para cargar datos relacionados, puede aplicar determinadas operaciones enumerables en la navegación de colección incluida, lo que permite filtrar y ordenar los resultados.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-130">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="eb3ac-131">Las operaciones que se admiten son: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` y `Take`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-131">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="eb3ac-132">Dichas operaciones se deben aplicar en la navegación de colección en la expresión lambda que se pasa al método Include, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-132">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="eb3ac-133">Cada navegación incluida solo permite un único conjunto de operaciones de filtro.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-133">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="eb3ac-134">En los casos en los que se aplican varias operaciones Include para una navegación de colección determinada (`blog.Posts` en los ejemplos siguientes), las operaciones de filtro solo se pueden especificar en una de ellas:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-134">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="eb3ac-135">De manera alternativa, se pueden aplicar operaciones idénticas para cada navegación que esté incluida varias veces:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-135">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

### <a name="include-on-derived-types"></a><span data-ttu-id="eb3ac-136">Inclusión en tipos derivados</span><span class="sxs-lookup"><span data-stu-id="eb3ac-136">Include on derived types</span></span>

<span data-ttu-id="eb3ac-137">Puede incluir datos relacionados provenientes de las navegaciones que se definen solo en un tipo derivado con `Include` y `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-137">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="eb3ac-138">Dado el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-138">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="eb3ac-139">El contenido de la navegación `School` de todas las personas que son estudiantes se puede cargar de manera diligente mediante el uso de diversos patrones:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-139">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="eb3ac-140">con conversión</span><span class="sxs-lookup"><span data-stu-id="eb3ac-140">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="eb3ac-141">con el operador `as`</span><span class="sxs-lookup"><span data-stu-id="eb3ac-141">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="eb3ac-142">con la sobrecarga de `Include` que toma el parámetro del tipo `string`</span><span class="sxs-lookup"><span data-stu-id="eb3ac-142">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="eb3ac-143">Carga explícita</span><span class="sxs-lookup"><span data-stu-id="eb3ac-143">Explicit loading</span></span>

<span data-ttu-id="eb3ac-144">Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-144">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="eb3ac-145">También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-145">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="eb3ac-146">Si está habilitado el seguimiento de cambios, cuando se cargue una entidad, EF Core establecerá automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada y establecerá las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recién cargada.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-146">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="eb3ac-147">Consulta de las entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="eb3ac-147">Querying related entities</span></span>

<span data-ttu-id="eb3ac-148">También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-148">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="eb3ac-149">Esto permite, entre otras acciones, ejecutar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-149">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="eb3ac-150">También puede filtrar las entidades relacionadas que se cargan en la memoria.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-150">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="eb3ac-151">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="eb3ac-151">Lazy loading</span></span>

<span data-ttu-id="eb3ac-152">La manera más simple de usar la carga diferida es instalar el paquete [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) y habilitarlo con una llamada a `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-152">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="eb3ac-153">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-153">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="eb3ac-154">O al usar AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-154">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="eb3ac-155">EF Core habilitará la carga diferida de cualquier propiedad de navegación que se pueda invalidar, es decir, debe ser `virtual` y debe estar en una clase desde la que se pueda heredar.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-155">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="eb3ac-156">Por ejemplo, en las entidades siguientes, las propiedades de navegación `Post.Blog` y `Blog.Posts` serán de carga diferida.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-156">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="eb3ac-157">Carga diferida sin servidores proxy</span><span class="sxs-lookup"><span data-stu-id="eb3ac-157">Lazy loading without proxies</span></span>

<span data-ttu-id="eb3ac-158">Los servidores proxy de carga diferida funcionan inyectando el servicio `ILazyLoader` en una entidad, tal como se describe en [Entity Type Constructors](../modeling/constructors.md) (Constructores de tipo de entidad).</span><span class="sxs-lookup"><span data-stu-id="eb3ac-158">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="eb3ac-159">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-159">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="eb3ac-160">Esto no requiere tipos de entidad de los cuales heredar ni propiedades de navegación para ser virtual y permite que las instancias de entidad creadas con `new` se carguen de manera diferida una vez que se asocian a un contexto.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-160">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="eb3ac-161">Sin embargo, requiere una referencia al servicio `ILazyLoader`, que está definido en el paquete [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="eb3ac-161">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="eb3ac-162">Este paquete contiene un conjunto mínimo de tipos, por lo que es muy poco el impacto al depender de él.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-162">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="eb3ac-163">Sin embargo, para evitar por completo depender de cualquier paquete de EF Core en los tipos de entidad, es posible insertar el método `ILazyLoader.Load` como delegado.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-163">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="eb3ac-164">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-164">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="eb3ac-165">El código anterior usa un método de extensión `Load` para que el uso del delegado sea un poco más limpio:</span><span class="sxs-lookup"><span data-stu-id="eb3ac-165">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="eb3ac-166">El parámetro de constructor del delegado de carga diferida se debe denominar "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="eb3ac-166">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="eb3ac-167">La configuración para usar otro nombre está planificada para una versión futura.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-167">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="eb3ac-168">Datos relacionados y serialización</span><span class="sxs-lookup"><span data-stu-id="eb3ac-168">Related data and serialization</span></span>

<span data-ttu-id="eb3ac-169">Como EF Core corregirá automáticamente las propiedades de navegación, puede terminar con ciclos en el grafo de objetos.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-169">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="eb3ac-170">Por ejemplo, cargar un blog y sus entradas relacionadas dará lugar a un objeto de blog que hará referencia a una colección de entradas.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-170">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="eb3ac-171">Cada una de esas entradas tendrá una referencia de vuelta al blog.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-171">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="eb3ac-172">Algunos marcos de serialización no permiten ese tipo de ciclos.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-172">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="eb3ac-173">Por ejemplo, JSON.NET generará la excepción siguiente si se encuentra un ciclo.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-173">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="eb3ac-174">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: se detectó un bucle con autorreferencia para la propiedad "Blog" con el tipo "MyApplication.Models.Blog").</span><span class="sxs-lookup"><span data-stu-id="eb3ac-174">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="eb3ac-175">Si usa ASP.NET Core, puede configurar JSON.NET para que omita los ciclos que encuentre en el grafo del objeto.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-175">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="eb3ac-176">Esto se hace en el método `ConfigureServices(...)` en `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-176">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="eb3ac-177">Otra alternativa consiste en decorar una de las propiedades de navegación con el atributo `[JsonIgnore]`, que indica a JSON.NET que no recorra esa propiedad de navegación mientras se serializa.</span><span class="sxs-lookup"><span data-stu-id="eb3ac-177">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
