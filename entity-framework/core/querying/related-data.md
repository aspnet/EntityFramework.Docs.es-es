---
title: 'Carga de datos relacionados: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: d3a1810599771befb451715d93454fff63949771
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238312"
---
# <a name="loading-related-data"></a>Carga de datos relacionados

Entity Framework Core permite usar las propiedades de navegación del modelo para cargar las entidades relacionados. Existen tres patrones de O/RM comunes que se usan para cargar los datos relacionados.

* **Carga diligente** significa que los datos relacionados se cargan desde la base de datos como parte de la consulta inicial.
* **Carga explícita** significa que los datos relacionados se cargan de manera explícita desde la base de datos más adelante.
* **Carga diferida** significa que los datos relacionados se cargan de manera transparente desde la base de datos cuando se accede a la propiedad de navegación.

> [!TIP]  
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) de este artículo en GitHub.

## <a name="eager-loading"></a>Carga diligente

Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta. En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto. Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.

Puede incluir los datos relacionados de varias relaciones en una sola consulta.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>Inclusión de varios niveles

Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`. En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

Puede combinar todo esto para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo. Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`. Para hacerlo, debe especificar cada inicio de ruta de acceso de inclusión en la raíz. Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`. Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF consolidará las combinaciones al generar código SQL.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a>Consultas únicas y divididas

> [!NOTE]
> Esta característica se incluye por primera vez en EF Core 5.0.

En las bases de datos relacionales, todas las entidades relacionadas se cargan de forma predeterminada mediante la introducción de instrucciones JOIN:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Si un blog típico tiene varias entradas relacionadas, las filas de estas entradas duplicarán la información del blog, lo que conduce al problema que se conoce como "explosión cartesiana". A medida que se cargan más relaciones uno a varios, la cantidad de datos duplicados puede crecer y afectar negativamente al rendimiento de la aplicación.

EF le permite especificar que una consulta LINQ determinada se debe *dividir* en varias consultas SQL. En lugar de instrucciones JOIN, las consultas divididas realizan una consulta SQL adicional por cada navegación de uno a varios incluida:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

El resultado es la siguiente consulta SQL:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

Si bien se evitan los problemas de rendimiento asociados a las instrucciones JOIN y la explosión cartesiana, también existen algunas desventajas:

* Aunque la mayoría de las bases de datos garantizan la coherencia de los datos en las consultas únicas, no sucede lo mismo en el caso de varias consultas. Esto significa que, si la base de datos se actualiza al mismo tiempo que se ejecutan las consultas, es posible que los datos resultantes no sean coherentes. Para resolver este problema, se pueden encapsular las consultas en una transacción serializable o de instantáneas, aunque esta solución puede generar problemas de rendimiento propios. Para más información, consulte la documentación de la base de datos.
* Cada consulta supone actualmente un recorrido adicional de ida y vuelta de la red a la base de datos; como consecuencia, se puede degradar el rendimiento, en especial cuando la latencia hacia la base de datos es alta (por ejemplo, servicios en la nube). EF Core mejorará esta situación en el futuro gracias al procesamiento por lotes de las consultas en un único recorrido de ida y vuelta.
* Aunque algunas bases de datos permiten el consumo de los resultados de varias consultas al mismo tiempo (SQL Server con MARS, SQLite), la mayoría de ellas solo permiten sola consulta activa en un momento dado. Esto significa que todos los resultados de las consultas anteriores deben almacenarse en búfer en la memoria de la aplicación antes de ejecutar consultas posteriores, lo que aumenta los requisitos de memoria de una manera importante.

Por desgracia, no hay una estrategia para cargar entidades relacionadas que se ajuste a todos los escenarios. Tenga en cuenta las ventajas y desventajas de las consultas únicas y divididas, y seleccione la que mejor se ajuste a sus necesidades.

> [!NOTE]
> Las entidades relacionadas uno a uno se cargan siempre a través de instrucciones JOIN, ya que el rendimiento no se ve afectado.
>
> Por el momento, el uso de la división de consultas en SQL Server requiere la configuración `MultipleActiveResultSets=true` en la cadena de conexión. Este requisito desaparecerá en una versión preliminar futura.
>
> Las versiones preliminares futuras de EF Core 5.0 permitirán especificar la división de consultas como valor predeterminado del contexto.

### <a name="filtered-include"></a>Inclusión filtrada

> [!NOTE]
> Esta característica se incluye por primera vez en EF Core 5.0.

Al aplicar Include para cargar datos relacionados, puede aplicar determinadas operaciones enumerables en la navegación de colección incluida, lo que permite filtrar y ordenar los resultados.

Las operaciones que se admiten son: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` y `Take`.

Dichas operaciones se deben aplicar en la navegación de colección en la expresión lambda que se pasa al método Include, como se muestra en el ejemplo siguiente:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

Cada navegación incluida solo permite un único conjunto de operaciones de filtro. En los casos en los que se aplican varias operaciones Include para una navegación de colección determinada (`blog.Posts` en los ejemplos siguientes), las operaciones de filtro solo se pueden especificar en una de ellas: 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

De manera alternativa, se pueden aplicar operaciones idénticas para cada navegación que esté incluida varias veces:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> En el caso de las consultas de seguimiento, los resultados de Inclusión filtrada pueden ser inesperados debido a la [corrección de la navegación](tracking.md). Todas las entidades pertinentes que se hayan consultado anteriormente y que se hayan almacenado en el seguimiento de cambios estarán presentes en los resultados de la consulta Inclusión filtrada aunque no cumplan los requisitos del filtro. Valore la posibilidad de usar consultas `NoTracking` o de volver a crear el elemento DbContext al emplear Inclusión filtrada en esas situaciones.

Ejemplo:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

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

* con conversión

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* con el operador `as`

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* con la sobrecarga de `Include` que toma el parámetro del tipo `string`

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a>Carga explícita

Puede cargar de manera explícita una propiedad de navegación a través de la API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

También puede cargar de manera explícita una propiedad de navegación si ejecuta una consulta independiente que devuelve las entidades relacionadas. Si está habilitado el seguimiento de cambios, cuando se cargue una entidad, EF Core establecerá automáticamente las propiedades de navegación de la entidad recién cargada para hacer referencia a cualquier entidad ya cargada y establecerá las propiedades de navegación de las entidades ya cargadas para hacer referencia a la entidad recién cargada.

### <a name="querying-related-entities"></a>Consulta de las entidades relacionadas

También puede obtener una consulta LINQ que represente el contenido de una propiedad de navegación.

Esto permite, entre otras acciones, ejecutar un operador de agregado en las entidades relacionadas sin cargarlas en la memoria.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

También puede filtrar las entidades relacionadas que se cargan en la memoria.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>Carga diferida

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
