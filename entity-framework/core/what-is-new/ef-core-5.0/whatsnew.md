---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 7737a143b9478412cdafe3c2fe5e8d7a106c9a3f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635502"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="7c1f3-103">Novedades en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="7c1f3-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="7c1f3-104">Ya se han completado todas las características planeadas para EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-104">All features planned for EF Core 5.0 have now been completed.</span></span> <span data-ttu-id="7c1f3-105">Esta página contiene información general sobre los cambios interesantes introducidos en cada versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-105">This page contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="7c1f3-106">Esta página no duplica el [plan para EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="7c1f3-107">En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="rc1"></a><span data-ttu-id="7c1f3-108">RC1</span><span class="sxs-lookup"><span data-stu-id="7c1f3-108">RC1</span></span>

### <a name="many-to-many"></a><span data-ttu-id="7c1f3-109">Varios a varios</span><span class="sxs-lookup"><span data-stu-id="7c1f3-109">Many-to-many</span></span>

<span data-ttu-id="7c1f3-110">EF Core 5.0 admite relaciones de varios a varios sin asignar explícitamente la tabla de combinación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-110">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="7c1f3-111">Por ejemplo, considere estos tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-111">For example, consider these entity types:</span></span>

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

<span data-ttu-id="7c1f3-112">Observe que `Post` contiene una colección de `Tags` y `Tag` contiene una colección de `Posts`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-112">Notice that `Post` contains a collection of `Tags`, and `Tag` contains a collection of `Posts`.</span></span> <span data-ttu-id="7c1f3-113">EF Core 5.0 reconoce esto como una relación de varios a varios por convención.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-113">EF Core 5.0 recognizes this as a many-to-many relationship by convention.</span></span> <span data-ttu-id="7c1f3-114">Esto significa que no se requiere ningún código en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-114">This means no code is required in `OnModelCreating`:</span></span>

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
}
```

<span data-ttu-id="7c1f3-115">Cuando se usan migraciones (o `EnsureCreated`) para crear la base de datos, EF Core creará automáticamente la tabla de combinación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-115">When Migrations (or `EnsureCreated`) are used to create the database, EF Core will automatically create the join table.</span></span> <span data-ttu-id="7c1f3-116">Por ejemplo, en SQL Server para este modelo, EF Core genera:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-116">For example, on SQL Server for this model, EF Core generates:</span></span>

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

<span data-ttu-id="7c1f3-117">Al crear y asociar entidades `Tag` y `Post`, se producen automáticamente actualizaciones de la tabla de combinación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-117">Creating and associating `Tag` and `Post` entities results in join table updates happening automatically.</span></span> <span data-ttu-id="7c1f3-118">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-118">For example:</span></span>

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

<span data-ttu-id="7c1f3-119">Después de insertar las entradas y etiquetas, EF creará automáticamente las filas en la tabla de combinación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-119">After inserting the Posts and Tags, EF will then automatically create rows in the join table.</span></span> <span data-ttu-id="7c1f3-120">Por ejemplo, en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-120">For example, on SQL Server:</span></span>

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

<span data-ttu-id="7c1f3-121">En el caso de las consultas, Include y otras operaciones de consulta funcionan igual que para cualquier otra relación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-121">For queries, Include and other query operations work just like for any other relationship.</span></span> <span data-ttu-id="7c1f3-122">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-122">For example:</span></span>

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

<span data-ttu-id="7c1f3-123">El SQL generado utiliza la tabla de combinación automáticamente para devolver todas las etiquetas relacionadas:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-123">The SQL generated uses the join table automatically to bring back all related Tags:</span></span>

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

<span data-ttu-id="7c1f3-124">A diferencia de EF6, EF Core permite la personalización completa de la tabla de combinación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-124">Unlike EF6, EF Core allows full customization of the join table.</span></span> <span data-ttu-id="7c1f3-125">Por ejemplo, el código siguiente configura una relación de varios a varios que también tiene navegaciones a la entidad de combinación y en la que la entidad de combinación contiene una propiedad de carga:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-125">For example, the code below configures a many-to-many relationship that also has navigations to the join entity, and in which the join entity contains a payload property:</span></span>

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

> [!NOTE]
> <span data-ttu-id="7c1f3-126">Todavía no se ha agregado compatibilidad con la aplicación de scaffolding a relaciones de varios a varios desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-126">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="7c1f3-127">Vea la [incidencia de seguimiento](https://github.com/dotnet/efcore/issues/22475).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-127">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

### <a name="map-entity-types-to-queries"></a><span data-ttu-id="7c1f3-128">Asignación de tipos de entidad a consultas</span><span class="sxs-lookup"><span data-stu-id="7c1f3-128">Map entity types to queries</span></span>

<span data-ttu-id="7c1f3-129">Normalmente, los tipos de entidad se asignan a tablas o vistas, de modo que EF Core extraerá el contenido de la tabla o vista al consultar ese tipo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-129">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="7c1f3-130">EF Core 5.0 permite asignar un tipo de entidad a una "consulta de definición".</span><span class="sxs-lookup"><span data-stu-id="7c1f3-130">EF Core 5.0 allows an entity type to mapped to a "defining query".</span></span> <span data-ttu-id="7c1f3-131">(Esto se admitía parcialmente en versiones anteriores, pero se ha mejorado mucho y tiene otra sintaxis en EF Core 5.0).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-131">(This was partially supported in previous versions, but is much improved and has different syntax in EF Core 5.0.)</span></span>

<span data-ttu-id="7c1f3-132">Por ejemplo, considere dos tablas: una con publicaciones modernas, la otra con publicaciones heredadas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-132">For example, consider two tables; one with modern posts; the other with legacy posts.</span></span> <span data-ttu-id="7c1f3-133">La tabla de publicaciones modernas tiene algunas columnas adicionales, pero a efectos de nuestra aplicación queremos combinar las publicaciones modernas y las heredadas y asignarlas a un tipo de entidad con todas las propiedades necesarias:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-133">The modern posts table has some additional columns, but for the purpose of our application we want both modern and legacy posts to be combined and mapped to an entity type with all necessary properties:</span></span>

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

<span data-ttu-id="7c1f3-134">En EF Core 5.0, se puede usar `ToSqlQuery` para asignar este tipo de entidad a una consulta que extrae y combina filas de ambas tablas:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-134">In EF Core 5.0, `ToSqlQuery` can be used to map this entity type to a query that pulls and combines rows from both tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

<span data-ttu-id="7c1f3-135">Observe que la tabla `legacy_posts` no tiene una columna `Category`, por lo que en su lugar sintetizamos un valor predeterminado para todas las publicaciones heredadas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-135">Notice that the `legacy_posts` table does not have a `Category` column, so we instead synthesize a default value for all legacy posts.</span></span>

<span data-ttu-id="7c1f3-136">Este tipo de entidad se puede usar de la manera normal para las consultas LINQ.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-136">This entity type can then be used in the normal way for LINQ queries.</span></span> <span data-ttu-id="7c1f3-137">Por ejemplo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-137">For example.</span></span> <span data-ttu-id="7c1f3-138">La consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-138">the LINQ query:</span></span>

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

<span data-ttu-id="7c1f3-139">Genera el siguiente código SQL en SQLite:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-139">Generates the following SQL on SQLite:</span></span>

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

<span data-ttu-id="7c1f3-140">Observe que la consulta configurada para el tipo de entidad se usa como elemento inicial para componer la consulta LINQ completa.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-140">Notice that the query configured for the entity type is used as a starting for composing the full LINQ query.</span></span>

### <a name="event-counters"></a><span data-ttu-id="7c1f3-141">Contadores de eventos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-141">Event counters</span></span>

<span data-ttu-id="7c1f3-142">[Los contadores de eventos de .NET](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) son una manera eficaz de exponer las métricas de rendimiento de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-142">[.NET event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) are a way to efficiently expose performance metrics from an application.</span></span> <span data-ttu-id="7c1f3-143">EF Core 5.0 incluye contadores de eventos en la categoría `Microsoft.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-143">EF Core 5.0 includes event counters under the `Microsoft.EntityFrameworkCore` category.</span></span> <span data-ttu-id="7c1f3-144">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-144">For example:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

<span data-ttu-id="7c1f3-145">Esto indica a los contadores de dotnet que empiecen a recopilar eventos de EF Core para el proceso 49496.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-145">This tells dotnet counters to start collecting EF Core events for process 49496.</span></span> <span data-ttu-id="7c1f3-146">Esto genera una salida similar a la siguiente en la consola:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-146">This generates output like this in the console:</span></span>

