---
title: 'Tipos de entidad con constructores: EF Core'
description: Usar constructores para enlazar datos con Entity Framework Core modelo
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 9502d75072eebb80c37cf1805e21f7d112269ba1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063717"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="84b6b-103">Tipos de entidad con constructores</span><span class="sxs-lookup"><span data-stu-id="84b6b-103">Entity types with constructors</span></span>

<span data-ttu-id="84b6b-104">Es posible definir un constructor con parámetros y hacer que EF Core llame a este constructor al crear una instancia de la entidad.</span><span class="sxs-lookup"><span data-stu-id="84b6b-104">It's possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="84b6b-105">Los parámetros de constructor se pueden enlazar a propiedades asignadas o a varios tipos de servicios para facilitar comportamientos como la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="84b6b-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]
> <span data-ttu-id="84b6b-106">Actualmente, todos los enlaces de constructor son por Convención.</span><span class="sxs-lookup"><span data-stu-id="84b6b-106">Currently, all constructor binding is by convention.</span></span> <span data-ttu-id="84b6b-107">La configuración de constructores específicos que se va a usar está planeada para una versión futura.</span><span class="sxs-lookup"><span data-stu-id="84b6b-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="84b6b-108">Enlazar a propiedades asignadas</span><span class="sxs-lookup"><span data-stu-id="84b6b-108">Binding to mapped properties</span></span>

<span data-ttu-id="84b6b-109">Considere un modelo típico de blog o post:</span><span class="sxs-lookup"><span data-stu-id="84b6b-109">Consider a typical Blog/Post model:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="84b6b-110">Cuando EF Core crea instancias de estos tipos, como los resultados de una consulta, primero llamará al constructor sin parámetros predeterminado y, a continuación, establecerá cada propiedad en el valor de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="84b6b-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="84b6b-111">Sin embargo, si EF Core encuentra un constructor con parámetros con nombres de parámetros y tipos que coinciden con los de propiedades asignadas, se llamará en su lugar al constructor con parámetros con valores para esas propiedades y no establecerá cada propiedad explícitamente.</span><span class="sxs-lookup"><span data-stu-id="84b6b-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="84b6b-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="84b6b-112">For example:</span></span>

```csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="84b6b-113">Cosas que tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="84b6b-113">Some things to note:</span></span>

* <span data-ttu-id="84b6b-114">No todas las propiedades deben tener parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="84b6b-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="84b6b-115">Por ejemplo, la propiedad post. Content no está establecida por ningún parámetro de constructor, por lo que EF Core la establecerá después de llamar al constructor de la manera normal.</span><span class="sxs-lookup"><span data-stu-id="84b6b-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="84b6b-116">Los nombres y tipos de parámetros deben coincidir con los nombres y tipos de propiedad, con la excepción de que las propiedades pueden tener mayúsculas y minúsculas Pascal, mientras que los parámetros tienen mayúsculas y minúsculas Camel.</span><span class="sxs-lookup"><span data-stu-id="84b6b-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="84b6b-117">EF Core no pueden establecer las propiedades de navegación (como blog o publicaciones anteriores) mediante un constructor.</span><span class="sxs-lookup"><span data-stu-id="84b6b-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="84b6b-118">El constructor puede ser público, privado o tener cualquier otra accesibilidad.</span><span class="sxs-lookup"><span data-stu-id="84b6b-118">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="84b6b-119">Sin embargo, los proxies de carga diferida requieren que el constructor sea accesible desde la clase de proxy heredada.</span><span class="sxs-lookup"><span data-stu-id="84b6b-119">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="84b6b-120">Normalmente esto significa que se hace público o protegido.</span><span class="sxs-lookup"><span data-stu-id="84b6b-120">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="84b6b-121">Propiedades de solo lectura</span><span class="sxs-lookup"><span data-stu-id="84b6b-121">Read-only properties</span></span>

<span data-ttu-id="84b6b-122">Una vez que las propiedades se establecen mediante el constructor, puede tener sentido que algunas de ellas sean de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="84b6b-122">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="84b6b-123">EF Core es compatible con esto, pero hay algunas cosas que debe buscar:</span><span class="sxs-lookup"><span data-stu-id="84b6b-123">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="84b6b-124">Las propiedades sin establecedores no se asignan por Convención.</span><span class="sxs-lookup"><span data-stu-id="84b6b-124">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="84b6b-125">(Esto tiende a asignar propiedades que no deben asignarse, como las propiedades calculadas).</span><span class="sxs-lookup"><span data-stu-id="84b6b-125">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="84b6b-126">El uso de valores de clave generados automáticamente requiere una propiedad de clave que sea de lectura y escritura, ya que el valor de clave debe establecerse mediante el generador de claves al insertar nuevas entidades.</span><span class="sxs-lookup"><span data-stu-id="84b6b-126">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="84b6b-127">Una manera sencilla de evitar estos aspectos es usar establecedores privados.</span><span class="sxs-lookup"><span data-stu-id="84b6b-127">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="84b6b-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="84b6b-128">For example:</span></span>

```csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="84b6b-129">EF Core ve una propiedad con un establecedor privado como de lectura y escritura, lo que significa que todas las propiedades se asignan como antes y la clave todavía se puede generar en el almacén.</span><span class="sxs-lookup"><span data-stu-id="84b6b-129">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="84b6b-130">Una alternativa al uso de establecedores privados es hacer que las propiedades sean realmente de solo lectura y agregar una asignación más explícita en OnModelCreating.</span><span class="sxs-lookup"><span data-stu-id="84b6b-130">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="84b6b-131">Del mismo modo, algunas propiedades se pueden quitar por completo y reemplazar solo por campos.</span><span class="sxs-lookup"><span data-stu-id="84b6b-131">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="84b6b-132">Por ejemplo, considere estos tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="84b6b-132">For example, consider these entity types:</span></span>

```csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="84b6b-133">Y esta configuración en OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="84b6b-133">And this configuration in OnModelCreating:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

<span data-ttu-id="84b6b-134">Cosas que hay que tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="84b6b-134">Things to note:</span></span>

* <span data-ttu-id="84b6b-135">La clave "propiedad" es ahora un campo.</span><span class="sxs-lookup"><span data-stu-id="84b6b-135">The key "property" is now a field.</span></span> <span data-ttu-id="84b6b-136">No es un `readonly` campo para que se puedan usar claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="84b6b-136">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="84b6b-137">Las demás propiedades son propiedades de solo lectura establecidas solo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="84b6b-137">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="84b6b-138">Si el valor de la clave principal solo se establece en EF o se lee de la base de datos, no es necesario incluirlo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="84b6b-138">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="84b6b-139">Esto deja la clave "Property" como un campo simple y deja claro que no se debe establecer explícitamente al crear nuevos blogs o publicaciones.</span><span class="sxs-lookup"><span data-stu-id="84b6b-139">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]
> <span data-ttu-id="84b6b-140">Este código producirá una advertencia del compilador ' 169 ' que indica que el campo nunca se utiliza.</span><span class="sxs-lookup"><span data-stu-id="84b6b-140">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="84b6b-141">Esto puede pasarse por alto, ya que en realidad EF Core está utilizando el campo de forma extralingüística.</span><span class="sxs-lookup"><span data-stu-id="84b6b-141">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="84b6b-142">Insertar servicios</span><span class="sxs-lookup"><span data-stu-id="84b6b-142">Injecting services</span></span>

<span data-ttu-id="84b6b-143">EF Core también puede insertar "servicios" en el constructor de un tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="84b6b-143">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="84b6b-144">Por ejemplo, se puede insertar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="84b6b-144">For example, the following can be injected:</span></span>

* <span data-ttu-id="84b6b-145">`DbContext` -la instancia de contexto actual, que también se puede escribir como el tipo de DbContext derivado.</span><span class="sxs-lookup"><span data-stu-id="84b6b-145">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="84b6b-146">`ILazyLoader` -el servicio de carga diferida; consulte la [documentación sobre la carga diferida](xref:core/querying/related-data) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="84b6b-146">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="84b6b-147">`Action<object, string>` -un delegado de carga diferida; consulte la [documentación de carga diferida](xref:core/querying/related-data) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="84b6b-147">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="84b6b-148">`IEntityType` -los metadatos de EF Core asociados a este tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="84b6b-148">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]
> <span data-ttu-id="84b6b-149">Actualmente, solo se pueden insertar los servicios conocidos por EF Core.</span><span class="sxs-lookup"><span data-stu-id="84b6b-149">Currently, only services known by EF Core can be injected.</span></span> <span data-ttu-id="84b6b-150">Se está considerando la compatibilidad con la inserción de servicios de aplicación en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="84b6b-150">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="84b6b-151">Por ejemplo, un DbContext insertado se puede usar para tener acceso de forma selectiva a la base de datos para obtener información sobre las entidades relacionadas sin cargarlas todas.</span><span class="sxs-lookup"><span data-stu-id="84b6b-151">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="84b6b-152">En el ejemplo siguiente, se usa para obtener el número de publicaciones en un blog sin cargar las entradas:</span><span class="sxs-lookup"><span data-stu-id="84b6b-152">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

```csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="84b6b-153">Algunos aspectos que se deben tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="84b6b-153">A few things to notice about this:</span></span>

* <span data-ttu-id="84b6b-154">El constructor es privado, ya que nunca lo llama EF Core, y hay otro constructor público para uso general.</span><span class="sxs-lookup"><span data-stu-id="84b6b-154">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="84b6b-155">El código que usa el servicio inyectado (es decir, el contexto) está defensivo con el `null` fin de controlar los casos en los que EF Core no está creando la instancia.</span><span class="sxs-lookup"><span data-stu-id="84b6b-155">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="84b6b-156">Dado que el servicio se almacena en una propiedad de lectura y escritura, se restablecerá cuando la entidad se adjunte a una nueva instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="84b6b-156">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]
> <span data-ttu-id="84b6b-157">Inyectar DbContext como esto se suele considerar un anti-patrón, ya que une los tipos de entidad directamente a EF Core.</span><span class="sxs-lookup"><span data-stu-id="84b6b-157">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="84b6b-158">Tenga en cuenta todas las opciones antes de usar la inserción de servicios como esta.</span><span class="sxs-lookup"><span data-stu-id="84b6b-158">Carefully consider all options before using service injection like this.</span></span>
