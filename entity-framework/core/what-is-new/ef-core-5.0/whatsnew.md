---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 8fa45bf31cb5f1a7e35134f9513a40469719f8c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065620"
---
# <a name="whats-new-in-ef-core-50"></a>Novedades en EF Core 5.0

Ya se han completado todas las características planeadas para EF Core 5.0. Esta página contiene información general sobre los cambios interesantes introducidos en cada versión preliminar.

Esta página no duplica el [plan para EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan). En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.

## <a name="rc1"></a>RC1

### <a name="many-to-many"></a>Varios a varios

EF Core 5.0 admite relaciones de varios a varios sin asignar explícitamente la tabla de combinación.

Por ejemplo, considere estos tipos de entidad:

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Tag> Tags { get; set; }
}

public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public ICollection<Post> Posts { get; set; }
}
```

Observe que `Post` contiene una colección de `Tags` y `Tag` contiene una colección de `Posts`. EF Core 5.0 reconoce esto como una relación de varios a varios por convención. Esto significa que no se requiere ningún código en `OnModelCreating`:

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Blog> Blogs { get; set; }
}
```

Cuando se usan migraciones (o `EnsureCreated`) para crear la base de datos, EF Core creará automáticamente la tabla de combinación. Por ejemplo, en SQL Server para este modelo, EF Core genera:

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id])
);

CREATE TABLE [Tag] (
    [Id] int NOT NULL IDENTITY,
    [Text] nvarchar(max) NULL,
    CONSTRAINT [PK_Tag] PRIMARY KEY ([Id])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] int NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([Id]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tag_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tag] ([Id]) ON DELETE CASCADE
);

CREATE INDEX [IX_PostTag_TagsId] ON [PostTag] ([TagsId]);
```

Al crear y asociar entidades `Blog` y `Post`, se producen automáticamente actualizaciones de la tabla de combinación. Por ejemplo:

```csharp
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

Después de insertar las entradas y etiquetas, EF creará automáticamente las filas en la tabla de combinación. Por ejemplo, en SQL Server:

```sql
SET NOCOUNT ON;
INSERT INTO [PostTag] ([PostsId], [TagsId])
VALUES (@p6, @p7),
(@p8, @p9),
(@p10, @p11),
(@p12, @p13),
(@p14, @p15),
(@p16, @p17);
```

En el caso de las consultas, Include y otras operaciones de consulta funcionan igual que para cualquier otra relación. Por ejemplo:

```csharp
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

El SQL generado utiliza la tabla de combinación automáticamente para devolver todas las etiquetas relacionadas:

```sql
SELECT [p].[Id], [p].[Name], [t0].[PostsId], [t0].[TagsId], [t0].[Id], [t0].[Text]
FROM [Posts] AS [p]
LEFT JOIN (
    SELECT [p0].[PostsId], [p0].[TagsId], [t].[Id], [t].[Text]
    FROM [PostTag] AS [p0]
    INNER JOIN [Tag] AS [t] ON [p0].[TagsId] = [t].[Id]
) AS [t0] ON [p].[Id] = [t0].[PostsId]
ORDER BY [p].[Id], [t0].[PostsId], [t0].[TagsId], [t0].[Id]
```

A diferencia de EF6, EF Core permite la personalización completa de la tabla de combinación. Por ejemplo, el código siguiente configura una relación de varios a varios que también tiene navegaciones a la entidad de combinación y en la que la entidad de combinación contiene una propiedad de carga:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Post>()
        .HasMany(p => p.Tags)
        .WithMany(p => p.Posts)
        .UsingEntity<PostTag>(
            j => j
                .HasOne(pt => pt.Tag)
                .WithMany()
                .HasForeignKey(pt => pt.TagId),
            j => j
                .HasOne(pt => pt.Post)
                .WithMany()
                .HasForeignKey(pt => pt.PostId),
            j =>
            {
                j.Property(pt => pt.PublicationDate).HasDefaultValueSql("CURRENT_TIMESTAMP");
                j.HasKey(t => new { t.PostId, t.TagId });
            });
}
```

### <a name="map-entity-types-to-queries"></a>Asignación de tipos de entidad a consultas

Normalmente, los tipos de entidad se asignan a tablas o vistas, de modo que EF Core extraerá el contenido de la tabla o vista al consultar ese tipo. EF Core 5.0 permite asignar un tipo de entidad a una "consulta de definición". (Esto se admitía parcialmente en versiones anteriores, pero se ha mejorado mucho y tiene otra sintaxis en EF Core 5.0).

Por ejemplo, considere dos tablas: una con publicaciones modernas, la otra con publicaciones heredadas. La tabla de publicaciones modernas tiene algunas columnas adicionales, pero a efectos de nuestra aplicación queremos combinar las publicaciones modernas y las heredadas y asignarlas a un tipo de entidad con todas las propiedades necesarias:

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

En EF Core 5.0, se puede usar `ToSqlQuery` para asignar este tipo de entidad a una consulta que extrae y combina filas de ambas tablas:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

Observe que la tabla `legacy_posts` no tiene una columna `Category`, por lo que en su lugar sintetizamos un valor predeterminado para todas las publicaciones heredadas.

Este tipo de entidad se puede usar de la manera normal para las consultas LINQ. Por ejemplo. La consulta LINQ:

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

Genera el siguiente código SQL en SQLite:

```sql
SELECT "p"."Id", "p"."BlogId", "p"."Category", "p"."Name"
FROM (
    SELECT Id, Name, Category, BlogId FROM posts
    UNION ALL
    SELECT Id, Name, "Legacy", BlogId from legacy_posts
) AS "p"
INNER JOIN "Blogs" AS "b" ON "p"."BlogId" = "b"."Id"
WHERE ('Unicorn' = '') OR (instr("b"."Name", 'Unicorn') > 0)
```

Observe que la consulta configurada para el tipo de entidad se usa como elemento inicial para componer la consulta LINQ completa.

### <a name="event-counters"></a>Contadores de eventos

[Los contadores de eventos de .NET](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) son una manera eficaz de exponer las métricas de rendimiento de una aplicación. EF Core 5.0 incluye contadores de eventos en la categoría `Microsoft.EntityFrameworkCore`. Por ejemplo:

