---
title: 'Carga diferida de datos relacionados: EF Core'
description: Carga diferida de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/lazy
ms.openlocfilehash: c42cde469e2be38d53a46cb6c5c252a088978e5c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078892"
---
# <a name="lazy-loading-of-related-data"></a><span data-ttu-id="3365f-103">Carga diferida de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="3365f-103">Lazy Loading of Related Data</span></span>

## <a name="lazy-loading-with-proxies"></a><span data-ttu-id="3365f-104">Carga diferida con servidores proxy</span><span class="sxs-lookup"><span data-stu-id="3365f-104">Lazy loading with proxies</span></span>

<span data-ttu-id="3365f-105">La manera más simple de usar la carga diferida es instalar el paquete [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) y habilitarlo con una llamada a `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="3365f-105">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="3365f-106">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3365f-106">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="3365f-107">O al usar AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="3365f-107">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="3365f-108">EF Core habilitará la carga diferida de cualquier propiedad de navegación que se pueda invalidar, es decir, debe ser `virtual` y debe estar en una clase desde la que se pueda heredar.</span><span class="sxs-lookup"><span data-stu-id="3365f-108">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="3365f-109">Por ejemplo, en las entidades siguientes, las propiedades de navegación `Post.Blog` y `Blog.Posts` serán de carga diferida.</span><span class="sxs-lookup"><span data-stu-id="3365f-109">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

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

## <a name="lazy-loading-without-proxies"></a><span data-ttu-id="3365f-110">Carga diferida sin servidores proxy</span><span class="sxs-lookup"><span data-stu-id="3365f-110">Lazy loading without proxies</span></span>

<span data-ttu-id="3365f-111">Los servidores proxy de carga diferida funcionan inyectando el servicio `ILazyLoader` en una entidad, tal como se describe en [Entity Type Constructors](xref:core/modeling/constructors) (Constructores de tipo de entidad).</span><span class="sxs-lookup"><span data-stu-id="3365f-111">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="3365f-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3365f-112">For example:</span></span>

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

<span data-ttu-id="3365f-113">Este método no requiere tipos de entidad de los cuales heredar ni propiedades de navegación para ser virtual, y permite que las instancias de entidad creadas con `new` se carguen de manera diferida una vez que se asocian a un contexto.</span><span class="sxs-lookup"><span data-stu-id="3365f-113">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="3365f-114">Sin embargo, requiere una referencia al servicio `ILazyLoader`, que está definido en el paquete [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="3365f-114">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="3365f-115">Este paquete contiene un conjunto mínimo de tipos, por lo que el impacto al depender de él es poco significativo.</span><span class="sxs-lookup"><span data-stu-id="3365f-115">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="3365f-116">Sin embargo, para evitar depender por completo de ningún paquete de EF Core en los tipos de entidad, es posible insertar el método `ILazyLoader.Load` como delegado.</span><span class="sxs-lookup"><span data-stu-id="3365f-116">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="3365f-117">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3365f-117">For example:</span></span>

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

<span data-ttu-id="3365f-118">El código anterior usa un método de extensión `Load` para que el uso del delegado sea un poco más limpio:</span><span class="sxs-lookup"><span data-stu-id="3365f-118">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

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
> <span data-ttu-id="3365f-119">El parámetro de constructor del delegado de carga diferida se debe denominar "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="3365f-119">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="3365f-120">La configuración para usar otro nombre está planificada para una versión futura.</span><span class="sxs-lookup"><span data-stu-id="3365f-120">Configuration to use a different name than this is planned for a future release.</span></span>