```console
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

### <a name="property-bags"></a><span data-ttu-id="7c1f3-147">Contenedores de propiedades</span><span class="sxs-lookup"><span data-stu-id="7c1f3-147">Property bags</span></span>

<span data-ttu-id="7c1f3-148">EF Core 5.0 permite asignar el mismo tipo de CLR a varios tipos distintos de entidad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-148">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types.</span></span> <span data-ttu-id="7c1f3-149">Estos tipos se conocen como tipos de entidad de tipo compartido.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-149">Such types are known as shared-type entity types.</span></span> <span data-ttu-id="7c1f3-150">Esta característica combinada con las propiedades del indexador (incluidas en la versión preliminar 1) permite usar contenedores de propiedades como tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-150">This feature combined with indexer properties (included in preview 1) allows property bags to be used as entity type.</span></span>

<span data-ttu-id="7c1f3-151">Por ejemplo, el elemento DbContext siguiente configura el tipo de BCL `Dictionary<string, object>` como tipo de entidad de tipo compartido para productos y categorías.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-151">For example, the DbContext below configures the BCL type `Dictionary<string, object>` as a shared-type entity type for both products and categories.</span></span>

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

<span data-ttu-id="7c1f3-152">Ahora se pueden agregar objetos de diccionario ("contenedores de propiedades") al contexto como instancias de entidad y guardarse.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-152">Dictionary objects ("property bags") can now be added to the context as entity instances and saved.</span></span> <span data-ttu-id="7c1f3-153">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-153">For example:</span></span>

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

<span data-ttu-id="7c1f3-154">Estas entidades se pueden consultar y actualizar de la manera normal:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-154">These entities can then be queried and updated in the normal way:</span></span>

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a><span data-ttu-id="7c1f3-155">Interceptación y eventos de SaveChanges</span><span class="sxs-lookup"><span data-stu-id="7c1f3-155">SaveChanges interception and events</span></span>

<span data-ttu-id="7c1f3-156">EF Core 5.0 introduce tanto eventos de .NET como un interceptor EF Core que se desencadena al llamar a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-156">EF Core 5.0 introduces both .NET events and an EF Core interceptor triggered when SaveChanges is called.</span></span>

<span data-ttu-id="7c1f3-157">Los eventos son sencillos de usar; por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-157">The events are simple to use; for example:</span></span>

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

<span data-ttu-id="7c1f3-158">Tenga en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-158">Notice that:</span></span>

* <span data-ttu-id="7c1f3-159">El remitente del evento es la instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-159">The event sender is the `DbContext` instance</span></span>
* <span data-ttu-id="7c1f3-160">El elemento args del evento `SavedChanges` contiene el número de entidades guardadas en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-160">The args for the `SavedChanges` event contains the number of entities saved to the database</span></span>

<span data-ttu-id="7c1f3-161">`ISaveChangesInterceptor` define el interceptor, pero a menudo es conveniente que se herede de `SaveChangesInterceptor` para evitar la implementación de todos los métodos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-161">The interceptor is defined by `ISaveChangesInterceptor`, but it is often convenient to inherit from `SaveChangesInterceptor` to avoid implementing every method.</span></span> <span data-ttu-id="7c1f3-162">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-162">For example:</span></span>

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

<span data-ttu-id="7c1f3-163">Tenga en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-163">Notice that:</span></span>

* <span data-ttu-id="7c1f3-164">El interceptor tiene métodos sincrónicos y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-164">The interceptor has both sync and async methods.</span></span> <span data-ttu-id="7c1f3-165">Esto puede ser útil si tiene que realizar operaciones de E/S asincrónicas, como escribir en un servidor de auditoría.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-165">This can be useful if you need to perform async I/O, such as writing to an audit server.</span></span>
* <span data-ttu-id="7c1f3-166">El interceptor permite omitir SaveChanges mediante el mecanismo `InterceptionResult` común a todos los interceptores.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-166">The interceptor allows SaveChanges to be skipped using the `InterceptionResult` mechanism common to all interceptors.</span></span>

<span data-ttu-id="7c1f3-167">El inconveniente de los interceptores es que deben registrarse en DbContext cuando se construyen.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-167">The downside of interceptors is that they must be registered on the DbContext when it is being constructed.</span></span> <span data-ttu-id="7c1f3-168">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-168">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

<span data-ttu-id="7c1f3-169">En cambio, los eventos se pueden registrar en la instancia de DbContext en cualquier momento.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-169">In contrast, the events can be registered on the DbContext instance at any time.</span></span>

### <a name="exclude-tables-from-migrations"></a><span data-ttu-id="7c1f3-170">Exclusión de tablas de migraciones</span><span class="sxs-lookup"><span data-stu-id="7c1f3-170">Exclude tables from migrations</span></span>

<span data-ttu-id="7c1f3-171">A veces resulta útil tener un solo tipo de entidad asignado en varios elementos DbContext.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-171">It is sometimes useful to have a single entity type mapped in multiple DbContexts.</span></span> <span data-ttu-id="7c1f3-172">Esto es especialmente cierto cuando se usan [contextos delimitados](https://www.martinfowler.com/bliki/BoundedContext.html), para los que es común tener un tipo DbContext diferente para cada contexto enlazado.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-172">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different DbContext type for each bounded context.</span></span>

<span data-ttu-id="7c1f3-173">Por ejemplo, es posible que un contexto de autorización y un contexto de informes necesiten un tipo `User`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-173">For example, a `User` type may be needed by both an authorization context and a reporting context.</span></span> <span data-ttu-id="7c1f3-174">Si se realiza un cambio en el tipo `User`, las migraciones de ambos elementos DbContext intentarán actualizar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-174">If a change is made to the `User` type, then migrations for both DbContexts will attempt to update the database.</span></span> <span data-ttu-id="7c1f3-175">Para evitar esto, el modelo de uno de los contextos se puede configurar para excluir la tabla de sus migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-175">To prevent this, the model for one of the contexts can be configured to exclude the table from its migrations.</span></span>

<span data-ttu-id="7c1f3-176">En el código siguiente, el `AuthorizationContext` generará migraciones para los cambios en la tabla `Users`, pero el `ReportingContext` no lo hará, lo que impedirá que las migraciones entren en conflicto.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-176">In the code below, the `AuthorizationContext` will generate migrations for changes to the `Users` table, but the `ReportingContext` will not, preventing the migrations from clashing.</span></span>

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

### <a name="required-11-dependents"></a><span data-ttu-id="7c1f3-177">Dependientes de 1:1 requeridos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-177">Required 1:1 dependents</span></span>

<span data-ttu-id="7c1f3-178">En EF Core 3.1, el extremo dependiente de una relación uno a uno se consideraba siempre opcional.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-178">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="7c1f3-179">Esto era más que evidente al usar entidades en propiedad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-179">This was most apparent when using owned entities.</span></span> <span data-ttu-id="7c1f3-180">Por ejemplo, considere el siguiente modelo y configuración:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-180">For example, consider the following model and configuration:</span></span>

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

<span data-ttu-id="7c1f3-181">Esto hace que las migraciones creen la siguiente tabla para SQLite:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-181">This results in Migrations creating the following table for SQLite:</span></span>

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

<span data-ttu-id="7c1f3-182">Tenga en cuenta que todas las columnas aceptan valores NULL, aunque algunas de las propiedades de `HomeAddress` se han configurado según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-182">Notice that all the columns are nullable, even though some of the `HomeAddress` properties have been configured as required.</span></span> <span data-ttu-id="7c1f3-183">Además, al consultar un `Person`, si todas las columnas de la dirección particular o de trabajo son NULL, EF Core dejará las propiedades `HomeAddress` o `WorkAddress` como NULL, en lugar de establecer una instancia vacía de `Address`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-183">Also, when querying for a `Person`, if all the columns for either the home or work address are null, then EF Core will leave the `HomeAddress` and/or `WorkAddress` properties as null, rather than setting an empty instance of `Address`.</span></span>

<span data-ttu-id="7c1f3-184">En EF Core 5.0, la navegación de `HomeAddress` se puede configurar ahora como elemento dependiente necesario.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-184">In EF Core 5.0, the `HomeAddress` navigation can now be configured as as a required dependent.</span></span> <span data-ttu-id="7c1f3-185">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-185">For example:</span></span>

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

<span data-ttu-id="7c1f3-186">La tabla creada por las migraciones incluirá ahora columnas que no aceptan valores NULL para las propiedades necesarias del elemento dependiente necesario:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-186">The table created by Migrations will now included non-nullable columns for the required properties of the required dependent:</span></span>

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

<span data-ttu-id="7c1f3-187">Además, EF Core iniciará ahora una excepción si se realiza un intento de guardar un propietario que tiene un elemento dependiente necesario NULL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-187">In addition, EF Core will now throw an exception if an attempt is made to save an owner which has a null required dependent.</span></span> <span data-ttu-id="7c1f3-188">En este ejemplo, EF Core producirá una excepción al intentar guardar un `Person` con un `HomeAddress` NULL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-188">In this example, EF Core will throw when attempting to save a `Person` with a null `HomeAddress`.</span></span>

<span data-ttu-id="7c1f3-189">Por último, EF Core creará una instancia de un elemento dependiente necesario aunque todas las columnas del elemento dependiente necesario tengan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-189">Finally, EF Core will still create an instance of a required dependent even when all the columns for the required dependent have null values.</span></span>

### <a name="options-for-migration-generation"></a><span data-ttu-id="7c1f3-190">Opciones para la generación de migraciones</span><span class="sxs-lookup"><span data-stu-id="7c1f3-190">Options for migration generation</span></span>

<span data-ttu-id="7c1f3-191">EF Core 5.0 introduce un mayor control sobre la generación de migraciones con distintos fines.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-191">EF Core 5.0 introduces greater control over generation of migrations for different purposes.</span></span> <span data-ttu-id="7c1f3-192">Esto incluye la posibilidad de:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-192">This includes the ability to:</span></span>

* <span data-ttu-id="7c1f3-193">Saber si la migración se genera para un script o para su inmediata ejecución</span><span class="sxs-lookup"><span data-stu-id="7c1f3-193">Know if the migration is being generated for a script or for immediate execution</span></span>
* <span data-ttu-id="7c1f3-194">Saber si se está generando un script idempotente</span><span class="sxs-lookup"><span data-stu-id="7c1f3-194">Know if an idempotent script is being generated</span></span>
* <span data-ttu-id="7c1f3-195">Saber si el script debe excluir las instrucciones de transacción (consulte _Scripts de migración con transacciones_ a continuación).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-195">Know if the script should exclude transaction statements (See _Migrations scripts with transactions_ below.)</span></span>

<span data-ttu-id="7c1f3-196">Este comportamiento se especifica mediante una enumeración `MigrationsSqlGenerationOptions`, que ahora se puede pasar a `IMigrator.GenerateScript`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-196">This behavior is specified by an the `MigrationsSqlGenerationOptions` enum, which can now be passed to `IMigrator.GenerateScript`.</span></span>

<span data-ttu-id="7c1f3-197">También se incluye en este trabajo una mejor generación de scripts idempotentes con llamadas a `EXEC` en SQL Server cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-197">Also included in this work is better generation of idempotent scripts with calls to `EXEC` on SQL Server when needed.</span></span> <span data-ttu-id="7c1f3-198">Este trabajo también habilita mejoras similares a los scripts generados por otros proveedores de bases de datos, incluido PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-198">This work also enables similar improvements to the scripts generated by other database providers, including PostgreSQL.</span></span>

### <a name="migrations-scripts-with-transactions"></a><span data-ttu-id="7c1f3-199">Scripts de migración con transacciones</span><span class="sxs-lookup"><span data-stu-id="7c1f3-199">Migrations scripts with transactions</span></span>

<span data-ttu-id="7c1f3-200">Los scripts SQL generados a partir de migraciones ahora contienen instrucciones para iniciar y confirmar las transacciones según sea necesario para la migración.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-200">SQL scripts generated from migrations now contain statements to begin and commit transactions as appropriate for the migration.</span></span> <span data-ttu-id="7c1f3-201">Por ejemplo, el script de migración siguiente se generó a partir de dos migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-201">For example, the migration script below was generated from two migrations.</span></span> <span data-ttu-id="7c1f3-202">Observe que cada migración se aplica ahora en una transacción.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-202">Notice that each migration is now applied inside a transaction.</span></span>

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

<span data-ttu-id="7c1f3-203">Tal y como se mencionó en la sección anterior, se puede deshabilitar este uso de las transacciones si es necesario controlar las transacciones de otra forma.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-203">As mentioned in the previous section, this use of transactions can be disabled if transactions need to be handled differently.</span></span>

### <a name="see-pending-migrations"></a><span data-ttu-id="7c1f3-204">Consulta de migraciones pendientes</span><span class="sxs-lookup"><span data-stu-id="7c1f3-204">See pending migrations</span></span>

<span data-ttu-id="7c1f3-205">El miembro de la comunidad [@Psypher9](https://github.com/Psypher9) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-205">This feature was contributed from the community by [@Psypher9](https://github.com/Psypher9).</span></span> <span data-ttu-id="7c1f3-206">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-206">Many thanks for the contribution!</span></span>

<span data-ttu-id="7c1f3-207">El comando `dotnet ef migrations list` muestra ahora las migraciones que todavía no se han aplicado a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-207">The `dotnet ef migrations list` command now shows which migrations have not yet been applied to the database.</span></span> <span data-ttu-id="7c1f3-208">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-208">For example:</span></span>

```console
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