```
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

Esto indica a los contadores de dotnet que empiecen a recopilar eventos de EF Core para el proceso 49496. Esto genera una salida similar a la siguiente en la consola:

```
[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                     1,755
    Queries (Total)                                            98,402
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             1
```

### <a name="property-bags"></a>Contenedores de propiedades

EF Core 5.0 permite asignar el mismo tipo de CLR a varios tipos distintos de entidad. Estos tipos se conocen como tipos de entidad de tipo compartido. Esta característica combinada con las propiedades del indexador (incluidas en la versión preliminar 1) permite usar contenedores de propiedades como tipo de entidad.

Por ejemplo, el elemento DbContext siguiente configura el tipo de BCL `Dictionary<string, object>` como tipo de entidad de tipo compartido para productos y categorías.

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");
    public DbSet<Dictionary<string, object>> Categories => Set<Dictionary<string, object>>("Category");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Category", b =>
        {
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
        });

        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<decimal>("Price");
            b.IndexerProperty<int?>("CategoryId");

            b.HasOne("Category", null).WithMany();
        });
    }
}
```

Ahora se pueden agregar objetos de diccionario ("contenedores de propiedades") al contexto como instancias de entidad y guardarse. Por ejemplo:

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

Estas entidades se pueden consultar y actualizar de la manera normal:

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a>Interceptación y eventos de SaveChanges

EF Core 5.0 introduce tanto eventos de .NET como un interceptor EF Core que se desencadena al llamar a SaveChanges.

Los eventos son sencillos de usar; por ejemplo:

```csharp
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

Tenga en lo siguiente:
* El remitente del evento es la instancia de `DbContext`.
* El elemento args del evento `SavedChanges` contiene el número de entidades guardadas en la base de datos.

`ISaveChangesInterceptor` define el interceptor, pero a menudo es conveniente que se herede de `SaveChangesInterceptor` para evitar la implementación de todos los métodos. Por ejemplo:

```csharp
public class MySaveChangesInterceptor : SaveChangesInterceptor
{
    public override InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        Console.WriteLine($"Saving changes for {eventData.Context.Database.GetConnectionString()}");

        return result;
    }

    public override ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        Console.WriteLine($"Saving changes asynchronously for {eventData.Context.Database.GetConnectionString()}");

        return new ValueTask<InterceptionResult<int>>(result);
    }
}
```

Tenga en lo siguiente:
* El interceptor tiene métodos sincrónicos y asincrónicos. Esto puede ser útil si tiene que realizar operaciones de E/S asincrónicas, como escribir en un servidor de auditoría.
* El interceptor permite omitir SaveChanges mediante el mecanismo `InterceptionResult` común a todos los interceptores.

El inconveniente de los interceptores es que deben registrarse en DbContext cuando se construyen. Por ejemplo:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

En cambio, los eventos se pueden registrar en la instancia de DbContext en cualquier momento.

### <a name="exclude-tables-from-migrations"></a>Exclusión de tablas de migraciones

A veces resulta útil tener un solo tipo de entidad asignado en varios elementos DbContext. Esto es especialmente cierto cuando se usan [contextos delimitados](https://www.martinfowler.com/bliki/BoundedContext.html), para los que es común tener un tipo DbContext diferente para cada contexto enlazado.

Por ejemplo, es posible que un contexto de autorización y un contexto de informes necesiten un tipo `User`. Si se realiza un cambio en el tipo `User`, las migraciones de ambos elementos DbContext intentarán actualizar la base de datos. Para evitar esto, el modelo de uno de los contextos se puede configurar para excluir la tabla de sus migraciones.

En el código siguiente, el `AuthorizationContext` generará migraciones para los cambios en la tabla `Users`, pero el `ReportingContext` no lo hará, lo que impedirá que las migraciones entren en conflicto.

```csharp
public class AuthorizationContext : DbContext
{
    public DbSet<User> Users { get; set; }
}

public class ReportingContext : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>().ToTable("Users", t => t.ExcludeFromMigrations());
    }
}
```

### <a name="required-11-dependents"></a>Dependientes de 1:1 requeridos

En EF Core 3.1, el extremo dependiente de una relación uno a uno se consideraba siempre opcional. Esto era más que evidente al usar entidades en propiedad. Por ejemplo, considere el siguiente modelo y configuración:

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Address HomeAddress { get; set; }
    public Address WorkAddress { get; set; }
}

public class Address
{
    public string Line1 { get; set; }
    public string Line2 { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string Country { get; set; }
    public string Postcode { get; set; }
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

Esto hace que las migraciones creen la siguiente tabla para SQLite:

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NULL,
    "HomeAddress_Region" TEXT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

Tenga en cuenta que todas las columnas aceptan valores NULL, aunque algunas de las propiedades de `HomeAddress` se han configurado según sea necesario. Además, al consultar un `Person`, si todas las columnas de la dirección particular o de trabajo son NULL, EF Core dejará las propiedades `HomeAddress` o `WorkAddress` como NULL, en lugar de establecer una instancia vacía de `Address`.

En EF Core 5.0, la navegación de `HomeAddress` se puede configurar ahora como elemento dependiente necesario. Por ejemplo:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

La tabla creada por las migraciones incluirá ahora columnas que no aceptan valores NULL para las propiedades necesarias del elemento dependiente necesario:

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NOT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NOT NULL,
    "HomeAddress_Region" TEXT NOT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NOT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

Además, EF Core iniciará ahora una excepción si se realiza un intento de guardar un propietario que tiene un elemento dependiente necesario NULL. En este ejemplo, EF Core producirá una excepción al intentar guardar un `Person` con un `HomeAddress` NULL.

Por último, EF Core creará una instancia de un elemento dependiente necesario aunque todas las columnas del elemento dependiente necesario tengan valores NULL.

### <a name="options-for-migration-generation"></a>Opciones para la generación de migraciones

EF Core 5.0 introduce un mayor control sobre la generación de migraciones con distintos fines. Esto incluye la posibilidad de:

* Saber si la migración se genera para un script o para su inmediata ejecución
* Saber si se está generando un script idempotente
* Saber si el script debe excluir las instrucciones de transacción (consulte _Scripts de migración con transacciones_ a continuación).

Este comportamiento se especifica mediante una enumeración `MigrationsSqlGenerationOptions`, que ahora se puede pasar a `IMigrator.GenerateScript`.

También se incluye en este trabajo una mejor generación de scripts idempotentes con llamadas a `EXEC` en SQL Server cuando sea necesario. Este trabajo también habilita mejoras similares a los scripts generados por otros proveedores de bases de datos, incluido PostgreSQL.

### <a name="migrations-scripts-with-transactions"></a>Scripts de migración con transacciones

Los scripts SQL generados a partir de migraciones ahora contienen instrucciones para iniciar y confirmar las transacciones según sea necesario para la migración. Por ejemplo, el script de migración siguiente se generó a partir de dos migraciones. Observe que cada migración se aplica ahora en una transacción.

```sql
BEGIN TRANSACTION;
GO

