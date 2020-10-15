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
# <a name="entity-types-with-constructors"></a>Tipos de entidad con constructores

Es posible definir un constructor con parámetros y hacer que EF Core llame a este constructor al crear una instancia de la entidad. Los parámetros de constructor se pueden enlazar a propiedades asignadas o a varios tipos de servicios para facilitar comportamientos como la carga diferida.

> [!NOTE]
> Actualmente, todos los enlaces de constructor son por Convención. La configuración de constructores específicos que se va a usar está planeada para una versión futura.

## <a name="binding-to-mapped-properties"></a>Enlazar a propiedades asignadas

Considere un modelo típico de blog o post:

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

Cuando EF Core crea instancias de estos tipos, como los resultados de una consulta, primero llamará al constructor sin parámetros predeterminado y, a continuación, establecerá cada propiedad en el valor de la base de datos. Sin embargo, si EF Core encuentra un constructor con parámetros con nombres de parámetros y tipos que coinciden con los de propiedades asignadas, se llamará en su lugar al constructor con parámetros con valores para esas propiedades y no establecerá cada propiedad explícitamente. Por ejemplo:

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

Cosas que tener en cuenta:

* No todas las propiedades deben tener parámetros de constructor. Por ejemplo, la propiedad post. Content no está establecida por ningún parámetro de constructor, por lo que EF Core la establecerá después de llamar al constructor de la manera normal.
* Los nombres y tipos de parámetros deben coincidir con los nombres y tipos de propiedad, con la excepción de que las propiedades pueden tener mayúsculas y minúsculas Pascal, mientras que los parámetros tienen mayúsculas y minúsculas Camel.
* EF Core no pueden establecer las propiedades de navegación (como blog o publicaciones anteriores) mediante un constructor.
* El constructor puede ser público, privado o tener cualquier otra accesibilidad. Sin embargo, los proxies de carga diferida requieren que el constructor sea accesible desde la clase de proxy heredada. Normalmente esto significa que se hace público o protegido.

### <a name="read-only-properties"></a>Propiedades de solo lectura

Una vez que las propiedades se establecen mediante el constructor, puede tener sentido que algunas de ellas sean de solo lectura. EF Core es compatible con esto, pero hay algunas cosas que debe buscar:

* Las propiedades sin establecedores no se asignan por Convención. (Esto tiende a asignar propiedades que no deben asignarse, como las propiedades calculadas).
* El uso de valores de clave generados automáticamente requiere una propiedad de clave que sea de lectura y escritura, ya que el valor de clave debe establecerse mediante el generador de claves al insertar nuevas entidades.

Una manera sencilla de evitar estos aspectos es usar establecedores privados. Por ejemplo:

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

EF Core ve una propiedad con un establecedor privado como de lectura y escritura, lo que significa que todas las propiedades se asignan como antes y la clave todavía se puede generar en el almacén.

Una alternativa al uso de establecedores privados es hacer que las propiedades sean realmente de solo lectura y agregar una asignación más explícita en OnModelCreating. Del mismo modo, algunas propiedades se pueden quitar por completo y reemplazar solo por campos. Por ejemplo, considere estos tipos de entidad:

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

Y esta configuración en OnModelCreating:

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

Cosas que hay que tener en cuenta:

* La clave "propiedad" es ahora un campo. No es un `readonly` campo para que se puedan usar claves generadas por el almacén.
* Las demás propiedades son propiedades de solo lectura establecidas solo en el constructor.
* Si el valor de la clave principal solo se establece en EF o se lee de la base de datos, no es necesario incluirlo en el constructor. Esto deja la clave "Property" como un campo simple y deja claro que no se debe establecer explícitamente al crear nuevos blogs o publicaciones.

> [!NOTE]
> Este código producirá una advertencia del compilador ' 169 ' que indica que el campo nunca se utiliza. Esto puede pasarse por alto, ya que en realidad EF Core está utilizando el campo de forma extralingüística.

## <a name="injecting-services"></a>Insertar servicios

EF Core también puede insertar "servicios" en el constructor de un tipo de entidad. Por ejemplo, se puede insertar lo siguiente:

* `DbContext` -la instancia de contexto actual, que también se puede escribir como el tipo de DbContext derivado.
* `ILazyLoader` -el servicio de carga diferida; consulte la [documentación sobre la carga diferida](xref:core/querying/related-data) para obtener más detalles.
* `Action<object, string>` -un delegado de carga diferida; consulte la [documentación de carga diferida](xref:core/querying/related-data) para obtener más detalles.
* `IEntityType` -los metadatos de EF Core asociados a este tipo de entidad

> [!NOTE]
> Actualmente, solo se pueden insertar los servicios conocidos por EF Core. Se está considerando la compatibilidad con la inserción de servicios de aplicación en una versión futura.

Por ejemplo, un DbContext insertado se puede usar para tener acceso de forma selectiva a la base de datos para obtener información sobre las entidades relacionadas sin cargarlas todas. En el ejemplo siguiente, se usa para obtener el número de publicaciones en un blog sin cargar las entradas:

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

Algunos aspectos que se deben tener en cuenta:

* El constructor es privado, ya que nunca lo llama EF Core, y hay otro constructor público para uso general.
* El código que usa el servicio inyectado (es decir, el contexto) está defensivo con el `null` fin de controlar los casos en los que EF Core no está creando la instancia.
* Dado que el servicio se almacena en una propiedad de lectura y escritura, se restablecerá cuando la entidad se adjunte a una nueva instancia de contexto.

> [!WARNING]
> Inyectar DbContext como esto se suele considerar un anti-patrón, ya que une los tipos de entidad directamente a EF Core. Tenga en cuenta todas las opciones antes de usar la inserción de servicios como esta.