<span data-ttu-id="7c1f3-209">Además, ahora hay un comando `Get-Migration` para la consola del administrador de paquetes con la misma funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-209">In addition, there is now a `Get-Migration` command for the Package Manager Console with the same functionality.</span></span>

### <a name="modelbuilder-api-for-value-comparers"></a><span data-ttu-id="7c1f3-210">API ModelBuilder para comparadores de valores</span><span class="sxs-lookup"><span data-stu-id="7c1f3-210">ModelBuilder API for value comparers</span></span>

<span data-ttu-id="7c1f3-211">Las propiedades de EF Core para tipos mutables personalizados [requieren un comparador de valores](xref:core/modeling/value-comparers) para que los cambios de propiedad se detecten correctamente.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-211">EF Core properties for custom mutable types [require a value comparer](xref:core/modeling/value-comparers) for property changes to be detected correctly.</span></span> <span data-ttu-id="7c1f3-212">Ahora se pueden especificar como parte de la configuración de la conversión de valor para el tipo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-212">This can now be specified as part of configuring the value conversion for the type.</span></span> <span data-ttu-id="7c1f3-213">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-213">For example:</span></span>

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

### <a name="entityentry-trygetvalue-methods"></a><span data-ttu-id="7c1f3-214">Métodos TryGetValue de EntityEntry</span><span class="sxs-lookup"><span data-stu-id="7c1f3-214">EntityEntry TryGetValue methods</span></span>