CREATE TABLE [Groups] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Groups] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Members] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [GroupId] int NULL,
    CONSTRAINT [PK_Members] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Members_Groups_GroupId] FOREIGN KEY ([GroupId]) REFERENCES [Groups] ([Id]) ON DELETE NO ACTION
);
GO

CREATE INDEX [IX_Members_GroupId] ON [Members] ([GroupId]);
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910194835_One', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
GO

BEGIN TRANSACTION;
GO

EXEC sp_rename N'[Groups].[Name]', N'GroupName', N'COLUMN';
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910195234_Two', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
```

Tal y como se mencionó en la sección anterior, se puede deshabilitar este uso de las transacciones si es necesario controlar las transacciones de otra forma.

### <a name="see-pending-migrations"></a>Consulta de migraciones pendientes

El miembro de la comunidad [@Psypher9](https://github.com/Psypher9) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

El comando `dotnet ef migrations list` muestra ahora las migraciones que todavía no se han aplicado a la base de datos. Por ejemplo:

```
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

Además, ahora hay un comando `Get-Migration` para la consola del administrador de paquetes con la misma funcionalidad.

### <a name="modelbuilder-api-for-value-comparers"></a>API ModelBuilder para comparadores de valores

Las propiedades de EF Core para tipos mutables personalizados [requieren un comparador de valores](xref:core/modeling/value-comparers) para que los cambios de propiedad se detecten correctamente. Ahora se pueden especificar como parte de la configuración de la conversión de valor para el tipo. Por ejemplo:

```csharp
modelBuilder
    .Entity<EntityType>()
    .Property(e => e.MyProperty)
    .HasConversion(
        v => JsonSerializer.Serialize(v, null),
        v => JsonSerializer.Deserialize<List<int>>(v, null),
        new ValueComparer<List<int>>(
            (c1, c2) => c1.SequenceEqual(c2),
            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
            c => c.ToList()));
```

### <a name="entityentry-trygetvalue-methods"></a>Métodos TryGetValue de EntityEntry

