---
title: 'Carga diferida de datos relacionados: EF Core'
description: Carga diferida de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/lazy
ms.openlocfilehash: 55622b9c5a8f70ef4e7246d6eb14678036948f18
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635463"
---
# <a name="lazy-loading-of-related-data"></a>Carga diferida de datos relacionados

## <a name="lazy-loading-with-proxies"></a>Carga diferida con servidores proxy

La manera más simple de usar la carga diferida es instalar el paquete [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) y habilitarlo con una llamada a `UseLazyLoadingProxies`. Por ejemplo:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

O al usar AddDbContext:

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

EF Core habilitará la carga diferida de cualquier propiedad de navegación que se pueda invalidar, es decir, debe ser `virtual` y debe estar en una clase desde la que se pueda heredar. Por ejemplo, en las entidades siguientes, las propiedades de navegación `Post.Blog` y `Blog.Posts` serán de carga diferida.

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

> [!WARNING]
> La carga diferida puede provocar recorridos de ida y vuelta a la base de datos adicionales e innecesarios (el problema conocido como N+1), y conviene tener cuidado para evitar esto. Consulte la [sección sobre el rendimiento](xref:core/performance/efficient-querying#beware-of-lazy-loading) para obtener más información.

## <a name="lazy-loading-without-proxies"></a>Carga diferida sin servidores proxy

Los servidores proxy de carga diferida funcionan inyectando el servicio `ILazyLoader` en una entidad, tal como se describe en [Entity Type Constructors](xref:core/modeling/constructors) (Constructores de tipo de entidad). Por ejemplo:

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

Este método no requiere tipos de entidad de los cuales heredar ni propiedades de navegación para ser virtual, y permite que las instancias de entidad creadas con `new` se carguen de manera diferida una vez que se asocian a un contexto. Sin embargo, requiere una referencia al servicio `ILazyLoader`, que está definido en el paquete [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/). Este paquete contiene un conjunto mínimo de tipos, por lo que el impacto al depender de él es poco significativo. Sin embargo, para evitar depender por completo de ningún paquete de EF Core en los tipos de entidad, es posible insertar el método `ILazyLoader.Load` como delegado. Por ejemplo:

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

El código anterior usa un método de extensión `Load` para que el uso del delegado sea un poco más limpio:

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
> El parámetro de constructor del delegado de carga diferida se debe denominar "lazyLoader". La configuración para usar otro nombre está planificada para una versión futura.