<span data-ttu-id="7c1f3-215">El miembro de la comunidad [@m4ss1m0g](https://github.com/m4ss1m0g) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-215">This feature was contributed from the community by [@m4ss1m0g](https://github.com/m4ss1m0g).</span></span> <span data-ttu-id="7c1f3-216">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-216">Many thanks for the contribution!</span></span>

<span data-ttu-id="7c1f3-217">Se ha agregado un método `TryGetValue` a `EntityEntry.CurrentValues` y `EntityEntry.OriginalValues`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-217">A `TryGetValue` method has been added to `EntityEntry.CurrentValues` and `EntityEntry.OriginalValues`.</span></span> <span data-ttu-id="7c1f3-218">Esto permite solicitar el valor de una propiedad sin comprobar primero si la propiedad está asignada en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-218">This allows the value of a property to be requested without first checking if the property is mapped in the EF model.</span></span> <span data-ttu-id="7c1f3-219">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-219">For example:</span></span>

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a><span data-ttu-id="7c1f3-220">Tamaño de lote máximo predeterminado para SQL Server</span><span class="sxs-lookup"><span data-stu-id="7c1f3-220">Default max batch size for SQL Server</span></span>

<span data-ttu-id="7c1f3-221">A partir de EF Core 5.0, el tamaño máximo predeterminado del lote para SaveChanges en SQL Server es ahora 42.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-221">Starting with EF Core 5.0, the default maximum batch size for SaveChanges on SQL Server is now 42.</span></span> <span data-ttu-id="7c1f3-222">Como es bien sabido, esta es también la respuesta a la última pregunta de la vida, el universo y todo lo demás.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-222">As is well known, this is also the answer to the Ultimate Question of Life, the Universe, and Everything.</span></span> <span data-ttu-id="7c1f3-223">Sin embargo, es probable que se trate de una coincidencia, ya que el valor se obtuvo a través de [análisis del rendimiento del procesamiento por lotes](https://github.com/dotnet/efcore/issues/9270).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-223">However, this is probably a coincidence, since the value was obtained through [analysis of batching performance](https://github.com/dotnet/efcore/issues/9270).</span></span> <span data-ttu-id="7c1f3-224">No creemos que hayamos descubierto una forma de la última pregunta, aunque parece algo plausible que la tierra se haya creado para comprender por qué SQL Server funciona de la forma en que lo hace.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-224">We do not believe that we have discovered a form of the Ultimate Question, although it does seem somewhat plausible that the Earth was created to understand why SQL Server works the way it does.</span></span>

### <a name="default-environment-to-development"></a><span data-ttu-id="7c1f3-225">Entorno predeterminado en desarrollo</span><span class="sxs-lookup"><span data-stu-id="7c1f3-225">Default environment to Development</span></span>

<span data-ttu-id="7c1f3-226">Las herramientas de línea de comandos de EF Core ahora configuran automáticamente las variables de entorno `ASPNETCORE_ENVIRONMENT` _y_ `DOTNET_ENVIRONMENT` en "desarrollo".</span><span class="sxs-lookup"><span data-stu-id="7c1f3-226">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="7c1f3-227">Esto aporta la experiencia al usar el host genérico en línea con la experiencia de ASP.NET Core durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-227">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span> <span data-ttu-id="7c1f3-228">Consulte [n.º 19903](https://github.com/dotnet/efcore/issues/19903).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-228">See [#19903](https://github.com/dotnet/efcore/issues/19903).</span></span>

### <a name="better-migrations-column-ordering"></a><span data-ttu-id="7c1f3-229">Mejor ordenación de las columnas de migraciones</span><span class="sxs-lookup"><span data-stu-id="7c1f3-229">Better migrations column ordering</span></span>

<span data-ttu-id="7c1f3-230">Las columnas de las clases base no asignadas se ordenan ahora después de otras columnas para los tipos de entidad asignados.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-230">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="7c1f3-231">Tenga en cuenta que esto solo afecta a las tablas recién creadas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-231">Note this only impacts newly created tables.</span></span> <span data-ttu-id="7c1f3-232">El orden de las columnas de las tablas existentes permanece sin cambios.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-232">The column order for existing tables remains unchanged.</span></span> <span data-ttu-id="7c1f3-233">Consulte [n.º 11314](https://github.com/dotnet/efcore/issues/11314).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-233">See [#11314](https://github.com/dotnet/efcore/issues/11314).</span></span>

### <a name="query-improvements"></a><span data-ttu-id="7c1f3-234">Mejoras en las consultas</span><span class="sxs-lookup"><span data-stu-id="7c1f3-234">Query improvements</span></span>

<span data-ttu-id="7c1f3-235">EF Core 5.0 RC1 contiene algunas mejoras adicionales en la traducción de consultas:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-235">EF Core 5.0 RC1 contains some additional query translation improvements:</span></span>

* <span data-ttu-id="7c1f3-236">Traducción de `is` en Cosmos: consultar [n.º 16391](https://github.com/dotnet/efcore/issues/16391)</span><span class="sxs-lookup"><span data-stu-id="7c1f3-236">Translation of `is` on Cosmos--see [#16391](https://github.com/dotnet/efcore/issues/16391)</span></span>
* <span data-ttu-id="7c1f3-237">Ahora se pueden anotar funciones asignadas por el usuario para controlar la propagación de valores NULL: consultar [n.º 19609](https://github.com/dotnet/efcore/issues/19609)</span><span class="sxs-lookup"><span data-stu-id="7c1f3-237">User-mapped functions can now be annotated to control null propagation--see [#19609](https://github.com/dotnet/efcore/issues/19609)</span></span>
* <span data-ttu-id="7c1f3-238">Compatibilidad con la traducción de GroupBy con agregados condicionales: consultar [n.º 11711](https://github.com/dotnet/efcore/issues/11711)</span><span class="sxs-lookup"><span data-stu-id="7c1f3-238">Support for translation of GroupBy with conditional aggregates--see [#11711](https://github.com/dotnet/efcore/issues/11711)</span></span>
* <span data-ttu-id="7c1f3-239">Traducción del operador DISTINCT en el elemento de grupo antes del agregado: consultar [n.º 17376](https://github.com/dotnet/efcore/issues/17376)</span><span class="sxs-lookup"><span data-stu-id="7c1f3-239">Translation of Distinct operator over group element before aggregate--see [#17376](https://github.com/dotnet/efcore/issues/17376)</span></span>

### <a name="model-building-for-fields"></a><span data-ttu-id="7c1f3-240">Creación de modelos para campos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-240">Model building for fields</span></span>

<span data-ttu-id="7c1f3-241">Por último, para RC1, EF Core ahora permite el uso de los métodos lambda en ModelBuilder para los campos y las propiedades.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-241">Finally for RC1, EF Core now allows use of the lambda methods in the ModelBuilder for fields as well as properties.</span></span> <span data-ttu-id="7c1f3-242">Por ejemplo, si por alguna razón es reacio a las propiedades y decide usar campos públicos, estos campos ahora se pueden asignar mediante los generadores de lambda:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-242">For example, if you are averse to properties for some reason and decide to use public fields, then these fields can now be mapped using the lambda builders:</span></span>

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

<span data-ttu-id="7c1f3-243">Aunque esto ahora es posible, no se recomienda hacerlo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-243">While this is now possible, we are certainly not recommending that you do this.</span></span> <span data-ttu-id="7c1f3-244">Además, tenga en cuenta que esto no agrega ninguna funcionalidad de asignación de campos adicional a EF Core, solo permite usar los métodos lambda en lugar de requerir siempre los métodos de cadena.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-244">Also, note that this does not add any additional field mapping capabilities to EF Core, it only allows the lambda methods to be used instead of always requiring the string methods.</span></span> <span data-ttu-id="7c1f3-245">Esto no resulta muy útil, ya que los campos no suelen ser públicos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-245">This is seldom useful since fields are rarely public.</span></span>

## <a name="preview-8"></a><span data-ttu-id="7c1f3-246">Versión preliminar 8</span><span class="sxs-lookup"><span data-stu-id="7c1f3-246">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="7c1f3-247">Asignación de tabla por tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="7c1f3-247">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="7c1f3-248">De forma predeterminada, EF Core asigna una jerarquía de herencia de tipos .NET a una tabla de base de datos única.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-248">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="7c1f3-249">Esto se conoce como asignación de tabla por jerarquía (TPH).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-249">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="7c1f3-250">EF Core 5.0 también permite asignar cada tipo .NET de una jerarquía de herencia a una tabla de base de datos diferente; esto se conoce como asignación de tabla por tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-250">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="7c1f3-251">Por ejemplo, piense en este modelo con una jerarquía asignada:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-251">For example, consider this model with a mapped hierarchy:</span></span>

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

<span data-ttu-id="7c1f3-252">De forma predeterminada, EF Core lo asignará a una tabla única:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-252">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EducationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="7c1f3-253">Sin embargo, la asignación de cada tipo de entidad a una tabla diferente dará como resultado en su lugar una tabla por tipo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-253">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

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
    [EducationLevel] nvarchar(max) NULL,
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

<span data-ttu-id="7c1f3-254">Tenga en cuenta que la creación de las restricciones de clave externa mostradas anteriormente se han agregado después de bifurcar el código para la versión preliminar 8.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-254">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="7c1f3-255">Los tipos de entidad pueden asignarse a tablas diferentes mediante atributos de asignación:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-255">Entity types can be mapped to different tables using mapping attributes:</span></span>

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
    public string EducationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="7c1f3-256">O mediante la configuración `ModelBuilder`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-256">Or using `ModelBuilder` configuration:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="7c1f3-257">El seguimiento de la documentación se realiza mediante el número de incidencia [1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-257">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="7c1f3-258">Migraciones: recompilación de tablas de SQLite</span><span class="sxs-lookup"><span data-stu-id="7c1f3-258">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="7c1f3-259">En comparación con otras bases de datos, SQLite está relativamente limitada en sus capacidades de manipulación de esquemas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-259">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="7c1f3-260">Por ejemplo, quitar una columna de una tabla existente requiere quitar y volver a crear la tabla completa.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-260">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="7c1f3-261">Las migraciones de EF Core 5.0 ahora admiten la recompilación automática de la tabla para los cambios de esquema que la necesiten.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-261">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="7c1f3-262">Por ejemplo, imagine que tenemos una tabla `Unicorns` creada para un tipo de entidad `Unicorn`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-262">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

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

<span data-ttu-id="7c1f3-263">Posteriormente, veremos que el almacenamiento de la edad de un unicornio se considera muy superficial, así que vamos a quitar esa propiedad, a agregar una nueva migración y a actualizar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-263">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="7c1f3-264">Esta actualización producirá un error al usar EF Core 3.1 porque no se puede quitar la columna.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-264">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="7c1f3-265">En EF Core 5.0, en lugar de eso, las migraciones recompilarán la tabla:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-265">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

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

<span data-ttu-id="7c1f3-266">Tenga en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-266">Notice that:</span></span>

* <span data-ttu-id="7c1f3-267">Se crea una tabla temporal con el esquema deseado para la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-267">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="7c1f3-268">Los datos se copian de la tabla actual en la tabla temporal.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-268">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="7c1f3-269">La aplicación de clave externa está desactivada.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-269">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="7c1f3-270">Se quita la tabla actual.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-270">The current table is dropped</span></span>
* <span data-ttu-id="7c1f3-271">Se cambia el nombre de la tabla temporal para hacerla la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-271">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="7c1f3-272">El seguimiento de la documentación se realiza mediante el número de incidencia [2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-272">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="7c1f3-273">Funciones con valores de tabla</span><span class="sxs-lookup"><span data-stu-id="7c1f3-273">Table-valued functions</span></span>

<span data-ttu-id="7c1f3-274">El miembro de la comunidad [@pmiddleton](https://github.com/pmiddleton) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-274">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="7c1f3-275">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-275">Many thanks for the contribution!</span></span>

<span data-ttu-id="7c1f3-276">EF Core 5.0 incluye compatibilidad de primera clase para asignar métodos .NET a funciones con valores de tabla (TVF).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-276">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="7c1f3-277">Estas funciones se pueden usar en consultas LINQ, donde la composición adicional de los resultados de la función también se traducirá a SQL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-277">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="7c1f3-278">Por ejemplo, considere esta TVF definida en una base de datos SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-278">For example, consider this TVF defined in a SQL Server database:</span></span>

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

<span data-ttu-id="7c1f3-279">El modelo de EF Core requiere dos tipos de entidad para usar esta TVF:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-279">The EF Core model requires two entity types to use this TVF:</span></span>

* <span data-ttu-id="7c1f3-280">Un tipo `Employee` que se asigna a la tabla Employees de la forma habitual.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-280">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="7c1f3-281">Un tipo `Report` que coincide con la forma que devuelve la función TVF.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-281">A `Report` type that matches the shape returned by the TVF</span></span>

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

<span data-ttu-id="7c1f3-282">Estos tipos se deben incluir en el modelo de EF Core:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-282">These types must be included in the EF Core model:</span></span>

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="7c1f3-283">Observe que `Report` no tiene ninguna clave principal, por lo que debe configurarse como tal.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-283">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="7c1f3-284">Por último, se debe asignar un método .NET a la función TVF en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-284">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="7c1f3-285">Este método se puede definir en DbContext mediante el método `FromExpression` nuevo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-285">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="7c1f3-286">Este método usa un parámetro y un tipo de valor devuelto que coinciden con la función TVF definida anteriormente.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-286">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="7c1f3-287">Después, el método se agrega al modelo de EF Core en OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-287">The method is then added to the EF Core model in OnModelCreating:</span></span>

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="7c1f3-288">(Usar una expresión lambda aquí es una manera sencilla de pasar `MethodInfo` a EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-288">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="7c1f3-289">Se omiten los argumentos que se pasan al método).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-289">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="7c1f3-290">Ahora podemos escribir consultas que llamen a `GetReports` y se creen sobre los resultados.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-290">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="7c1f3-291">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-291">For example:</span></span>

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

<span data-ttu-id="7c1f3-292">En SQL Server, esto se traduce en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-292">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="7c1f3-293">Observe que SQL se ha modificado en la tabla `Employees`, llama a `GetReports` y, luego, agrega una cláusula WHERE adicional a los resultados de la función.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-293">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="7c1f3-294">Asignación flexible de consultas y actualizaciones</span><span class="sxs-lookup"><span data-stu-id="7c1f3-294">Flexible query/update mapping</span></span>

<span data-ttu-id="7c1f3-295">EF Core 5.0 permite asignar el mismo tipo de entidad a objetos de base de datos diferentes.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-295">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="7c1f3-296">Estos objetos pueden ser tablas, vistas o funciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-296">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="7c1f3-297">Por ejemplo, un tipo de entidad se puede asignar a una vista de base de datos y una tabla de base de datos:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-297">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="7c1f3-298">De forma predeterminada, EF Core consultará desde la vista y enviará actualizaciones a la tabla.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-298">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="7c1f3-299">Por ejemplo, ejecutar el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-299">For example, executing the following code:</span></span>

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="7c1f3-300">Da como resultado una consulta en la vista y, luego, una actualización de la tabla:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-300">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="7c1f3-301">Configuración de la consulta dividida en todo el contexto</span><span class="sxs-lookup"><span data-stu-id="7c1f3-301">Context-wide split-query configuration</span></span>

<span data-ttu-id="7c1f3-302">Las consultas divididas (vea más abajo) ahora se pueden configurar como valor predeterminado para cualquier consulta que ejecute DbContext.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-302">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="7c1f3-303">Esta configuración solo está disponible para los proveedores relacionales, por lo que debe especificarse como parte de la configuración de `UseProvider`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-303">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="7c1f3-304">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-304">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="7c1f3-305">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-305">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="7c1f3-306">Asignación de PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="7c1f3-306">PhysicalAddress mapping</span></span>

<span data-ttu-id="7c1f3-307">El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-307">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="7c1f3-308">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-308">Many thanks for the contribution!</span></span>

<span data-ttu-id="7c1f3-309">Ahora, la [clase PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-309">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="7c1f3-310">Para obtener más información, vea más abajo los ejemplos para `IPAddress`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-310">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="7c1f3-311">Versión preliminar 7</span><span class="sxs-lookup"><span data-stu-id="7c1f3-311">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="7c1f3-312">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="7c1f3-312">DbContextFactory</span></span>

<span data-ttu-id="7c1f3-313">EF Core 5.0 presenta `AddDbContextFactory` y `AddPooledDbContextFactory` para registrar una factoría que crea instancias de DbContext en el contenedor de inserción de dependencias (DI) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-313">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="7c1f3-314">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-314">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="7c1f3-315">Los servicios de aplicaciones, como los controladores de ASP.NET Core, pueden depender de `IDbContextFactory<TContext>` en el constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-315">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="7c1f3-316">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-316">For example:</span></span>

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

<span data-ttu-id="7c1f3-317">A continuación, las instancias de DbContext se pueden crear y usar según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-317">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="7c1f3-318">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-318">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="7c1f3-319">Tenga en cuenta que las instancias de DbContext creadas de este modo _no_ están administradas por el proveedor de servicios de la aplicación y, por lo tanto, la aplicación debe desecharlas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-319">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="7c1f3-320">Este desacoplamiento es muy útil para las aplicaciones Blazor, en las que se recomienda usar `IDbContextFactory`, pero también puede ser práctico en otros escenarios.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-320">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="7c1f3-321">Las instancias DbContext se pueden agrupar mediante una llamada a `AddPooledDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-321">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="7c1f3-322">Esta agrupación funciona de la misma manera que para `AddDbContextPool`, y también tiene las mismas limitaciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-322">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="7c1f3-323">En el problema [n.º 2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-323">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="7c1f3-324">Restablecimiento del estado de DbContext</span><span class="sxs-lookup"><span data-stu-id="7c1f3-324">Reset DbContext state</span></span>

<span data-ttu-id="7c1f3-325">EF Core 5.0 presenta `ChangeTracker.Clear()`, que borra la instancia de DbContext de todas las entidades de las que se ha realizado un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-325">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="7c1f3-326">Normalmente, esto no es necesario si se aplica el procedimiento recomendado de crear una nueva instancia de contexto de corta duración para cada unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-326">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="7c1f3-327">No obstante, si es necesario restablecer el estado de una instancia de DbContext, el uso del método `Clear()` es más eficaz y robusto que la separación masiva de todas las entidades.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-327">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="7c1f3-328">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-328">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="7c1f3-329">Nuevo patrón para los valores predeterminados generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="7c1f3-329">New pattern for store-generated defaults</span></span>

<span data-ttu-id="7c1f3-330">EF Core permite establecer un valor explícito para una columna que también puede tener una restricción de valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-330">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="7c1f3-331">EF Core usa el valor predeterminado de CLR de tipo de propiedad Type como centinela para este. Si el valor no es el predeterminado de CLR, se inserta; de lo contrario, se usa el valor predeterminado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-331">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="7c1f3-332">Esto crea problemas en los tipos en los que el valor predeterminado de CLR no es un buen centinela, en particular, las propiedades `bool`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-332">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="7c1f3-333">EF Core 5.0 ahora permite que la variable de respaldo acepte valores NULL para los casos como este.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-333">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="7c1f3-334">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-334">For example:</span></span>

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

<span data-ttu-id="7c1f3-335">Tenga en cuenta que la variable de respaldo admite valores NULL, pero la propiedad expuesta públicamente, no.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-335">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="7c1f3-336">Esto permite que el valor de centinela sea `null` sin afectar a la superficie pública del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-336">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="7c1f3-337">En este caso, si `IsValid` nunca se establece, se usará el valor predeterminado de la base de datos, ya que el campo de respaldo sigue siendo NULL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-337">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="7c1f3-338">Si se establece `true` o `false`, este valor se guarda explícitamente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-338">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="7c1f3-339">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-339">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="7c1f3-340">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-340">Cosmos partition keys</span></span>

<span data-ttu-id="7c1f3-341">EF Core permite que la clave de partición de Cosmos se incluya en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-341">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="7c1f3-342">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-342">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="7c1f3-343">A partir de la versión preliminar 7, la clave de partición se incluye en la clave pública del tipo de entidad y se usa para mejorar el rendimiento en algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-343">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="7c1f3-344">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-344">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="7c1f3-345">Configuración de Cosmos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-345">Cosmos configuration</span></span>

<span data-ttu-id="7c1f3-346">EF Core 5.0 mejora la configuración de Cosmos y de las conexiones Cosmos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-346">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="7c1f3-347">Anteriormente, EF Core requería especificar explícitamente el punto de conexión y la clave al conectarse a una base de datos de Cosmos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-347">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="7c1f3-348">En su lugar, EF Core 5.0 permite usar una cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-348">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="7c1f3-349">Además, EF Core 5.0 permite establecer explícitamente la instancia de WebProxy.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-349">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="7c1f3-350">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-350">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="7c1f3-351">Ahora también se pueden configurar muchos otros valores de tiempo de espera, límites, etc.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-351">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="7c1f3-352">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-352">For example:</span></span>

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

<span data-ttu-id="7c1f3-353">Por último, el modo de conexión predeterminado es ahora `ConnectionMode.Gateway`, que suele ofrecer mayor compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-353">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="7c1f3-354">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-354">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="7c1f3-355">Singularización en scaffolding de DbContext</span><span class="sxs-lookup"><span data-stu-id="7c1f3-355">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="7c1f3-356">Anteriormente, al aplicar scaffolding a un elemento DbContext de una base de datos existente, EF Core creaba nombres de tipo de entidad que coincidían con los de las tablas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-356">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="7c1f3-357">Por ejemplo, las tablas `People` y `Addresses` producían tipos de entidad denominados `People` y `Addresses`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-357">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="7c1f3-358">En versiones anteriores, este comportamiento se podía configurar mediante el registro de un servicio de pluralización.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-358">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="7c1f3-359">Ahora, en EF Core 5.0, el paquete [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) se usa como servicio de pluralización predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-359">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="7c1f3-360">Esto significa que, ahora, en las tablas `People` y `Addresses`, se aplicarán técnicas de ingeniería inversa para obtener los tipos de entidad denominados `Person` y `Address`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-360">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="7c1f3-361">Puntos de retorno</span><span class="sxs-lookup"><span data-stu-id="7c1f3-361">Savepoints</span></span>

<span data-ttu-id="7c1f3-362">EF Core admite ahora [puntos de retorno](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) para un mayor control sobre las transacciones que ejecutan varias operaciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-362">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="7c1f3-363">Los puntos de retorno se pueden crear, liberar y revertir manualmente.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-363">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="7c1f3-364">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-364">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="7c1f3-365">Además, EF Core se revierte ahora al último punto de retorno cuando se produce un error al ejecutar `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-365">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="7c1f3-366">Esto permite que se vuelva a intentar SaveChanges sin volver a probar toda la transacción.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-366">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="7c1f3-367">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-367">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="7c1f3-368">Versión preliminar 6</span><span class="sxs-lookup"><span data-stu-id="7c1f3-368">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="7c1f3-369">División de consultas para colecciones relacionadas</span><span class="sxs-lookup"><span data-stu-id="7c1f3-369">Split queries for related collections</span></span>

<span data-ttu-id="7c1f3-370">A partir de EF Core 3.0, EF Core siempre genera una única consulta SQL para cada consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-370">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="7c1f3-371">Esto garantiza la coherencia de los datos devueltos de acuerdo con las restricciones del modo de transacción en uso.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-371">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="7c1f3-372">Sin embargo, esta operación puede ser muy lenta si la consulta utiliza `Include` o una proyección para devolver varias colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-372">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="7c1f3-373">Ahora, EF Core 5.0 permite dividir en varias consultas SQL una única consulta LINQ con colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-373">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="7c1f3-374">Esto puede mejorar de forma significativa el rendimiento, pero puede dar lugar a incoherencias en los resultados devueltos si los datos de las dos consultas son diferentes.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-374">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="7c1f3-375">Las transacciones serializables o de instantáneas se pueden usar para mitigar esta situación y lograr la coherencia con las consultas divididas, pero esto puede conllevar otros costos de rendimiento y diferencias de comportamiento.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-375">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="7c1f3-376">División de consultas con Include</span><span class="sxs-lookup"><span data-stu-id="7c1f3-376">Split queries with Include</span></span>

<span data-ttu-id="7c1f3-377">Imaginemos, por ejemplo, que tenemos una consulta que extrae dos niveles de colecciones relacionadas mediante `Include`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-377">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="7c1f3-378">De forma predeterminada, EF Core generará el siguiente código SQL al usar el proveedor SQLite:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-378">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="7c1f3-379">La nueva API `AsSplitQuery` se puede usar para cambiar este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-379">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="7c1f3-380">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-380">For example:</span></span>

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="7c1f3-381">AsSplitQuery está disponible para todos los proveedores de bases de datos relacionales y se puede usar en cualquier parte de la consulta, igual que AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-381">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="7c1f3-382">EF Core generará ahora las tres consultas SQL siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-382">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="7c1f3-383">Se admiten todas las operaciones en la raíz de la consulta.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-383">All operations on the query root are supported.</span></span> <span data-ttu-id="7c1f3-384">Esto incluye las operaciones OrderBy, Skip, Take y Join, así como FirstOrDefault y operaciones de selección similares de resultado único.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-384">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="7c1f3-385">Tenga en cuenta que Inclusión filtrada con OrderBy, Skip o Take no se admite en la versión preliminar 6, pero está disponible en las compilaciones diarias y se incluirá en la versión preliminar 7.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-385">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="7c1f3-386">División de consultas con proyecciones de colecciones</span><span class="sxs-lookup"><span data-stu-id="7c1f3-386">Split queries with collection projections</span></span>

<span data-ttu-id="7c1f3-387">`AsSplitQuery` también se puede usar cuando se carguen colecciones en proyecciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-387">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="7c1f3-388">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-388">For example:</span></span>

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="7c1f3-389">Esta consulta LINQ genera las dos consultas SQL siguientes cuando se usa el proveedor de SQLite:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-389">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="7c1f3-390">Tenga en cuenta que solo se admite la materialización de la colección.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-390">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="7c1f3-391">Las composiciones posteriores a `e.Albums` en el caso anterior no generarán consultas divididas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-391">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="7c1f3-392">El número de seguimiento de las mejoras de esta área es el [21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-392">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="7c1f3-393">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="7c1f3-393">IndexAttribute</span></span>

<span data-ttu-id="7c1f3-394">El nuevo IndexAttribute se puede colocar en un tipo de entidad a fin de especificar un índice para una sola columna.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-394">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="7c1f3-395">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-395">For example:</span></span>

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="7c1f3-396">Para SQL Server, las migraciones generarán el siguiente código SQL:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-396">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="7c1f3-397">IndexAttribute también se puede usar para especificar un índice que abarque varias columnas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-397">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="7c1f3-398">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-398">For example:</span></span>

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

<span data-ttu-id="7c1f3-399">Para SQL Server, esto dará como resultado lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-399">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="7c1f3-400">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-400">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="7c1f3-401">Mejora de las excepciones de traducción de las consultas</span><span class="sxs-lookup"><span data-stu-id="7c1f3-401">Improved query translation exceptions</span></span>

<span data-ttu-id="7c1f3-402">Seguimos mejorando los mensajes de excepción que se generan cuando se produce un error en la traducción de una consulta.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-402">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="7c1f3-403">Por ejemplo, esta consulta usa la propiedad no asignada `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-403">For example, this query uses the unmapped property `IsSigned`:</span></span>

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="7c1f3-404">EF Core producirá la siguiente excepción que indica que se produjo un error de traducción porque no se asignó `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-404">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
   .Where(a => a.IsSigned)' could not be translated. Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed. Possibly the specified member is not mapped. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

<span data-ttu-id="7c1f3-405">Del mismo modo, ahora se generan mejores mensajes de excepción al intentar traducir comparaciones de cadenas con una semántica dependiente de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-405">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="7c1f3-406">Por ejemplo, esta consulta intenta usar `StringComparison.CurrentCulture`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-406">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="7c1f3-407">Ahora, EF Core producirá la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-407">EF Core will now throw the following exception:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
     .Where(a => a.Name.Equals(
         value: "The Unicorns",
         comparisonType: CurrentCulture))' could not be translated. Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported. See <https://go.microsoft.com/fwlink/?linkid=2129535> for more information. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

### <a name="specify-transaction-id"></a><span data-ttu-id="7c1f3-408">Especificación del id. de la transacción</span><span class="sxs-lookup"><span data-stu-id="7c1f3-408">Specify transaction ID</span></span>

<span data-ttu-id="7c1f3-409">El miembro de la comunidad [@Marusyk](https://github.com/Marusyk) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-409">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="7c1f3-410">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-410">Many thanks for the contribution!</span></span>

<span data-ttu-id="7c1f3-411">EF Core expone un id. de transacción para la correlación de las transacciones entre llamadas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-411">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="7c1f3-412">Este id. lo establece normalmente EF Core cuando se inicia una transacción.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-412">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="7c1f3-413">Si es la aplicación la que inicia la transacción, esta característica permite que la aplicación establezca de forma explícita el id. de transacción para que se ponga en correlación de forma correcta en todas las ubicaciones donde se use.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-413">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="7c1f3-414">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-414">For example:</span></span>

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="7c1f3-415">Asignación de IPAddress</span><span class="sxs-lookup"><span data-stu-id="7c1f3-415">IPAddress mapping</span></span>

<span data-ttu-id="7c1f3-416">El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-416">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="7c1f3-417">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-417">Many thanks for the contribution!</span></span>

<span data-ttu-id="7c1f3-418">Ahora, la [clase estándar IPAddress](/dotnet/api/system.net.ipaddress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-418">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="7c1f3-419">Por ejemplo, valore la posibilidad de asignar este tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-419">For example, consider mapping this entity type:</span></span>

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="7c1f3-420">En SQL Server, esto hará que las migraciones creen la siguiente tabla:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-420">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="7c1f3-421">Las entidades se pueden agregar de la manera habitual:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-421">Entities can then be added in the normal way:</span></span>

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="7c1f3-422">Y el código SQL resultante insertará la dirección IPv4 o IPv6 normalizada:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-422">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="7c1f3-423">Exclusión de OnConfiguring al aplicar scaffolding</span><span class="sxs-lookup"><span data-stu-id="7c1f3-423">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="7c1f3-424">Cuando se aplica scaffolding a un elemento DbContext desde una base de datos existente, EF Core crea de forma predeterminada una sobrecarga de OnConfiguring con una cadena de conexión para que el contexto se pueda usar de inmediato.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-424">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="7c1f3-425">Sin embargo, esto no es útil si ya tiene una clase parcial con OnConfiguring o si está configurando el contexto de otra manera.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-425">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="7c1f3-426">Para solucionar esto, ahora se puede indicar a los comandos de scaffolding que omitan la generación de OnConfiguring.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-426">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="7c1f3-427">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-427">For example:</span></span>

```console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="7c1f3-428">O bien, en la Consola del administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-428">Or in the Package Manager Console:</span></span>

```console
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="7c1f3-429">Tenga en cuenta que se recomienda usar [una cadena de conexión con nombre y almacenamiento seguro, como Secretos de usuario](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-429">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="7c1f3-430">Traducciones de FirstOrDefault en las cadenas</span><span class="sxs-lookup"><span data-stu-id="7c1f3-430">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="7c1f3-431">El miembro de la comunidad [@dvoreckyaa](https://github.com/dvoreckyaa) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-431">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="7c1f3-432">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-432">Many thanks for the contribution!</span></span>

<span data-ttu-id="7c1f3-433">Ahora se traducen FirstOrDefault y operadores similares para los caracteres de las cadenas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-433">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="7c1f3-434">Por ejemplo, esta consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-434">For example, this LINQ query:</span></span>

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="7c1f3-435">Se traducirá al siguiente código SQL al usar SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-435">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="7c1f3-436">Simplificación de bloques de casos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-436">Simplify case blocks</span></span>

<span data-ttu-id="7c1f3-437">Ahora, EF Core genera mejores consultas con bloques de casos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-437">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="7c1f3-438">Por ejemplo, esta consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-438">For example, this LINQ query:</span></span>

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="7c1f3-439">Estaba traducida en SQL Server oficialmente de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-439">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="7c1f3-440">Pero ahora se traduce así:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-440">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="7c1f3-441">Versión preliminar 5</span><span class="sxs-lookup"><span data-stu-id="7c1f3-441">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="7c1f3-442">Intercalaciones de bases de datos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-442">Database collations</span></span>

<span data-ttu-id="7c1f3-443">La intercalación predeterminada de una base de datos puede especificarse ahora en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-443">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="7c1f3-444">Este pasará a las migraciones generadas para establecer la intercalación cuando se cree la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-444">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="7c1f3-445">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-445">For example:</span></span>

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="7c1f3-446">A continuación, las migraciones generan lo siguiente para crear la base de datos en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-446">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="7c1f3-447">También se puede especificar la intercalación que se va a usar para columnas de bases de datos concretas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-447">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="7c1f3-448">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-448">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="7c1f3-449">En el caso de las que no usan migraciones, se aplica a las intercalaciones ingeniería inversa desde la base de datos, al aplicar scaffolding a DbContext.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-449">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="7c1f3-450">Por último, `EF.Functions.Collate()` permite realizar consultas ad-hoc mediante diferentes intercalaciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-450">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="7c1f3-451">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-451">For example:</span></span>

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="7c1f3-452">De este modo se generará la siguiente consulta para SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-452">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="7c1f3-453">Tenga en cuenta que las intercalaciones ad-hoc deben usarse con cuidado, ya que pueden afectar negativamente al rendimiento de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-453">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="7c1f3-454">En el problema [n.º 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-454">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="7c1f3-455">Transmisión de argumentos a IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="7c1f3-455">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="7c1f3-456">Los argumentos se transmiten ahora desde la línea de comandos al método de `CreateDbContext` de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-456">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="7c1f3-457">Por ejemplo, para indicar que se trata de una compilación de desarrollo, se puede pasar un argumento personalizado (por ejemplo, `dev`) en la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-457">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```console
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="7c1f3-458">Este argumento pasará entonces a la factoría, donde se puede usar para controlar cómo se crea e inicializa el contexto.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-458">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="7c1f3-459">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-459">For example:</span></span>

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="7c1f3-460">En el problema [n.º 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-460">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="7c1f3-461">Consultas de no seguimiento con resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="7c1f3-461">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="7c1f3-462">Ahora se pueden configurar consultas de no seguimiento para hacer la resolución de identidad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-462">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="7c1f3-463">Por ejemplo, la siguiente consulta creará una instancia de Blog para cada Post, aunque cada blog tenga la misma clave principal.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-463">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="7c1f3-464">Sin embargo, a costa de que por lo general sea ligeramente más lento y siempre use más memoria, se puede cambiar esta consulta para asegurarse de que solo se crea una instancia de Blog:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-464">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="7c1f3-465">Tenga en cuenta que esto solo es útil para consultas de no seguimiento, ya que todas las consultas de seguimiento ya muestran este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-465">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="7c1f3-466">Además, después de la revisión de la API, se cambiará la sintaxis de `PerformIdentityResolution`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-466">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="7c1f3-467">Consulte el problema [n.º 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-467">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="7c1f3-468">En el problema [n.º 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-468">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="7c1f3-469">Columnas calculadas almacenadas (persistentes)</span><span class="sxs-lookup"><span data-stu-id="7c1f3-469">Stored (persisted) computed columns</span></span>

<span data-ttu-id="7c1f3-470">La mayoría de las bases de datos permiten almacenar valores de columnas calculadas después del cálculo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-470">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="7c1f3-471">Aunque ocupa espacio en disco, la columna se calcula solo una vez al actualizar, en lugar de cada vez que se recupera su valor.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-471">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="7c1f3-472">Esto también permite indizar la columna para algunas bases de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-472">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="7c1f3-473">EF Core 5.0 permite configurar las columnas calculadas como almacenadas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-473">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="7c1f3-474">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-474">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="7c1f3-475">Columnas calculadas de SQLite</span><span class="sxs-lookup"><span data-stu-id="7c1f3-475">SQLite computed columns</span></span>

<span data-ttu-id="7c1f3-476">EF Core admite ahora columnas calculadas en las bases de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-476">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="7c1f3-477">Versión preliminar 4</span><span class="sxs-lookup"><span data-stu-id="7c1f3-477">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="7c1f3-478">Configuración de la precisión y la escala de la base de datos en el modelo</span><span class="sxs-lookup"><span data-stu-id="7c1f3-478">Configure database precision/scale in model</span></span>

<span data-ttu-id="7c1f3-479">Ahora se puede especificar la precisión y la escala de una propiedad mediante el generador de modelos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-479">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="7c1f3-480">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-480">For example:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="7c1f3-481">La precisión y la escala todavía se pueden establecer mediante el tipo completo de base de datos, como "decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="7c1f3-481">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="7c1f3-482">En el problema [n.º 527](https://github.com/dotnet/EntityFramework.Docs/issues/527) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-482">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="7c1f3-483">Especificación del factor de relleno del índice de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7c1f3-483">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="7c1f3-484">Ahora se puede especificar el factor de relleno al crear un índice en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-484">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="7c1f3-485">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-485">For example:</span></span>

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="7c1f3-486">Versión preliminar 3</span><span class="sxs-lookup"><span data-stu-id="7c1f3-486">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="7c1f3-487">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="7c1f3-487">Filtered Include</span></span>

<span data-ttu-id="7c1f3-488">El método Include ahora admite el filtrado de las entidades incluidas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-488">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="7c1f3-489">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-489">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="7c1f3-490">Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".</span><span class="sxs-lookup"><span data-stu-id="7c1f3-490">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="7c1f3-491">Para reducir el número de las entidades incluidas, se puede usar Skip y Take.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-491">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="7c1f3-492">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-492">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```

<span data-ttu-id="7c1f3-493">Esta consulta devolverá los blogs que tengan cinco publicaciones como máximo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-493">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="7c1f3-494">Vea la [documentación de Include](xref:core/querying/related-data#filtered-include) para obtener información completa.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-494">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="7c1f3-495">Nueva API de ModelBuilder para las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="7c1f3-495">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="7c1f3-496">Las propiedades de navegación se configuran principalmente al [definir relaciones](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-496">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="7c1f3-497">No obstante, se puede usar el nuevo método `Navigation` cuando es necesario realizar una configuración adicional en las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-497">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="7c1f3-498">Por ejemplo, para establecer un campo de respaldo de la navegación cuando el campo no se encuentre por convención:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-498">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="7c1f3-499">Tenga en cuenta que la API de `Navigation` no reemplaza la configuración de la relación,</span><span class="sxs-lookup"><span data-stu-id="7c1f3-499">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="7c1f3-500">sino que permite la configuración adicional de las propiedades de navegación en relaciones ya detectadas o definidas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-500">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="7c1f3-501">Consulte la [documentación sobre la configuración de las propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-501">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="7c1f3-502">Nuevos parámetros de la línea de comandos para espacios de nombres y cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="7c1f3-502">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="7c1f3-503">Ahora, las migraciones y el scaffolding permiten especificar los espacios de nombres en la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-503">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="7c1f3-504">Esto puede hacerse para, por ejemplo, aplicar ingeniería inversa a una base de datos y colocar las clases Context y Model en diferentes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-504">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="7c1f3-505">Para más información, consulte la documentación sobre [migraciones](xref:core/managing-schemas/migrations/index#namespaces) e [ingeniería inversa](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-505">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="7c1f3-506">Ahora, además, también puede pasarse una cadena de conexión al comando `database-update`:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-506">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="7c1f3-507">Para más información, consulte la [documentación sobre herramientas](xref:core/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-507">See the [Tools documentation](xref:core/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="7c1f3-508">También se han agregado parámetros equivalentes a los comandos de PowerShell que se usan en la consola del administrador de paquetes de VS.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-508">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="7c1f3-509">EnableDetailedErrors ha vuelto</span><span class="sxs-lookup"><span data-stu-id="7c1f3-509">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="7c1f3-510">Por motivos de rendimiento, EF no realiza comprobaciones de valores NULL adicionales cuando se leen los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-510">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="7c1f3-511">Esto puede dar lugar a excepciones cuya causa principal es difícil de rastrear cuando se encuentra un valor NULL inesperado.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-511">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="7c1f3-512">El uso de `EnableDetailedErrors` agregará una comprobación de valores NULL adicional a las consultas de modo que, para una pequeña sobrecarga de rendimiento, estos errores sean más fáciles de rastrear para averiguar su causa principal.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-512">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="7c1f3-513">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-513">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="7c1f3-514">En el problema [n.º 955](https://github.com/dotnet/EntityFramework.Docs/issues/955) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-514">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="7c1f3-515">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-515">Cosmos partition keys</span></span>

<span data-ttu-id="7c1f3-516">Ahora, es posible especificar en las consultas la clave de partición que va a usarse para una consulta determinada.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-516">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="7c1f3-517">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-517">For example:</span></span>

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="7c1f3-518">En el problema [n.º 2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-518">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="7c1f3-519">Compatibilidad con la función DATALENGTH de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7c1f3-519">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="7c1f3-520">Se puede acceder con el nuevo método `EF.Functions.DataLength`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-520">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="7c1f3-521">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-521">For example:</span></span>

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="7c1f3-522">Versión preliminar 2</span><span class="sxs-lookup"><span data-stu-id="7c1f3-522">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="7c1f3-523">Uso de un atributo de C# para especificar un campo de respaldo de propiedad</span><span class="sxs-lookup"><span data-stu-id="7c1f3-523">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="7c1f3-524">Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-524">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="7c1f3-525">Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-525">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="7c1f3-526">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-526">For example:</span></span>

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

<span data-ttu-id="7c1f3-527">En el problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-527">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="7c1f3-528">Asignación completa de discriminador</span><span class="sxs-lookup"><span data-stu-id="7c1f3-528">Complete discriminator mapping</span></span>

<span data-ttu-id="7c1f3-529">EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-529">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="7c1f3-530">Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-530">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="7c1f3-531">EF Core 5.0 ahora implementa estas mejoras.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-531">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="7c1f3-532">Por ejemplo, las versiones anteriores de EF Core siempre generaban este SQL para una consulta que devuelve todos los tipos de una jerarquía:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-532">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="7c1f3-533">EF Core 5.0 ahora genera lo siguiente cuando se configura una asignación completa de discriminador:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-533">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="7c1f3-534">Este va a ser el comportamiento predeterminado a partir de la versión preliminar 3.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-534">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="7c1f3-535">Mejoras de rendimiento de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7c1f3-535">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="7c1f3-536">Se han realizado dos mejoras de rendimiento para SQLIte:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-536">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="7c1f3-537">La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-537">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="7c1f3-538">La inicialización de SqliteConnection ahora es diferida.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-538">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="7c1f3-539">Estas mejoras se encuentran en el proveedor Microsoft.Data.Sqlite de ADO.NET y, por lo tanto, también mejoran el rendimiento fuera de EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-539">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="7c1f3-540">Versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="7c1f3-540">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="7c1f3-541">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="7c1f3-541">Simple logging</span></span>

<span data-ttu-id="7c1f3-542">Esta característica agrega funcionalidad similar a `Database.Log` en EF6.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-542">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="7c1f3-543">Es decir, proporciona una manera sencilla de obtener registros de EF Core sin necesidad de configurar ningún tipo de plataforma de registro externa.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-543">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="7c1f3-544">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-544">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="7c1f3-545">En el problema [n.º 2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-545">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="7c1f3-546">Forma sencilla de generar contenido SQL</span><span class="sxs-lookup"><span data-stu-id="7c1f3-546">Simple way to get generated SQL</span></span>

<span data-ttu-id="7c1f3-547">EF Core 5.0 presenta el método de extensión `ToQueryString`, que devuelve el SQL que EF Core genera al ejecutar una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-547">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="7c1f3-548">La documentación preliminar se incluye en el [estado semanal de EF del 9 de enero de 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-548">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="7c1f3-549">En el problema [n.º 1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-549">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="7c1f3-550">Uso de un atributo de C# para indicar que una entidad no tiene clave</span><span class="sxs-lookup"><span data-stu-id="7c1f3-550">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="7c1f3-551">Ahora se pueden configurar los tipos de entidad para indicar que no tienen clave mediante el nuevo valor `KeylessAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-551">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="7c1f3-552">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-552">For example:</span></span>

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="7c1f3-553">En el problema [n.º 2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-553">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="7c1f3-554">Posibilidad de cambiar la conexión o la cadena de conexión en una instancia inicializada de DbContext</span><span class="sxs-lookup"><span data-stu-id="7c1f3-554">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="7c1f3-555">Ahora es más fácil crear una instancia de DbContext sin ninguna conexión o cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-555">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="7c1f3-556">Además, la conexión o la cadena de conexión ahora también se pueden mutar en la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-556">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="7c1f3-557">Esta característica permite que la misma instancia de contexto se conecte de forma dinámica a diferentes bases de datos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-557">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="7c1f3-558">En el problema [n.º 2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-558">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="7c1f3-559">Proxies de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="7c1f3-559">Change-tracking proxies</span></span>

<span data-ttu-id="7c1f3-560">Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) y [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-560">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="7c1f3-561">A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-561">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="7c1f3-562">Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-562">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="7c1f3-563">En el problema [n.º 2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-563">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="7c1f3-564">Vistas de depuración mejoradas</span><span class="sxs-lookup"><span data-stu-id="7c1f3-564">Enhanced debug views</span></span>

<span data-ttu-id="7c1f3-565">Las vistas de depuración son una forma fácil de consultar los aspectos internos de EF Core al depurar problemas.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-565">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="7c1f3-566">Hace algún tiempo ya se implementó una vista de depuración para el modelo.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-566">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="7c1f3-567">En el caso de EF Core 5.0, hemos facilitado la lectura de la vista de modelo y hemos agregado una nueva vista de depuración para las entidades de las que se ha realizado un seguimiento en el administrador de estado.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-567">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="7c1f3-568">La documentación preliminar se incluye en el [estado semanal de EF del 12 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-568">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="7c1f3-569">En el problema [n.º 2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-569">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="7c1f3-570">Control mejorado de la semántica de valores NULL de base de datos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-570">Improved handling of database null semantics</span></span>

<span data-ttu-id="7c1f3-571">Normalmente, las bases de datos relacionales tratan NULL como valores desconocidos y, por lo tanto, no son iguales a otros valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-571">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="7c1f3-572">Mientras, C# trata NULL como un valor definido que se compara igual que cualquier otro valor NULL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-572">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="7c1f3-573">De forma predeterminada, EF Core traduce las consultas para que usen la semántica de valores NULL de C#.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-573">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="7c1f3-574">EF Core 5.0 mejora en gran medida la eficacia de dichas traducciones.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-574">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="7c1f3-575">En el problema [n.º 1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-575">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="7c1f3-576">Propiedades del indizador</span><span class="sxs-lookup"><span data-stu-id="7c1f3-576">Indexer properties</span></span>

<span data-ttu-id="7c1f3-577">EF Core 5.0 admite la asignación de propiedades de indizador de C#.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-577">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="7c1f3-578">Estas propiedades permiten a las entidades actuar como bolsas de propiedades en las que las columnas se asignan a propiedades con nombre de la bolsa.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-578">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="7c1f3-579">En el problema [n.º 2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-579">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="7c1f3-580">Generación de restricciones CHECK para las asignaciones de enumeración</span><span class="sxs-lookup"><span data-stu-id="7c1f3-580">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="7c1f3-581">Las migraciones de EF Core 5.0 ahora pueden generar restricciones CHECK para las asignaciones de propiedades de enumeración.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-581">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="7c1f3-582">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-582">For example:</span></span>

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="7c1f3-583">En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-583">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="7c1f3-584">IsRelational</span><span class="sxs-lookup"><span data-stu-id="7c1f3-584">IsRelational</span></span>

<span data-ttu-id="7c1f3-585">Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-585">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="7c1f3-586">Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-586">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="7c1f3-587">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-587">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="7c1f3-588">En el problema [n.º 2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-588">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="7c1f3-589">Simultaneidad optimista de Cosmos con mecanismos ETag</span><span class="sxs-lookup"><span data-stu-id="7c1f3-589">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="7c1f3-590">El proveedor de bases de datos de Azure Cosmos DB ya es compatible con la simultaneidad optimista mediante mecanismos ETag.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-590">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="7c1f3-591">Utilice el generador de modelos de OnModelCreating para confirmar un mecanismo ETag:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-591">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="7c1f3-592">Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](xref:core/saving/concurrency), por ejemplo, para implementar reintentos.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-592">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="7c1f3-593">En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-593">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="7c1f3-594">Traducciones de consultas para más construcciones DateTime</span><span class="sxs-lookup"><span data-stu-id="7c1f3-594">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="7c1f3-595">Ahora las consultas que contienen la nueva construcción DateTime se traducen.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-595">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="7c1f3-596">Además, ahora se asignan las funciones de SQL Server que hay a continuación:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-596">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="7c1f3-597">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="7c1f3-597">DateDiffWeek</span></span>
* <span data-ttu-id="7c1f3-598">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="7c1f3-598">DateFromParts</span></span>

<span data-ttu-id="7c1f3-599">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-599">For example:</span></span>

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="7c1f3-600">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-600">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="7c1f3-601">Traducciones de consultas para más construcciones de matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="7c1f3-601">Query translations for more byte array constructs</span></span>

<span data-ttu-id="7c1f3-602">Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-602">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="7c1f3-603">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="7c1f3-603">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="7c1f3-604">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-604">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="7c1f3-605">Traducción de consultas para Reverse</span><span class="sxs-lookup"><span data-stu-id="7c1f3-605">Query translation for Reverse</span></span>

<span data-ttu-id="7c1f3-606">Las consultas que usan `Reverse` ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-606">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="7c1f3-607">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-607">For example:</span></span>

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="7c1f3-608">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-608">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="7c1f3-609">Traducción de consultas para operadores bit a bit</span><span class="sxs-lookup"><span data-stu-id="7c1f3-609">Query translation for bitwise operators</span></span>

<span data-ttu-id="7c1f3-610">Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7c1f3-610">Queries using bitwise operators are now translated in more cases For example:</span></span>

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="7c1f3-611">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-611">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="7c1f3-612">Traducción de consultas para cadenas en Cosmos</span><span class="sxs-lookup"><span data-stu-id="7c1f3-612">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="7c1f3-613">Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-613">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="7c1f3-614">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7c1f3-614">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
