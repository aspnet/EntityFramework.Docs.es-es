---
title: 'Tipos de entidad con constructores: EF Core'
description: Usar constructores para enlazar datos con Entity Framework Core modelo
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 06d18f173275599ad1e547193363e13c48fc8dcf
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071594"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="a834e-103">Tipos de entidad con constructores</span><span class="sxs-lookup"><span data-stu-id="a834e-103">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="a834e-104">Esta característica es nueva en EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="a834e-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="a834e-105">A partir de EF Core 2,1, ahora es posible definir un constructor con parámetros y EF Core llamar a este constructor al crear una instancia de la entidad.</span><span class="sxs-lookup"><span data-stu-id="a834e-105">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="a834e-106">Los parámetros de constructor se pueden enlazar a propiedades asignadas o a varios tipos de servicios para facilitar comportamientos como la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="a834e-106">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="a834e-107">A partir de EF Core 2,1, todos los enlaces de constructor son por Convención.</span><span class="sxs-lookup"><span data-stu-id="a834e-107">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="a834e-108">La configuración de constructores específicos que se va a usar está planeada para una versión futura.</span><span class="sxs-lookup"><span data-stu-id="a834e-108">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="a834e-109">Enlazar a propiedades asignadas</span><span class="sxs-lookup"><span data-stu-id="a834e-109">Binding to mapped properties</span></span>

<span data-ttu-id="a834e-110">Considere un modelo típico de blog o post:</span><span class="sxs-lookup"><span data-stu-id="a834e-110">Consider a typical Blog/Post model:</span></span>

``` csharp
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

<span data-ttu-id="a834e-111">Cuando EF Core crea instancias de estos tipos, como los resultados de una consulta, primero llamará al constructor sin parámetros predeterminado y, a continuación, establecerá cada propiedad en el valor de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a834e-111">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="a834e-112">Sin embargo, si EF Core encuentra un constructor con parámetros con nombres de parámetros y tipos que coinciden con los de propiedades asignadas, se llamará en su lugar al constructor con parámetros con valores para esas propiedades y no establecerá cada propiedad explícitamente.</span><span class="sxs-lookup"><span data-stu-id="a834e-112">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="a834e-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a834e-113">For example:</span></span>

``` csharp
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

<span data-ttu-id="a834e-114">Cosas que tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="a834e-114">Some things to note:</span></span>

* <span data-ttu-id="a834e-115">No todas las propiedades deben tener parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="a834e-115">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="a834e-116">Por ejemplo, la propiedad post. Content no está establecida por ningún parámetro de constructor, por lo que EF Core la establecerá después de llamar al constructor de la manera normal.</span><span class="sxs-lookup"><span data-stu-id="a834e-116">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="a834e-117">Los nombres y tipos de parámetros deben coincidir con los nombres y tipos de propiedad, con la excepción de que las propiedades pueden tener mayúsculas y minúsculas Pascal, mientras que los parámetros tienen mayúsculas y minúsculas Camel.</span><span class="sxs-lookup"><span data-stu-id="a834e-117">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="a834e-118">EF Core no pueden establecer las propiedades de navegación (como blog o publicaciones anteriores) mediante un constructor.</span><span class="sxs-lookup"><span data-stu-id="a834e-118">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="a834e-119">El constructor puede ser público, privado o tener cualquier otra accesibilidad.</span><span class="sxs-lookup"><span data-stu-id="a834e-119">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="a834e-120">Sin embargo, los proxies de carga diferida requieren que el constructor sea accesible desde la clase de proxy heredada.</span><span class="sxs-lookup"><span data-stu-id="a834e-120">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="a834e-121">Normalmente esto significa que se hace público o protegido.</span><span class="sxs-lookup"><span data-stu-id="a834e-121">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="a834e-122">Propiedades de solo lectura</span><span class="sxs-lookup"><span data-stu-id="a834e-122">Read-only properties</span></span>

<span data-ttu-id="a834e-123">Una vez que las propiedades se establecen mediante el constructor, puede tener sentido que algunas de ellas sean de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="a834e-123">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="a834e-124">EF Core es compatible con esto, pero hay algunas cosas que debe buscar:</span><span class="sxs-lookup"><span data-stu-id="a834e-124">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="a834e-125">Las propiedades sin establecedores no se asignan por Convención.</span><span class="sxs-lookup"><span data-stu-id="a834e-125">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="a834e-126">(Esto tiende a asignar propiedades que no deben asignarse, como las propiedades calculadas).</span><span class="sxs-lookup"><span data-stu-id="a834e-126">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="a834e-127">El uso de valores de clave generados automáticamente requiere una propiedad de clave que sea de lectura y escritura, ya que el valor de clave debe establecerse mediante el generador de claves al insertar nuevas entidades.</span><span class="sxs-lookup"><span data-stu-id="a834e-127">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="a834e-128">Una manera sencilla de evitar estos aspectos es usar establecedores privados.</span><span class="sxs-lookup"><span data-stu-id="a834e-128">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="a834e-129">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a834e-129">For example:</span></span>