El miembro de la comunidad [@m4ss1m0g](https://github.com/m4ss1m0g) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

Se ha agregado un método `TryGetValue` a `EntityEntry.CurrentValues` y `EntityEntry.OriginalValues`. Esto permite solicitar el valor de una propiedad sin comprobar primero si la propiedad está asignada en el modelo EF. Por ejemplo:

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a>Tamaño de lote máximo predeterminado para SQL Server

A partir de EF Core 5.0, el tamaño máximo predeterminado del lote para SaveChanges en SQL Server es ahora 42. Como es bien sabido, esta es también la respuesta a la última pregunta de la vida, el universo y todo lo demás. Sin embargo, es probable que se trate de una coincidencia, ya que el valor se obtuvo a través de [análisis del rendimiento del procesamiento por lotes](https://github.com/dotnet/efcore/issues/9270). No creemos que hayamos descubierto una forma de la última pregunta, aunque parece algo plausible que la tierra se haya creado para comprender por qué SQL Server funciona de la forma en que lo hace.

### <a name="default-environment-to-development"></a>Entorno predeterminado en desarrollo

Las herramientas de línea de comandos de EF Core ahora configuran automáticamente las variables de entorno `ASPNETCORE_ENVIRONMENT` _y_ `DOTNET_ENVIRONMENT` en "desarrollo". Esto aporta la experiencia al usar el host genérico en línea con la experiencia de ASP.NET Core durante el desarrollo. Consulte [n.º 19903](https://github.com/dotnet/efcore/issues/19903).

### <a name="better-migrations-column-ordering"></a>Mejor ordenación de las columnas de migraciones

Las columnas de las clases base no asignadas se ordenan ahora después de otras columnas para los tipos de entidad asignados. Tenga en cuenta que esto solo afecta a las tablas recién creadas. El orden de las columnas de las tablas existentes permanece sin cambios. Consulte [n.º 11314](https://github.com/dotnet/efcore/issues/11314).

### <a name="query-improvements"></a>Mejoras en las consultas

EF Core 5.0 RC1 contiene algunas mejoras adicionales en la traducción de consultas:

* Traducción de `is` en Cosmos: consultar [n.º 16391](https://github.com/dotnet/efcore/issues/16391)
* Ahora se pueden anotar funciones asignadas por el usuario para controlar la propagación de valores NULL: consultar [n.º 19609](https://github.com/dotnet/efcore/issues/19609)
* Compatibilidad con la traducción de GroupBy con agregados condicionales: consultar [n.º 11711](https://github.com/dotnet/efcore/issues/11711)
* Traducción del operador DISTINCT en el elemento de grupo antes del agregado: consultar [n.º 17376](https://github.com/dotnet/efcore/issues/17376)

### <a name="model-building-for-fields"></a>Creación de modelos para campos

Por último, para RC1, EF Core ahora permite el uso de los métodos lambda en ModelBuilder para los campos y las propiedades. Por ejemplo, si por alguna razón es reacio a las propiedades y decide usar campos públicos, estos campos ahora se pueden asignar mediante los generadores de lambda:

```csharp
public class Post
{
    public int Id;
    public string Name;
    public string Category;
    public int BlogId;
    public Blog Blog;
}

public class Blog
{
    public int Id;
    public string Name;
    public ICollection<Post> Posts;
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
    });

    modelBuilder.Entity<Post>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
        b.Property(e => e.Category);
        b.Property(e => e.BlogId);
        b.HasOne(e => e.Blog).WithMany(e => e.Posts);
    });
}
```

Aunque esto ahora es posible, no se recomienda hacerlo. Además, tenga en cuenta que esto no agrega ninguna funcionalidad de asignación de campos adicional a EF Core, solo permite usar los métodos lambda en lugar de requerir siempre los métodos de cadena. Esto no resulta muy útil, ya que los campos no suelen ser públicos.

## <a name="preview-8"></a>Versión preliminar 8

### <a name="table-per-type-tpt-mapping"></a>Asignación de tabla por tipo (TPT)

De forma predeterminada, EF Core asigna una jerarquía de herencia de tipos .NET a una tabla de base de datos única. Esto se conoce como asignación de tabla por jerarquía (TPH). EF Core 5.0 también permite asignar cada tipo .NET de una jerarquía de herencia a una tabla de base de datos diferente; esto se conoce como asignación de tabla por tipo (TPT).

Por ejemplo, piense en este modelo con una jerarquía asignada:

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

De forma predeterminada, EF Core lo asignará a una tabla única:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

Sin embargo, la asignación de cada tipo de entidad a una tabla diferente dará como resultado en su lugar una tabla por tipo:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

Tenga en cuenta que la creación de las restricciones de clave externa mostradas anteriormente se han agregado después de bifurcar el código para la versión preliminar 8.

Los tipos de entidad pueden asignarse a tablas diferentes mediante atributos de asignación:

```csharp
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

O mediante la configuración `ModelBuilder`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

El seguimiento de la documentación se realiza mediante el número de incidencia [1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).

### <a name="migrations-rebuild-sqlite-tables"></a>Migraciones: recompilación de tablas de SQLite

En comparación con otras bases de datos, SQLite está relativamente limitada en sus capacidades de manipulación de esquemas. Por ejemplo, quitar una columna de una tabla existente requiere quitar y volver a crear la tabla completa. Las migraciones de EF Core 5.0 ahora admiten la recompilación automática de la tabla para los cambios de esquema que la necesiten.

Por ejemplo, imagine que tenemos una tabla `Unicorns` creada para un tipo de entidad `Unicorn`:

```csharp
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

Posteriormente, veremos que el almacenamiento de la edad de un unicornio se considera muy superficial, así que vamos a quitar esa propiedad, a agregar una nueva migración y a actualizar la base de datos. Esta actualización producirá un error al usar EF Core 3.1 porque no se puede quitar la columna. En EF Core 5.0, en lugar de eso, las migraciones recompilarán la tabla:

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

Tenga en lo siguiente:
* Se crea una tabla temporal con el esquema deseado para la nueva tabla.
* Los datos se copian de la tabla actual en la tabla temporal.
* La aplicación de clave externa está desactivada.
* Se quita la tabla actual.
* Se cambia el nombre de la tabla temporal para hacerla la tabla nueva.

El seguimiento de la documentación se realiza mediante el número de incidencia [2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).

### <a name="table-valued-functions"></a>Funciones con valores de tabla

El miembro de la comunidad [@pmiddleton](https://github.com/pmiddleton) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

EF Core 5.0 incluye compatibilidad de primera clase para asignar métodos .NET a funciones con valores de tabla (TVF). Estas funciones se pueden usar en consultas LINQ, donde la composición adicional de los resultados de la función también se traducirá a SQL.

Por ejemplo, considere esta TVF definida en una base de datos SQL Server:

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

El modelo de EF Core requiere dos tipos de entidad para usar esta TVF:
* Un tipo `Employee` que se asigna a la tabla Employees de la forma habitual.
* Un tipo `Report` que coincide con la forma que devuelve la función TVF.

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```csharp
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

Estos tipos se deben incluir en el modelo de EF Core:

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

Observe que `Report` no tiene ninguna clave principal, por lo que debe configurarse como tal.

Por último, se debe asignar un método .NET a la función TVF en la base de datos. Este método se puede definir en DbContext mediante el método `FromExpression` nuevo:

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

Este método usa un parámetro y un tipo de valor devuelto que coinciden con la función TVF definida anteriormente. Después, el método se agrega al modelo de EF Core en OnModelCreating:

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

(Usar una expresión lambda aquí es una manera sencilla de pasar `MethodInfo` a EF Core. Se omiten los argumentos que se pasan al método).

Ahora podemos escribir consultas que llamen a `GetReports` y se creen sobre los resultados. Por ejemplo:

```csharp
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

En SQL Server, esto se traduce en lo siguiente:

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

Observe que SQL se ha modificado en la tabla `Employees`, llama a `GetReports` y, luego, agrega una cláusula WHERE adicional a los resultados de la función.

### <a name="flexible-queryupdate-mapping"></a>Asignación flexible de consultas y actualizaciones

EF Core 5.0 permite asignar el mismo tipo de entidad a objetos de base de datos diferentes. Estos objetos pueden ser tablas, vistas o funciones.

Por ejemplo, un tipo de entidad se puede asignar a una vista de base de datos y una tabla de base de datos:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

De forma predeterminada, EF Core consultará desde la vista y enviará actualizaciones a la tabla. Por ejemplo, ejecutar el código siguiente:

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

Da como resultado una consulta en la vista y, luego, una actualización de la tabla:

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a>Configuración de la consulta dividida en todo el contexto

Las consultas divididas (vea más abajo) ahora se pueden configurar como valor predeterminado para cualquier consulta que ejecute DbContext. Esta configuración solo está disponible para los proveedores relacionales, por lo que debe especificarse como parte de la configuración de `UseProvider`. Por ejemplo:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

El seguimiento de la documentación se realiza mediante la incidencia [n.º 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="physicaladdress-mapping"></a>Asignación de PhysicalAddress

El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

Ahora, la [clase PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa. Para obtener más información, vea más abajo los ejemplos para `IPAddress`.

## <a name="preview-7"></a>Versión preliminar 7

### <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5.0 presenta `AddDbContextFactory` y `AddPooledDbContextFactory` para registrar una factoría que crea instancias de DbContext en el contenedor de inserción de dependencias (DI) de la aplicación. Por ejemplo:

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

Los servicios de aplicaciones, como los controladores de ASP.NET Core, pueden depender de `IDbContextFactory<TContext>` en el constructor del servicio. Por ejemplo:

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

A continuación, las instancias de DbContext se pueden crear y usar según sea necesario. Por ejemplo:

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

Tenga en cuenta que las instancias de DbContext creadas de este modo _no_ están administradas por el proveedor de servicios de la aplicación y, por lo tanto, la aplicación debe desecharlas. Este desacoplamiento es muy útil para las aplicaciones Blazor, en las que se recomienda usar `IDbContextFactory`, pero también puede ser práctico en otros escenarios.

Las instancias DbContext se pueden agrupar mediante una llamada a `AddPooledDbContextFactory`. Esta agrupación funciona de la misma manera que para `AddDbContextPool`, y también tiene las mismas limitaciones.

En el problema [n.º 2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) se realiza el seguimiento de la documentación.

### <a name="reset-dbcontext-state"></a>Restablecimiento del estado de DbContext

EF Core 5.0 presenta `ChangeTracker.Clear()`, que borra la instancia de DbContext de todas las entidades de las que se ha realizado un seguimiento. Normalmente, esto no es necesario si se aplica el procedimiento recomendado de crear una nueva instancia de contexto de corta duración para cada unidad de trabajo. No obstante, si es necesario restablecer el estado de una instancia de DbContext, el uso del método `Clear()` es más eficaz y robusto que la separación masiva de todas las entidades.

El seguimiento de la documentación se realiza mediante la incidencia [n.º 2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).

### <a name="new-pattern-for-store-generated-defaults"></a>Nuevo patrón para los valores predeterminados generados por el almacén

EF Core permite establecer un valor explícito para una columna que también puede tener una restricción de valor predeterminado. EF Core usa el valor predeterminado de CLR de tipo de propiedad Type como centinela para este. Si el valor no es el predeterminado de CLR, se inserta; de lo contrario, se usa el valor predeterminado de la base de datos.

Esto crea problemas en los tipos en los que el valor predeterminado de CLR no es un buen centinela, en particular, las propiedades `bool`. EF Core 5.0 ahora permite que la variable de respaldo acepte valores NULL para los casos como este. Por ejemplo:

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

Tenga en cuenta que la variable de respaldo admite valores NULL, pero la propiedad expuesta públicamente, no. Esto permite que el valor de centinela sea `null` sin afectar a la superficie pública del tipo de entidad. En este caso, si `IsValid` nunca se establece, se usará el valor predeterminado de la base de datos, ya que el campo de respaldo sigue siendo NULL. Si se establece `true` o `false`, este valor se guarda explícitamente en la base de datos.

El seguimiento de la documentación se realiza mediante la incidencia [n.º 2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).

### <a name="cosmos-partition-keys"></a>Claves de partición de Cosmos

EF Core permite que la clave de partición de Cosmos se incluya en el modelo EF. Por ejemplo:

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

A partir de la versión preliminar 7, la clave de partición se incluye en la clave pública del tipo de entidad y se usa para mejorar el rendimiento en algunas consultas.

El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="cosmos-configuration"></a>Configuración de Cosmos

EF Core 5.0 mejora la configuración de Cosmos y de las conexiones Cosmos.

Anteriormente, EF Core requería especificar explícitamente el punto de conexión y la clave al conectarse a una base de datos de Cosmos. En su lugar, EF Core 5.0 permite usar una cadena de conexión. Además, EF Core 5.0 permite establecer explícitamente la instancia de WebProxy. Por ejemplo:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

Ahora también se pueden configurar muchos otros valores de tiempo de espera, límites, etc. Por ejemplo:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

Por último, el modo de conexión predeterminado es ahora `ConnectionMode.Gateway`, que suele ofrecer mayor compatibilidad.

El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="scaffold-dbcontext-now-singularizes"></a>Singularización en scaffolding de DbContext

Anteriormente, al aplicar scaffolding a un elemento DbContext de una base de datos existente, EF Core creaba nombres de tipo de entidad que coincidían con los de las tablas de la base de datos. Por ejemplo, las tablas `People` y `Addresses` producían tipos de entidad denominados `People` y `Addresses`.

En versiones anteriores, este comportamiento se podía configurar mediante el registro de un servicio de pluralización. Ahora, en EF Core 5.0, el paquete [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) se usa como servicio de pluralización predeterminado. Esto significa que, ahora, en las tablas `People` y `Addresses`, se aplicarán técnicas de ingeniería inversa para obtener los tipos de entidad denominados `Person` y `Address`.

### <a name="savepoints"></a>Puntos de retorno

EF Core admite ahora [puntos de retorno](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) para un mayor control sobre las transacciones que ejecutan varias operaciones.

Los puntos de retorno se pueden crear, liberar y revertir manualmente. Por ejemplo:

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

Además, EF Core se revierte ahora al último punto de retorno cuando se produce un error al ejecutar `SaveChanges`. Esto permite que se vuelva a intentar SaveChanges sin volver a probar toda la transacción.

El seguimiento de la documentación se realiza mediante la incidencia [n.º 2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).

## <a name="preview-6"></a>Versión preliminar 6

### <a name="split-queries-for-related-collections"></a>División de consultas para colecciones relacionadas

A partir de EF Core 3.0, EF Core siempre genera una única consulta SQL para cada consulta LINQ. Esto garantiza la coherencia de los datos devueltos de acuerdo con las restricciones del modo de transacción en uso. Sin embargo, esta operación puede ser muy lenta si la consulta utiliza `Include` o una proyección para devolver varias colecciones relacionadas.

Ahora, EF Core 5.0 permite dividir en varias consultas SQL una única consulta LINQ con colecciones relacionadas. Esto puede mejorar de forma significativa el rendimiento, pero puede dar lugar a incoherencias en los resultados devueltos si los datos de las dos consultas son diferentes. Las transacciones serializables o de instantáneas se pueden usar para mitigar esta situación y lograr la coherencia con las consultas divididas, pero esto puede conllevar otros costos de rendimiento y diferencias de comportamiento.

#### <a name="split-queries-with-include"></a>División de consultas con Include

Imaginemos, por ejemplo, que tenemos una consulta que extrae dos niveles de colecciones relacionadas mediante `Include`:

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

De forma predeterminada, EF Core generará el siguiente código SQL al usar el proveedor SQLite:

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

La nueva API `AsSplitQuery` se puede usar para cambiar este comportamiento. Por ejemplo:

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery está disponible para todos los proveedores de bases de datos relacionales y se puede usar en cualquier parte de la consulta, igual que AsNoTracking. EF Core generará ahora las tres consultas SQL siguientes:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

Se admiten todas las operaciones en la raíz de la consulta. Esto incluye las operaciones OrderBy, Skip, Take y Join, así como FirstOrDefault y operaciones de selección similares de resultado único.

Tenga en cuenta que Inclusión filtrada con OrderBy, Skip o Take no se admite en la versión preliminar 6, pero está disponible en las compilaciones diarias y se incluirá en la versión preliminar 7.

#### <a name="split-queries-with-collection-projections"></a>División de consultas con proyecciones de colecciones

`AsSplitQuery` también se puede usar cuando se carguen colecciones en proyecciones. Por ejemplo:

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

Esta consulta LINQ genera las dos consultas SQL siguientes cuando se usa el proveedor de SQLite:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

Tenga en cuenta que solo se admite la materialización de la colección. Las composiciones posteriores a `e.Albums` en el caso anterior no generarán consultas divididas. El número de seguimiento de las mejoras de esta área es el [21234](https://github.com/dotnet/efcore/issues/21234).

### <a name="indexattribute"></a>IndexAttribute

El nuevo IndexAttribute se puede colocar en un tipo de entidad a fin de especificar un índice para una sola columna. Por ejemplo:

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

Para SQL Server, las migraciones generarán el siguiente código SQL:

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

IndexAttribute también se puede usar para especificar un índice que abarque varias columnas. Por ejemplo:

```csharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

Para SQL Server, esto dará como resultado lo siguiente:

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

El seguimiento de la documentación se realiza mediante la incidencia [n.º 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="improved-query-translation-exceptions"></a>Mejora de las excepciones de traducción de las consultas

Seguimos mejorando los mensajes de excepción que se generan cuando se produce un error en la traducción de una consulta. Por ejemplo, esta consulta usa la propiedad no asignada `IsSigned`:

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core producirá la siguiente excepción que indica que se produjo un error de traducción porque no se asignó `IsSigned`:

> Excepción no controlada. System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.IsSigned)". Información adicional: Error al traducir el miembro "IsSigned" en el tipo de entidad "Artist". Es posible que el miembro especificado no se haya asignado. Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync(). Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.

Del mismo modo, ahora se generan mejores mensajes de excepción al intentar traducir comparaciones de cadenas con una semántica dependiente de la referencia cultural. Por ejemplo, esta consulta intenta usar `StringComparison.CurrentCulture`:

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

Ahora, EF Core producirá la siguiente excepción:

> Excepción no controlada. System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.Name.Equals(value: 'The Unicorns', comparisonType: CurrentCulture))". Información adicional: No se admite la traducción del método "string.Equals" que usa el argumento "StringComparison". Vea https://go.microsoft.com/fwlink/?linkid=2129535 para obtener más información. Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync(). Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.

### <a name="specify-transaction-id"></a>Especificación del id. de la transacción

El miembro de la comunidad [@Marusyk](https://github.com/Marusyk) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

EF Core expone un id. de transacción para la correlación de las transacciones entre llamadas. Este id. lo establece normalmente EF Core cuando se inicia una transacción. Si es la aplicación la que inicia la transacción, esta característica permite que la aplicación establezca de forma explícita el id. de transacción para que se ponga en correlación de forma correcta en todas las ubicaciones donde se use. Por ejemplo:

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>Asignación de IPAddress

El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

Ahora, la [clase estándar IPAddress](/dotnet/api/system.net.ipaddress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa. Por ejemplo, valore la posibilidad de asignar este tipo de entidad:

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

En SQL Server, esto hará que las migraciones creen la siguiente tabla:

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

Las entidades se pueden agregar de la manera habitual:

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

Y el código SQL resultante insertará la dirección IPv4 o IPv6 normalizada:

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>Exclusión de OnConfiguring al aplicar scaffolding

Cuando se aplica scaffolding a un elemento DbContext desde una base de datos existente, EF Core crea de forma predeterminada una sobrecarga de OnConfiguring con una cadena de conexión para que el contexto se pueda usar de inmediato. Sin embargo, esto no es útil si ya tiene una clase parcial con OnConfiguring o si está configurando el contexto de otra manera.

Para solucionar esto, ahora se puede indicar a los comandos de scaffolding que omitan la generación de OnConfiguring. Por ejemplo:

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

O bien, en la Consola del administrador de paquetes:

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

Tenga en cuenta que se recomienda usar [una cadena de conexión con nombre y almacenamiento seguro, como Secretos de usuario](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).

### <a name="translations-for-firstordefault-on-strings"></a>Traducciones de FirstOrDefault en las cadenas

El miembro de la comunidad [@dvoreckyaa](https://github.com/dvoreckyaa) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

Ahora se traducen FirstOrDefault y operadores similares para los caracteres de las cadenas. Por ejemplo, esta consulta LINQ:

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

Se traducirá al siguiente código SQL al usar SQL Server:

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>Simplificación de bloques de casos

Ahora, EF Core genera mejores consultas con bloques de casos. Por ejemplo, esta consulta LINQ:

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

Estaba traducida en SQL Server oficialmente de la siguiente manera:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

Pero ahora se traduce así:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a>Versión preliminar 5

### <a name="database-collations"></a>Intercalaciones de bases de datos

La intercalación predeterminada de una base de datos puede especificarse ahora en el modelo EF. Este pasará a las migraciones generadas para establecer la intercalación cuando se cree la base de datos. Por ejemplo:

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

A continuación, las migraciones generan lo siguiente para crear la base de datos en SQL Server:

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

También se puede especificar la intercalación que se va a usar para columnas de bases de datos concretas. Por ejemplo:

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

En el caso de las que no usan migraciones, se aplica a las intercalaciones ingeniería inversa desde la base de datos, al aplicar scaffolding a DbContext.

Por último, `EF.Functions.Collate()` permite realizar consultas ad-hoc mediante diferentes intercalaciones. Por ejemplo:

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

De este modo se generará la siguiente consulta para SQL Server:

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

Tenga en cuenta que las intercalaciones ad-hoc deben usarse con cuidado, ya que pueden afectar negativamente al rendimiento de la base de datos.

En el problema [n.º 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) se hace el seguimiento de la documentación.

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>Transmisión de argumentos a IDesignTimeDbContextFactory

Los argumentos se transmiten ahora desde la línea de comandos al método de `CreateDbContext` de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1). Por ejemplo, para indicar que se trata de una compilación de desarrollo, se puede pasar un argumento personalizado (por ejemplo, `dev`) en la línea de comandos:

```
dotnet ef migrations add two --verbose --dev
```

Este argumento pasará entonces a la factoría, donde se puede usar para controlar cómo se crea e inicializa el contexto. Por ejemplo:

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

En el problema [n.º 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) se hace el seguimiento de la documentación.

### <a name="no-tracking-queries-with-identity-resolution"></a>Consultas de no seguimiento con resolución de la identidad

Ahora se pueden configurar consultas de no seguimiento para hacer la resolución de identidad. Por ejemplo, la siguiente consulta creará una instancia de Blog para cada Post, aunque cada blog tenga la misma clave principal.

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Sin embargo, a costa de que por lo general sea ligeramente más lento y siempre use más memoria, se puede cambiar esta consulta para asegurarse de que solo se crea una instancia de Blog:

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Tenga en cuenta que esto solo es útil para consultas de no seguimiento, ya que todas las consultas de seguimiento ya muestran este comportamiento. Además, después de la revisión de la API, se cambiará la sintaxis de `PerformIdentityResolution`. Consulte el problema [n.º 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

En el problema [n.º 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) se realiza el seguimiento de la documentación.

### <a name="stored-persisted-computed-columns"></a>Columnas calculadas almacenadas (persistentes)

La mayoría de las bases de datos permiten almacenar valores de columnas calculadas después del cálculo. Aunque ocupa espacio en disco, la columna se calcula solo una vez al actualizar, en lugar de cada vez que se recupera su valor. Esto también permite indizar la columna para algunas bases de datos.

EF Core 5.0 permite configurar las columnas calculadas como almacenadas. Por ejemplo:

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>Columnas calculadas de SQLite

EF Core admite ahora columnas calculadas en las bases de datos de SQLite.

## <a name="preview-4"></a>Versión preliminar 4

### <a name="configure-database-precisionscale-in-model"></a>Configuración de la precisión y la escala de la base de datos en el modelo

Ahora se puede especificar la precisión y la escala de una propiedad mediante el generador de modelos. Por ejemplo:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

La precisión y la escala todavía se pueden establecer mediante el tipo completo de base de datos, como "decimal (16, 4)".

En el problema [n.º 527](https://github.com/dotnet/EntityFramework.Docs/issues/527) se realiza el seguimiento de la documentación.

### <a name="specify-sql-server-index-fill-factor"></a>Especificación del factor de relleno del índice de SQL Server

Ahora se puede especificar el factor de relleno al crear un índice en SQL Server. Por ejemplo:

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Versión preliminar 3

### <a name="filtered-include"></a>Inclusión filtrada

El método Include ahora admite el filtrado de las entidades incluidas. Por ejemplo:

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".

Para reducir el número de las entidades incluidas, se puede usar Skip y Take. Por ejemplo:

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
Esta consulta devolverá los blogs que tengan cinco publicaciones como máximo.

Vea la [documentación de Include](xref:core/querying/related-data#filtered-include) para obtener información completa.

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nueva API de ModelBuilder para las propiedades de navegación

Las propiedades de navegación se configuran principalmente al [definir relaciones](xref:core/modeling/relationships). No obstante, se puede usar el nuevo método `Navigation` cuando es necesario realizar una configuración adicional en las propiedades de navegación. Por ejemplo, para establecer un campo de respaldo de la navegación cuando el campo no se encuentre por convención:

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Tenga en cuenta que la API de `Navigation` no reemplaza la configuración de la relación, sino que permite la configuración adicional de las propiedades de navegación en relaciones ya detectadas o definidas.

Consulte la [documentación sobre la configuración de las propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nuevos parámetros de la línea de comandos para espacios de nombres y cadenas de conexión

Ahora, las migraciones y el scaffolding permiten especificar los espacios de nombres en la línea de comandos. Esto puede hacerse para, por ejemplo, aplicar ingeniería inversa a una base de datos y colocar las clases Context y Model en diferentes espacios de nombres:

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

Para más información, consulte la documentación sobre [migraciones](xref:core/managing-schemas/migrations/index#namespaces) e [ingeniería inversa](xref:core/managing-schemas/scaffolding#directories-and-namespaces).

---
Ahora, además, también puede pasarse una cadena de conexión al comando `database-update`:

```dotnetcli
dotnet ef database update --connection "connection string"
```

Para más información, consulte la [documentación sobre herramientas](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).

También se han agregado parámetros equivalentes a los comandos de PowerShell que se usan en la consola del administrador de paquetes de VS.

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors ha vuelto

Por motivos de rendimiento, EF no realiza comprobaciones de valores NULL adicionales cuando se leen los valores de la base de datos. Esto puede dar lugar a excepciones cuya causa principal es difícil de rastrear cuando se encuentra un valor NULL inesperado.

El uso de `EnableDetailedErrors` agregará una comprobación de valores NULL adicional a las consultas de modo que, para una pequeña sobrecarga de rendimiento, estos errores sean más fáciles de rastrear para averiguar su causa principal.

Por ejemplo:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

En el problema [n.º 955](https://github.com/dotnet/EntityFramework.Docs/issues/955) se realiza el seguimiento de la documentación.

### <a name="cosmos-partition-keys"></a>Claves de partición de Cosmos

Ahora, es posible especificar en las consultas la clave de partición que va a usarse para una consulta determinada. Por ejemplo:

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

En el problema [n.º 2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) se realiza el seguimiento de la documentación.

### <a name="support-for-the-sql-server-datalength-function"></a>Compatibilidad con la función DATALENGTH de SQL Server

Se puede acceder con el nuevo método `EF.Functions.DataLength`. Por ejemplo:

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Versión preliminar 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Uso de un atributo de C# para especificar un campo de respaldo de propiedad

Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad. Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente. Por ejemplo:

```csharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

En el problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) se realiza el seguimiento de la documentación.

### <a name="complete-discriminator-mapping"></a>Asignación completa de discriminador

EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](xref:core/modeling/inheritance). Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador. EF Core 5.0 ahora implementa estas mejoras.

Por ejemplo, las versiones anteriores de EF Core siempre generaban este SQL para una consulta que devuelve todos los tipos de una jerarquía:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5.0 ahora genera lo siguiente cuando se configura una asignación completa de discriminador:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

Este va a ser el comportamiento predeterminado a partir de la versión preliminar 3.

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Mejoras de rendimiento de Microsoft.Data.Sqlite

Se han realizado dos mejoras de rendimiento para SQLIte:

* La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.
* La inicialización de SqliteConnection ahora es diferida.

Estas mejoras se encuentran en el proveedor Microsoft.Data.Sqlite de ADO.NET y, por lo tanto, también mejoran el rendimiento fuera de EF Core.

## <a name="preview-1"></a>Versión preliminar 1

### <a name="simple-logging"></a>Registro sencillo

Esta característica agrega funcionalidad similar a `Database.Log` en EF6. Es decir, proporciona una manera sencilla de obtener registros de EF Core sin necesidad de configurar ningún tipo de plataforma de registro externa.

La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

En el problema [n.º 2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) se realiza el seguimiento de la documentación adicional.

### <a name="simple-way-to-get-generated-sql"></a>Forma sencilla de generar contenido SQL

EF Core 5.0 presenta el método de extensión `ToQueryString`, que devuelve el SQL que EF Core genera al ejecutar una consulta LINQ.

La documentación preliminar se incluye en el [estado semanal de EF del 9 de enero de 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).

En el problema [n.º 1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) se realiza el seguimiento de la documentación adicional.

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Uso de un atributo de C# para indicar que una entidad no tiene clave

Ahora se pueden configurar los tipos de entidad para indicar que no tienen clave mediante el nuevo valor `KeylessAttribute`. Por ejemplo:

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

En el problema [n.º 2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) se realiza el seguimiento de la documentación.

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>Posibilidad de cambiar la conexión o la cadena de conexión en una instancia inicializada de DbContext

Ahora es más fácil crear una instancia de DbContext sin ninguna conexión o cadena de conexión. Además, la conexión o la cadena de conexión ahora también se pueden mutar en la instancia de contexto. Esta característica permite que la misma instancia de contexto se conecte de forma dinámica a diferentes bases de datos.

En el problema [n.º 2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) se realiza el seguimiento de la documentación.

### <a name="change-tracking-proxies"></a>Proxies de seguimiento de cambios

Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) y [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged). A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios. Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.

En el problema [n.º 2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) se realiza el seguimiento de la documentación.

### <a name="enhanced-debug-views"></a>Vistas de depuración mejoradas

Las vistas de depuración son una forma fácil de consultar los aspectos internos de EF Core al depurar problemas. Hace algún tiempo ya se implementó una vista de depuración para el modelo. En el caso de EF Core 5.0, hemos facilitado la lectura de la vista de modelo y hemos agregado una nueva vista de depuración para las entidades de las que se ha realizado un seguimiento en el administrador de estado.

La documentación preliminar se incluye en el [estado semanal de EF del 12 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).

En el problema [n.º 2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) se realiza el seguimiento de la documentación adicional.

### <a name="improved-handling-of-database-null-semantics"></a>Control mejorado de la semántica de valores NULL de base de datos

Normalmente, las bases de datos relacionales tratan NULL como valores desconocidos y, por lo tanto, no son iguales a otros valores NULL. Mientras, C# trata NULL como un valor definido que se compara igual que cualquier otro valor NULL. De forma predeterminada, EF Core traduce las consultas para que usen la semántica de valores NULL de C#. EF Core 5.0 mejora en gran medida la eficacia de dichas traducciones.

En el problema [n.º 1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) se realiza el seguimiento de la documentación.

### <a name="indexer-properties"></a>Propiedades del indizador

EF Core 5.0 admite la asignación de propiedades de indizador de C#. Estas propiedades permiten a las entidades actuar como bolsas de propiedades en las que las columnas se asignan a propiedades con nombre de la bolsa.

En el problema [n.º 2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) se realiza el seguimiento de la documentación.

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Generación de restricciones CHECK para las asignaciones de enumeración

Las migraciones de EF Core 5.0 ahora pueden generar restricciones CHECK para las asignaciones de propiedades de enumeración. Por ejemplo:

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.

### <a name="isrelational"></a>IsRelational

Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`. Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales. Por ejemplo:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

En el problema [n.º 2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) se realiza el seguimiento de la documentación.

### <a name="cosmos-optimistic-concurrency-with-etags"></a>Simultaneidad optimista de Cosmos con mecanismos ETag

El proveedor de bases de datos de Azure Cosmos DB ya es compatible con la simultaneidad optimista mediante mecanismos ETag. Utilice el generador de modelos de OnModelCreating para confirmar un mecanismo ETag:

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](xref:core/saving/concurrency), por ejemplo, para implementar reintentos.

En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.

### <a name="query-translations-for-more-datetime-constructs"></a>Traducciones de consultas para más construcciones DateTime

Ahora las consultas que contienen la nueva construcción DateTime se traducen.

Además, ahora se asignan las funciones de SQL Server que hay a continuación:

* DateDiffWeek
* DateFromParts

Por ejemplo:

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.

### <a name="query-translations-for-more-byte-array-constructs"></a>Traducciones de consultas para más construcciones de matriz de bytes

Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.

La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.

### <a name="query-translation-for-reverse"></a>Traducción de consultas para Reverse

Las consultas que usan `Reverse` ahora se traducen. Por ejemplo:

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.

### <a name="query-translation-for-bitwise-operators"></a>Traducción de consultas para operadores bit a bit

Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.

### <a name="query-translation-for-strings-on-cosmos"></a>Traducción de consultas para cadenas en Cosmos

Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.
