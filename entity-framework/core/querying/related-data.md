---
title: 'Carga de datos relacionados: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 590d16902329ffb3fff8026f8dfdcfc887f6dea3
ms.sourcegitcommit: eefcab31142f61a7aaeac03ea90dcd39f158b8b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873190"
---
# <a name="loading-related-data"></a>Carga de datos relacionados

Entity Framework Core permite usar las propiedades de navegación del modelo para cargar las entidades relacionados. Existen tres patrones de O/RM comunes que se usan para cargar los datos relacionados.
* **Carga diligente** significa que los datos relacionados se cargan desde la base de datos como parte de la consulta inicial.
* **Carga explícita** significa que los datos relacionados se cargan de manera explícita desde la base de datos más adelante.
* **Carga diferida** significa que los datos relacionados se cargan de manera transparente desde la base de datos cuando se accede a la propiedad de navegación.

> [!TIP]  
> Puede ver un [ejemplo](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) de este artículo en GitHub.

## <a name="eager-loading"></a>Carga diligente

Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta. En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto. Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.


Puede incluir los datos relacionados de varias relaciones en una sola consulta.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>Inclusión de varios niveles

Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`. En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> Las versiones actuales de Visual Studio ofrecen opciones para completar código incorrecto y pueden provocar que las expresiones correctas se marquen como errores de sintaxis cuando se use el método `ThenInclude` después de una propiedad de navegación de colección. Este es un síntoma de un error de IntelliSense al que se hace seguimiento en https://github.com/dotnet/roslyn/issues/8237. Resulta seguro ignorar estos errores de sintaxis falsos siempre que el código sea correcto y se pueda compilar de manera adecuada. 

Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

Puede combinar todo esto para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo. Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`. Para hacerlo, debe especificar cada inicio de ruta de acceso de inclusión en la raíz. Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`. Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF consolidará las combinaciones al generar código SQL.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a>Inclusión en tipos derivados

Puede incluir datos relacionados provenientes de las navegaciones que se definen solo en un tipo derivado con `Include` y `ThenInclude`. 

Dado el modelo siguiente:

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

El contenido de la navegación `School` de todas las personas que son estudiantes se puede cargar de manera diligente mediante el uso de diversos patrones:

- con conversión
  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

- con el operador `as`
  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

- con la sobrecarga de `Include` que toma el parámetro del tipo `string`
  ```csharp
  context.People.Include("School").ToList()
  ```

### <a name="ignored-includes"></a>Inclusiones omitidas

Si modifica la consulta de manera que ya no devuelva instancias del tipo de entidad con que empezó la consulta, se omiten los operadores de inclusión.

En el ejemplo siguiente, los operadores de inclusión se basan en `Blog`, pero luego el operador `Select` se usa para cambiar la consulta para que devuelva un tipo anónimo. En este caso, los operadores de inclusión no tienen ningún efecto.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

De manera predeterminada, EF Core registrará una advertencia cuando se omitan los operadores de inclusión. Consulte [Logging](../miscellaneous/logging.md) (Registro) para más información sobre cómo ver la salida de registro. Puede cambiar el comportamiento cuando se omite un operador de inclusión, ya sea para que se genere alguna excepción o para que no haga nada. Esto se hace al configurar las opciones del contexto, por lo general en `DbContext.OnConfiguring` o en `Startup.cs` si usa ASP.NET Core.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a>Carga explícita

> [!NOTE]  
> Esta característica se introdujo en EF Core 1.1.

Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas. Si está habilitado el seguimiento de cambios, cuando se cargue una entidad, EF Core establecerá automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada y establecerá las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recién cargada.

### <a name="querying-related-entities"></a>Consulta de las entidades relacionadas

También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.

Esto permite, entre otras acciones, ejecutar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

También puede filtrar las entidades relacionadas que se cargan en la memoria.

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>Carga diferida

> [!NOTE]  
> Esta característica se introdujo en EF Core 2.1.

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
### <a name="lazy-loading-without-proxies"></a>Carga diferida sin servidores proxy

Los servidores proxy de carga diferida funcionan inyectando el servicio `ILazyLoader` en una entidad, tal como se describe en [Entity Type Constructors](../modeling/constructors.md) (Constructores de tipo de entidad). Por ejemplo:
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
Esto no requiere tipos de entidad de los cuales heredar ni propiedades de navegación para ser virtual y permite que las instancias de entidad creadas con `new` se carguen de manera diferida una vez que se asocian a un contexto. Sin embargo, requiere una referencia al servicio `ILazyLoader`, que está definido en el paquete [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/). Este paquete contiene un conjunto mínimo de tipos, por lo que es muy poco el impacto al depender de él. Sin embargo, para evitar por completo depender de cualquier paquete de EF Core en los tipos de entidad, es posible insertar el método `ILazyLoader.Load` como delegado. Por ejemplo:
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

## <a name="related-data-and-serialization"></a>Datos relacionados y serialización

Como EF Core corregirá automáticamente las propiedades de navegación, puede terminar con ciclos en el grafo de objetos. Por ejemplo, cargar un blog y sus entradas relacionadas dará lugar a un objeto de blog que hará referencia a una colección de entradas. Cada una de esas entradas tendrá una referencia de vuelta al blog.

Algunos marcos de serialización no permiten ese tipo de ciclos. Por ejemplo, JSON.NET generará la excepción siguiente si se encuentra un ciclo.

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: se detectó un bucle con autorreferencia para la propiedad "Blog" con el tipo "MyApplication.Models.Blog").

Si usa ASP.NET Core, puede configurar JSON.NET para que omita los ciclos que encuentre en el grafo del objeto. Esto se hace en el método `ConfigureServices(...)` en `Startup.cs`.

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

Otra alternativa consiste en decorar una de las propiedades de navegación con el atributo `[JsonIgnore]`, que indica a JSON.NET que no recorra esa propiedad de navegación mientras se serializa.