``` csharp
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

<span data-ttu-id="a834e-130">EF Core ve una propiedad con un establecedor privado como de lectura y escritura, lo que significa que todas las propiedades se asignan como antes y la clave todavía se puede generar en el almacén.</span><span class="sxs-lookup"><span data-stu-id="a834e-130">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="a834e-131">Una alternativa al uso de establecedores privados es hacer que las propiedades sean realmente de solo lectura y agregar una asignación más explícita en OnModelCreating.</span><span class="sxs-lookup"><span data-stu-id="a834e-131">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="a834e-132">Del mismo modo, algunas propiedades se pueden quitar por completo y reemplazar solo por campos.</span><span class="sxs-lookup"><span data-stu-id="a834e-132">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="a834e-133">Por ejemplo, considere estos tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="a834e-133">For example, consider these entity types:</span></span>

``` csharp
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

<span data-ttu-id="a834e-134">Y esta configuración en OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="a834e-134">And this configuration in OnModelCreating:</span></span>

``` csharp
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

<span data-ttu-id="a834e-135">Cosas que hay que tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="a834e-135">Things to note:</span></span>

* <span data-ttu-id="a834e-136">La clave "propiedad" es ahora un campo.</span><span class="sxs-lookup"><span data-stu-id="a834e-136">The key "property" is now a field.</span></span> <span data-ttu-id="a834e-137">No es un `readonly` campo para que se puedan usar claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="a834e-137">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="a834e-138">Las demás propiedades son propiedades de solo lectura establecidas solo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="a834e-138">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="a834e-139">Si el valor de la clave principal solo se establece en EF o se lee de la base de datos, no es necesario incluirlo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="a834e-139">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="a834e-140">Esto deja la clave "Property" como un campo simple y deja claro que no se debe establecer explícitamente al crear nuevos blogs o publicaciones.</span><span class="sxs-lookup"><span data-stu-id="a834e-140">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="a834e-141">Este código producirá una advertencia del compilador ' 169 ' que indica que el campo nunca se utiliza.</span><span class="sxs-lookup"><span data-stu-id="a834e-141">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="a834e-142">Esto puede pasarse por alto, ya que en realidad EF Core está utilizando el campo de forma extralingüística.</span><span class="sxs-lookup"><span data-stu-id="a834e-142">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="a834e-143">Insertar servicios</span><span class="sxs-lookup"><span data-stu-id="a834e-143">Injecting services</span></span>

<span data-ttu-id="a834e-144">EF Core también puede insertar "servicios" en el constructor de un tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="a834e-144">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="a834e-145">Por ejemplo, se puede insertar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a834e-145">For example, the following can be injected:</span></span>

* <span data-ttu-id="a834e-146">`DbContext` -la instancia de contexto actual, que también se puede escribir como el tipo de DbContext derivado.</span><span class="sxs-lookup"><span data-stu-id="a834e-146">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="a834e-147">`ILazyLoader` -el servicio de carga diferida; consulte la [documentación sobre la carga diferida](xref:core/querying/related-data) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="a834e-147">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="a834e-148">`Action<object, string>` -un delegado de carga diferida; consulte la [documentación de carga diferida](xref:core/querying/related-data) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="a834e-148">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="a834e-149">`IEntityType` -los metadatos de EF Core asociados a este tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="a834e-149">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="a834e-150">A partir de EF Core 2,1, solo se pueden insertar los servicios conocidos por EF Core.</span><span class="sxs-lookup"><span data-stu-id="a834e-150">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="a834e-151">Se está considerando la compatibilidad con la inserción de servicios de aplicación en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="a834e-151">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="a834e-152">Por ejemplo, un DbContext insertado se puede usar para tener acceso de forma selectiva a la base de datos para obtener información sobre las entidades relacionadas sin cargarlas todas.</span><span class="sxs-lookup"><span data-stu-id="a834e-152">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="a834e-153">En el ejemplo siguiente, se usa para obtener el número de publicaciones en un blog sin cargar las entradas:</span><span class="sxs-lookup"><span data-stu-id="a834e-153">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

``` csharp
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

<span data-ttu-id="a834e-154">Algunos aspectos que se deben tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="a834e-154">A few things to notice about this:</span></span>

* <span data-ttu-id="a834e-155">El constructor es privado, ya que nunca lo llama EF Core, y hay otro constructor público para uso general.</span><span class="sxs-lookup"><span data-stu-id="a834e-155">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="a834e-156">El código que usa el servicio inyectado (es decir, el contexto) está defensivo con el `null` fin de controlar los casos en los que EF Core no está creando la instancia.</span><span class="sxs-lookup"><span data-stu-id="a834e-156">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="a834e-157">Dado que el servicio se almacena en una propiedad de lectura y escritura, se restablecerá cuando la entidad se adjunte a una nueva instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="a834e-157">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="a834e-158">Inyectar DbContext como esto se suele considerar un anti-patrón, ya que une los tipos de entidad directamente a EF Core.</span><span class="sxs-lookup"><span data-stu-id="a834e-158">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="a834e-159">Tenga en cuenta todas las opciones antes de usar la inserción de servicios como esta.</span><span class="sxs-lookup"><span data-stu-id="a834e-159">Carefully consider all options before using service injection like this.</span></span>
