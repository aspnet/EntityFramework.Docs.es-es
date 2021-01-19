---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 64b72ba2a6f752b9d71ea9b64ab08f4cf92ef03d
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129283"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="0ba5e-103">Novedades en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="0ba5e-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="0ba5e-104">En la lista siguiente se incluyen las principales características nuevas de EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-104">The following list includes the major new features in EF Core 5.0.</span></span> <span data-ttu-id="0ba5e-105">Para obtener una lista completa de los problemas de la versión, consulte nuestro sitio de [seguimiento de problemas](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-105">For the full list of issues in the release, see our [issue tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span></span>

<span data-ttu-id="0ba5e-106">Como versión principal, EF Core 5.0 también presenta varios [cambios importantes](xref:core/what-is-new/ef-core-5.0/breaking-changes), que son mejoras en la API o cambios de comportamiento que podrían afectar negativamente a las aplicaciones existentes.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-106">As a major release, EF Core 5.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), which are API improvements or behavioral changes that may have negative impact on existing applications.</span></span>

## <a name="many-to-many"></a><span data-ttu-id="0ba5e-107">Varios a varios</span><span class="sxs-lookup"><span data-stu-id="0ba5e-107">Many-to-many</span></span>

<span data-ttu-id="0ba5e-108">EF Core 5.0 admite relaciones de varios a varios sin asignar explícitamente la tabla de combinación.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-108">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="0ba5e-109">Por ejemplo, considere estos tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-109">For example, consider these entity types:</span></span>

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

<span data-ttu-id="0ba5e-110">EF Core 5.0 reconoce esto como una relación de varios a varios por convención y crea automáticamente una tabla combinada `PostTag` en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-110">EF Core 5.0 recognizes this as a many-to-many relationship by convention, and automatically creates a `PostTag` join table in the database.</span></span> <span data-ttu-id="0ba5e-111">Los datos se pueden consultar y actualizar sin hacer referencia explícita a la tabla combinada, lo que simplifica considerablemente el código.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-111">Data can be queried and updated without explicitly referencing the join table, considerably simplifying code.</span></span> <span data-ttu-id="0ba5e-112">La tabla combinada se sigue pudiendo personalizar y consultar explícitamente si es necesario.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-112">The join table can still be customized and queried explicitly if needed.</span></span>

<span data-ttu-id="0ba5e-113">Para obtener más información, [vea la documentación completa sobre las relaciones de varios a varios](xref:core/modeling/relationships#many-to-many).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-113">For further information, [see the full documentation on many-to-many](xref:core/modeling/relationships#many-to-many).</span></span>

## <a name="split-queries"></a><span data-ttu-id="0ba5e-114">Consultas divididas</span><span class="sxs-lookup"><span data-stu-id="0ba5e-114">Split queries</span></span>

<span data-ttu-id="0ba5e-115">A partir de EF Core 3.0, EF Core siempre genera una única consulta SQL para cada consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-115">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="0ba5e-116">Esto garantiza la coherencia de los datos devueltos de acuerdo con las restricciones del modo de transacción en uso.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-116">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="0ba5e-117">Sin embargo, esta operación puede ser muy lenta si la consulta utiliza `Include` o una proyección para devolver varias colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-117">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="0ba5e-118">Ahora, EF Core 5.0 permite dividir en varias consultas SQL una única consulta LINQ con colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-118">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="0ba5e-119">Esto puede mejorar de forma significativa el rendimiento, pero puede dar lugar a incoherencias en los resultados devueltos si los datos de las dos consultas son diferentes.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-119">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="0ba5e-120">Las transacciones serializables o de instantáneas se pueden usar para mitigar esta situación y lograr la coherencia con las consultas divididas, pero esto puede conllevar otros costos de rendimiento y diferencias de comportamiento.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-120">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

<span data-ttu-id="0ba5e-121">Imaginemos, por ejemplo, que tenemos una consulta que extrae dos niveles de colecciones relacionadas mediante `Include`:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-121">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

<span data-ttu-id="0ba5e-122">De forma predeterminada, EF Core generará el siguiente código SQL al usar el proveedor SQLite:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-122">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

<span data-ttu-id="0ba5e-123">Con las consultas divididas, en su lugar se genera este código SQL:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-123">With split queries, the following SQL is generated instead:</span></span>

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

<span data-ttu-id="0ba5e-124">Las consultas divididas se pueden habilitar colocando el nuevo operador `AsSplitQuery` en cualquier parte de la consulta LINQ o globalmente en el método `OnConfiguring` del modelo.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-124">Split queries can be enabled by placing the new `AsSplitQuery` operator anywhere in your LINQ query, or globally in your model's `OnConfiguring`.</span></span> <span data-ttu-id="0ba5e-125">Para obtener más información, [vea la documentación completa sobre las consultas divididas](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-125">For further information, [see the full documentation on split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="simple-logging-and-improved-diagnostics"></a><span data-ttu-id="0ba5e-126">Registro sencillo y diagnósticos mejorados</span><span class="sxs-lookup"><span data-stu-id="0ba5e-126">Simple logging and improved diagnostics</span></span>

<span data-ttu-id="0ba5e-127">EF Core 5.0 presenta una forma sencilla de configurar el registro a través del nuevo método `LogTo`.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-127">EF Core 5.0 introduces a simple way to set up logging via the new `LogTo` method.</span></span> <span data-ttu-id="0ba5e-128">Lo siguiente hará que los mensajes de registro se escriban en la consola, incluido todo el código SQL generado por EF Core:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-128">The following will cause logging messages to be written to the console, including all SQL generated by EF Core:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

<span data-ttu-id="0ba5e-129">Además, ahora es posible llamar a `ToQueryString` en cualquier consulta LINQ recuperando el código SQL que ejecutaría la consulta:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-129">In addition, it is now possible to call `ToQueryString` on any LINQ query, retrieving the SQL that the query would execute:</span></span>

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

<span data-ttu-id="0ba5e-130">Por último, se han incorporado varios tipos de EF Core con una propiedad `DebugView` mejorada, la cual proporciona una vista detallada de los elementos internos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-130">Finally, various EF Core types have been fitted with an enhanced `DebugView` property which provides a detailed view into the internals.</span></span> <span data-ttu-id="0ba5e-131">Por ejemplo, se puede consultar la propiedad <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> para ver exactamente las entidades de las que se realiza un seguimiento en un momento determinado.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-131">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> can be consulted to see exactly which entities are being tracked in a given moment.</span></span>

<span data-ttu-id="0ba5e-132">Para obtener más información, [consulte la documentación sobre el registro y la interceptación](xref:core/logging-events-diagnostics/index).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-132">For further information, [see the documentation on logging and interception](xref:core/logging-events-diagnostics/index).</span></span>

## <a name="filtered-include"></a><span data-ttu-id="0ba5e-133">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="0ba5e-133">Filtered include</span></span>

<span data-ttu-id="0ba5e-134">El método `Include` ahora admite el filtrado de las entidades incluidas:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-134">The `Include` method now supports filtering of the entities included:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="0ba5e-135">Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".</span><span class="sxs-lookup"><span data-stu-id="0ba5e-135">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="0ba5e-136">Para obtener más información, [vea la documentación completa sobre las consultas divididas](xref:core/querying/related-data/eager#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-136">For further information, [see the full documentation on split queries](xref:core/querying/related-data/eager#filtered-include).</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="0ba5e-137">Asignación de tabla por tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="0ba5e-137">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="0ba5e-138">De forma predeterminada, EF Core asigna una jerarquía de herencia de tipos .NET a una tabla de base de datos única.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-138">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="0ba5e-139">Esto se conoce como asignación de tabla por jerarquía (TPH).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-139">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="0ba5e-140">EF Core 5.0 también permite asignar cada tipo .NET de una jerarquía de herencia a una tabla de base de datos diferente; esto se conoce como asignación de tabla por tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-140">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="0ba5e-141">Por ejemplo, piense en este modelo con una jerarquía asignada:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-141">For example, consider this model with a mapped hierarchy:</span></span>

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public string EducationLevel { get; set; }
}

public class Dog : Animal
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="0ba5e-142">Con TPT, se crea una tabla de base de datos para cada tipo en la jerarquía:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-142">With TPT, a database table is created for each type in the hierarchy:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);
```

<span data-ttu-id="0ba5e-143">Para obtener más información, [vea la documentación completa sobre la característica de tabla por tipo](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-143">For further information, [see the full documentation on TPT](xref:core/modeling/inheritance).</span></span>

## <a name="flexible-entity-mapping"></a><span data-ttu-id="0ba5e-144">Asignación de entidades flexible</span><span class="sxs-lookup"><span data-stu-id="0ba5e-144">Flexible entity mapping</span></span>

<span data-ttu-id="0ba5e-145">Normalmente, los tipos de entidad se asignan a tablas o vistas, de modo que EF Core extraerá el contenido de la tabla o vista al consultar ese tipo.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-145">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="0ba5e-146">EF Core 5.0 incluye opciones de asignación adicionales, donde una entidad se puede asignar a una consulta SQL (denominada "consulta de definición") o a una función con valores de tabla (TVF):</span><span class="sxs-lookup"><span data-stu-id="0ba5e-146">EF Core 5.0 adds additional mapping options, where an entity can be mapped to a SQL query (called a "defining query"), or to a table-valued function (TVF):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");

    modelBuilder.Entity<Blog>().ToFunction("BlogsReturningFunction");
}
```

<span data-ttu-id="0ba5e-147">Las funciones con valores de tabla también se pueden asignar a un método de .NET en lugar de a una instancia de DbSet, lo que permite pasar parámetros; la asignación se puede configurar con <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-147">Table-valued functions can also be mapped to a .NET method rather than to a DbSet, allowing parameters to be passed; the mapping can be set up with <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.</span></span>

<span data-ttu-id="0ba5e-148">Por último, ahora es posible asignar una entidad a una vista cuando se consulta (o a una función o una consulta de definición) y a una tabla cuando se actualiza:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-148">Finally, it is now possible to map an entity to a view when querying (or to a function or defining query), but to a table when updating:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a><span data-ttu-id="0ba5e-149">Tipos de entidad de tipo compartido y contenedores de propiedades</span><span class="sxs-lookup"><span data-stu-id="0ba5e-149">Shared-type entity types and property bags</span></span>

<span data-ttu-id="0ba5e-150">EF Core 5.0 permite asignar el mismo tipo de CLR a varios tipos distintos de entidad; estos tipos se conocen como tipos de entidad de tipo compartido.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-150">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types; such types are known as shared-type entity types.</span></span> <span data-ttu-id="0ba5e-151">Aunque se puede usar cualquier tipo de CLR con esta característica, el diccionario de .NET (`Dictionary`) ofrece un caso de uso especialmente atractivo al que llamamos "contenedores de propiedades":</span><span class="sxs-lookup"><span data-stu-id="0ba5e-151">While any CLR type can be used with this feature, .NET `Dictionary` offers a particularly compelling use-case which we call "property bags":</span></span>

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<decimal>("Price");
        });
    }
}
```

<span data-ttu-id="0ba5e-152">Después, estas entidades se pueden consultar y actualizar como los tipos de entidad normales con su propio tipo de CLR dedicado.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-152">These entities can then be queried and updated just like normal entity types with their own, dedicated CLR type.</span></span> <span data-ttu-id="0ba5e-153">Para obtener más información, vea la documentación sobre [contenedores de propiedades](xref:core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-153">More information can be found in the documentation on [property bags](xref:core/modeling/shadow-properties).</span></span>

## <a name="required-11-dependents"></a><span data-ttu-id="0ba5e-154">Dependientes de 1:1 requeridos</span><span class="sxs-lookup"><span data-stu-id="0ba5e-154">Required 1:1 dependents</span></span>

<span data-ttu-id="0ba5e-155">En EF Core 3.1, el extremo dependiente de una relación uno a uno se consideraba siempre opcional.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-155">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="0ba5e-156">Esto era más que evidente al usar entidades en propiedad, ya que todas las columnas de una entidad en propiedad se creaban para aceptar valores NULL en la base de datos, incluso si se habían configurado como requeridas en el modelo.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-156">This was most apparent when using owned entities, as all the owned entity's column were created as nullable in the database, even if they were configured as required in the model.</span></span>

<span data-ttu-id="0ba5e-157">En EF Core 5.0, la navegación a una entidad en propiedad se puede configurar como una entidad dependiente requerida.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-157">In EF Core 5.0, a navigation to an owned entity can be configured as as a required dependent.</span></span> <span data-ttu-id="0ba5e-158">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-158">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();
    });
}
```

## <a name="dbcontextfactory"></a><span data-ttu-id="0ba5e-159">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="0ba5e-159">DbContextFactory</span></span>

<span data-ttu-id="0ba5e-160">EF Core 5.0 introduce `AddDbContextFactory` y `AddPooledDbContextFactory` para registrar una fábrica que crea instancias de DbContext en el contenedor de inserción de dependencias de la aplicación. Esto puede ser útil cuando el código de la aplicación necesita crear y eliminar instancias de contexto manualmente.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-160">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container; this can be useful when application code needs to create and dispose context instances manually.</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="0ba5e-161">En este punto, los servicios de aplicaciones, como los controladores de ASP.NET Core, se pueden insertar con la interfaz `IDbContextFactory<TContext>` y pueden usarla para crear instancias de contexto:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-161">At this point, application services such as ASP.NET Core controllers can then be injected with `IDbContextFactory<TContext>`, and use it to instantiate context instances:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
        => _contextFactory = contextFactory;

    public void DoSomeThing()
    {
        using (var context = _contextFactory.CreateDbContext())
        {
            // ...
        }
    }
}
```

<span data-ttu-id="0ba5e-162">Para obtener más información, [vea la documentación completa sobre DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-162">For further information, [see the full documentation on DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span></span>

## <a name="sqlite-table-rebuilds"></a><span data-ttu-id="0ba5e-163">Recompilaciones de tabla de SQLite</span><span class="sxs-lookup"><span data-stu-id="0ba5e-163">SQLite table rebuilds</span></span>

<span data-ttu-id="0ba5e-164">En comparación con otras bases de datos, SQLite está relativamente limitada en sus capacidades de manipulación de esquemas; por ejemplo, no se admite la eliminación de una columna de una tabla existente.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-164">Compared to other databases, SQLite is relatively limited in its schema manipulation capabilities; for example, dropping a column from an existing table is not supported.</span></span> <span data-ttu-id="0ba5e-165">EF Core 5.0 pone fin a estas limitaciones mediante la creación de una nueva tabla, la copia de los datos de la tabla anterior, la eliminación de dicha tabla anterior y el cambio de nombre de la nueva tabla, todo esto forma automática.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-165">EF Core 5.0 works around these limitations by automatically creating a new table, copying the data from the old table, dropping the old table and renaming the new one.</span></span> <span data-ttu-id="0ba5e-166">Esto "recompila" la tabla y permite aplicar de forma segura operaciones de migración no admitidas previamente.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-166">This "rebuilds" the table, and allows previously unsupported migration operations to be safely applied.</span></span>

<span data-ttu-id="0ba5e-167">Para obtener más detalles sobre qué operaciones de migración se admiten ahora a través de las recompilaciones de tabla, [consulte esta página de documentación](xref:core/providers/sqlite/limitations#migrations-limitations).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-167">For details on which migration operations are now supported via table rebuilds, [see this documentation page](xref:core/providers/sqlite/limitations#migrations-limitations).</span></span>

## <a name="database-collations"></a><span data-ttu-id="0ba5e-168">Intercalaciones de bases de datos</span><span class="sxs-lookup"><span data-stu-id="0ba5e-168">Database collations</span></span>

<span data-ttu-id="0ba5e-169">EF Core 5.0 incluye compatibilidad con la especificación de intercalaciones de texto a nivel de base de datos, columna o consulta.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-169">EF Core 5.0 introduces support for specifying text collations at the database, column or query level.</span></span> <span data-ttu-id="0ba5e-170">Esto permite que la distinción de mayúsculas y minúsculas y otros aspectos textuales se configuren de forma flexible y no afecten al rendimiento de las consultas.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-170">This allows case sensitivity and other textual aspects to be configured in a way that is both flexible and does not compromise query performance.</span></span>

<span data-ttu-id="0ba5e-171">Por ejemplo, el siguiente código configurará la columna `Name` para que distinga entre mayúsculas y minúsculas en SQL Server, y los índices creados en la columna funcionarán según corresponda:</span><span class="sxs-lookup"><span data-stu-id="0ba5e-171">For example, the following will configure the `Name` column to be case-sensitive on SQL Server, and any indexes created on the column will function accordingly:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

<span data-ttu-id="0ba5e-172">Para obtener más información, [vea la documentación completa sobre intercalaciones y distinción entre mayúsculas y minúsculas](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-172">For further information, [see the full documentation on collations and case sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="event-counters"></a><span data-ttu-id="0ba5e-173">Contadores de eventos</span><span class="sxs-lookup"><span data-stu-id="0ba5e-173">Event counters</span></span>

<span data-ttu-id="0ba5e-174">EF Core 5.0 expone [contadores de eventos](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) que se pueden usar para realizar un seguimiento del rendimiento de la aplicación y detectar diversas anomalías.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-174">EF Core 5.0 exposes [event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) which can be used to track your application's performance and spot various anomalies.</span></span> <span data-ttu-id="0ba5e-175">Solo es necesario asociar a un proceso que ejecuta EF con la herramienta [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters):</span><span class="sxs-lookup"><span data-stu-id="0ba5e-175">Simply attach to a process running EF with the [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) tool:</span></span>

```console
> dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496

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

<span data-ttu-id="0ba5e-176">Para obtener más información, [vea la documentación completa sobre los contadores de eventos](xref:core/logging-events-diagnostics/event-counters).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-176">For further information, [see the full documentation on event counters](xref:core/logging-events-diagnostics/event-counters).</span></span>

## <a name="other-features"></a><span data-ttu-id="0ba5e-177">Otras características</span><span class="sxs-lookup"><span data-stu-id="0ba5e-177">Other features</span></span>

### <a name="model-building"></a><span data-ttu-id="0ba5e-178">Creación de modelos</span><span class="sxs-lookup"><span data-stu-id="0ba5e-178">Model building</span></span>

* <span data-ttu-id="0ba5e-179">Las API de creación de modelos se han agregado para facilitar la configuración de [comparadores de valores](xref:core/modeling/value-comparers).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-179">Model building APIs have been introduced for easier configuration of [value comparers](xref:core/modeling/value-comparers).</span></span>
* <span data-ttu-id="0ba5e-180">Ahora, las columnas calculadas se pueden configurar como [*almacenadas* o *virtuales*](xref:core/modeling/generated-properties#computed-columns).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-180">Computed columns can now be configured as [*stored* or *virtual*](xref:core/modeling/generated-properties#computed-columns).</span></span>
* <span data-ttu-id="0ba5e-181">Ahora la precisión y la escala se pueden configurar [a través de la API fluida](xref:core/modeling/entity-properties#precision-and-scale).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-181">Precision and scale can now be configured [via the Fluent API](xref:core/modeling/entity-properties#precision-and-scale).</span></span>
* <span data-ttu-id="0ba5e-182">Se han introducido nuevas API de creación de modelos para las [propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-182">New model building APIs have been introduced for [navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>
* <span data-ttu-id="0ba5e-183">Se han agregado nuevas API de creación de modelos para los campos, de forma similar a las propiedades.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-183">New model building APIs have been introduced for fields, similar to properties.</span></span>
* <span data-ttu-id="0ba5e-184">Los tipos de .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) y [IPAddress](/dotnet/api/system.net.ipaddress) ahora se pueden asignar a columnas de cadena de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-184">The .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) and [IPAddress](/dotnet/api/system.net.ipaddress) types can now be mapped to database string columns.</span></span>
* <span data-ttu-id="0ba5e-185">Ahora se puede configurar un campo de respaldo a través del [nuevo atributo `[BackingField]`](xref:core/modeling/backing-field).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-185">A backing field can now be configured via [the new `[BackingField]` attribute](xref:core/modeling/backing-field).</span></span>
* <span data-ttu-id="0ba5e-186">Ahora se permiten los campos de respaldo que aceptan valores NULL, lo que proporciona una mejor compatibilidad con los valores predeterminados generados por el almacén donde el valor predeterminado de CLR no es un buen centinela (en particular, los valores `bool`).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-186">Nullable backing fields are now allowed, providing better support for store-generated defaults where the CLR default isn't a good sentinel value (notable `bool`).</span></span>
* <span data-ttu-id="0ba5e-187">Se puede usar un nuevo atributo `[Index]` en un tipo de entidad para especificar un índice, en lugar de usar la API fluida.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-187">A new `[Index]` attribute can be used on an entity type to specify an index, instead of using the Fluent API.</span></span>
* <span data-ttu-id="0ba5e-188">Se puede usar un nuevo atributo `[Keyless]` para configurar un tipo de entidad [sin clave](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-188">A new `[Keyless]` attribute can be used to configure an entity type [as having no key](xref:core/modeling/keyless-entity-types).</span></span>
* <span data-ttu-id="0ba5e-189">De forma predeterminada, [EF Core ahora se refiere a los discriminadores como *completos*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), lo que significa que espera no ver nunca valores de discriminador no configurados por la aplicación en el modelo.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-189">By default, [EF Core now regards discriminators as *complete*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), meaning that it expects to never see discriminator values not configured by the application in the model.</span></span> <span data-ttu-id="0ba5e-190">Esto permite algunas mejoras en el rendimiento y se puede deshabilitar en caso de que la columna discriminadora pueda contener valores desconocidos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-190">This allows for some performance improvements, and can be disabled if your discriminator column might hold unknown values.</span></span>

### <a name="query"></a><span data-ttu-id="0ba5e-191">Consultar</span><span class="sxs-lookup"><span data-stu-id="0ba5e-191">Query</span></span>

* <span data-ttu-id="0ba5e-192">Las excepciones de errores de traducción de consultas ahora contienen motivos más explícitos sobre las causas del error para ayudar a identificar el problema.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-192">Query translation failure exceptions now contain more explicit reasons about the reasons for the failure, to help pinpoint the issue.</span></span>
* <span data-ttu-id="0ba5e-193">Las consultas de no seguimiento ahora pueden realizar la [resolución de identidad](xref:core/querying/tracking#identity-resolution), lo que evita que se devuelvan varias instancias de entidad para el mismo objeto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-193">No-tracking queries can now perform [identity resolution](xref:core/querying/tracking#identity-resolution), avoiding multiple entity instances being returned for the same database object.</span></span>
* <span data-ttu-id="0ba5e-194">Se ha agregado compatibilidad con GroupBy con agregados condicionales (por ejemplo, `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-194">Added support for GroupBy with conditional aggregates (e.g. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span></span>
* <span data-ttu-id="0ba5e-195">Se ha agregado compatibilidad con la traducción del operador Distinct en los elementos de grupo antes del agregado.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-195">Added support for translating the Distinct operator over group elements before aggregate.</span></span>
* <span data-ttu-id="0ba5e-196">Traducción de [`Reverse`](/dotnet/api/system.linq.queryable.reverse).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-196">Translation of [`Reverse`](/dotnet/api/system.linq.queryable.reverse).</span></span>
* <span data-ttu-id="0ba5e-197">Traducción de objetos `DateTime` mejorada para SQL Server (por ejemplo, [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) y [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-197">Improved translation around `DateTime` for SQL Server (e.g. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span></span>
* <span data-ttu-id="0ba5e-198">Traducción de nuevos métodos en matrices de bytes (por ejemplo, [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length) y [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-198">Translation of new methods on byte arrays (e.g. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span></span>
* <span data-ttu-id="0ba5e-199">Traducción de algunos operadores bit a bit adicionales, como el complemento de dos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-199">Translation of some additional bitwise operators, such as two's complement.</span></span>
* <span data-ttu-id="0ba5e-200">Traducción de `FirstOrDefault` en las cadenas.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-200">Translation of `FirstOrDefault` over strings.</span></span>
* <span data-ttu-id="0ba5e-201">Traducción de consultas mejorada en cuanto a la semántica de valores NULL, lo que da lugar a consultas más precisas y eficaces.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-201">Improved query translation around null semantics, resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="0ba5e-202">Ahora se pueden anotar funciones asignadas por el usuario para controlar la propagación de valores NULL, lo que también da como resultado consultas más precisas y eficaces.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-202">User-mapped functions can now be annotated to control null propagation, again resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="0ba5e-203">El código SQL que contiene bloques de casos ahora es bastante más conciso.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-203">SQL containing CASE blocks is now considerably more concise.</span></span>
* <span data-ttu-id="0ba5e-204">Ahora se puede llamar a la función [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) de SQL Server en las consultas a través del nuevo método [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-204">The SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) function can now be called in queries via the new [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) method.</span></span>
* <span data-ttu-id="0ba5e-205">`EnableDetailedErrors` agrega [detalles adicionales a las excepciones](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-205">`EnableDetailedErrors` adds [additional details to exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span></span>

### <a name="saving"></a><span data-ttu-id="0ba5e-206">Guardando</span><span class="sxs-lookup"><span data-stu-id="0ba5e-206">Saving</span></span>

* <span data-ttu-id="0ba5e-207">[Interceptación](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) y [eventos](xref:core/logging-events-diagnostics/events) de SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-207">SaveChanges [interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) and [events](xref:core/logging-events-diagnostics/events).</span></span>
* <span data-ttu-id="0ba5e-208">Se han incorporado API para controlar [puntos de retorno de transacciones](xref:core/saving/transactions#savepoints).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-208">APIs have been introduced for controlling [transaction savepoints](xref:core/saving/transactions#savepoints).</span></span> <span data-ttu-id="0ba5e-209">Además, EF Core creará automáticamente un punto de retorno cuando se llame a `SaveChanges` y ya haya una transacción en curso, y se revertirá a él en caso de error.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-209">In addition, EF Core will automatically create a savepoint when `SaveChanges` is called and a transaction is already in progress, and roll back to it in case of failure.</span></span>
* <span data-ttu-id="0ba5e-210">La aplicación puede establecer explícitamente un identificador de transacción, lo que permite una correlación más sencilla de los eventos de transacción en el registro y en cualquier otro lugar.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-210">A transaction ID can be explicitly set by the application, allowing for easier correlation of transaction events in logging and elsewhere.</span></span>
* <span data-ttu-id="0ba5e-211">El tamaño máximo predeterminado del lote de SQL Server ha cambiado a 42 debido a un análisis del rendimiento del procesamiento por lotes.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-211">The default maximum batch size for SQL Server has been changed to 42 based on an analysis of batching performance.</span></span>

### <a name="migrations-and-scaffolding"></a><span data-ttu-id="0ba5e-212">Migraciones y scaffolding</span><span class="sxs-lookup"><span data-stu-id="0ba5e-212">Migrations and scaffolding</span></span>

* <span data-ttu-id="0ba5e-213">Ahora, las tablas se pueden [excluir de las migraciones](xref:core/modeling/entity-types#excluding-from-migrations).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-213">Tables can now be [excluded from migrations](xref:core/modeling/entity-types#excluding-from-migrations).</span></span>
* <span data-ttu-id="0ba5e-214">Un nuevo comando [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) muestra las migraciones que todavía no se han aplicado a la base de datos ([`Get-Migration`](xref:core/cli/powershell#get-migration) hace lo mismo en la consola de Administración de paquetes).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-214">A new [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) command now shows which migrations have not yet been applied to the database ([`Get-Migration`](xref:core/cli/powershell#get-migration) does the same in the Package Management Console).</span></span>
* <span data-ttu-id="0ba5e-215">Ahora, los scripts de migración contienen instrucciones de transacción cuando corresponde para mejorar la administración de los casos en los que se producen errores al aplicar la migración.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-215">Migrations scripts now contain transaction statements where appropriate to improve handling cases where migration application fails.</span></span>
* <span data-ttu-id="0ba5e-216">Las columnas de las clases base no asignadas se ordenan ahora después de otras columnas para los tipos de entidad asignados.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-216">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="0ba5e-217">Tenga en cuenta que esto solo afecta a las tablas recién creadas; el orden de las columnas de las tablas existentes permanece sin cambios.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-217">Note this only impacts newly created tables; the column order for existing tables remains unchanged.</span></span>
* <span data-ttu-id="0ba5e-218">La generación de migraciones ahora puede tener en cuenta si la migración que se está generando es idempotente y si la salida se ejecutará inmediatamente o se generará como un script.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-218">Migration generation can now be aware if the migration being generated is idempotent, and whether the output will be executed immediately or generated as a script.</span></span>
* <span data-ttu-id="0ba5e-219">Se han agregado nuevos parámetros de la línea de comandos para especificar los espacios de nombres en [migraciones](xref:core/managing-schemas/migrations/index#namespaces) y [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-219">New command-line parameters have been added for specifying namespaces in [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span></span>
* <span data-ttu-id="0ba5e-220">El comando [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) ahora acepta un nuevo parámetro `--connection` para especificar la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-220">The [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) command now accepts a new `--connection` parameter for specifying the connection string.</span></span>
* <span data-ttu-id="0ba5e-221">La aplicación de scaffolding a las bases de datos existentes ahora pone en singular los nombres de las tablas, por lo que las tablas denominadas `People` y `Addresses` pasarán a ser tipos de entidad denominados `Person` y `Address` al aplicarles scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-221">Scaffolding existing databases now singularizes table names, so tables named `People` and `Addresses` will be scaffolded to entity types called `Person` and `Address`.</span></span> <span data-ttu-id="0ba5e-222">[Los nombres de las bases de datos originales se pueden conservar](xref:core/managing-schemas/scaffolding#preserving-names).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-222">[Original database names can still be preserved](xref:core/managing-schemas/scaffolding#preserving-names).</span></span>
* <span data-ttu-id="0ba5e-223">La nueva opción [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) puede indicar a EF Core que excluya el método `OnModelConfiguring` cuando se aplique scaffolding a un modelo.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-223">The new [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option can instruct EF Core to exclude `OnModelConfiguring` when scaffolding a model.</span></span>

### <a name="cosmos"></a><span data-ttu-id="0ba5e-224">Cosmos</span><span class="sxs-lookup"><span data-stu-id="0ba5e-224">Cosmos</span></span>

* <span data-ttu-id="0ba5e-225">Se han ampliado las [opciones de conexión de Cosmos](xref:core/providers/cosmos/index#cosmos-options).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-225">[Cosmos connection settings](xref:core/providers/cosmos/index#cosmos-options) have been expanded.</span></span>
* <span data-ttu-id="0ba5e-226">Ahora se admite la [simultaneidad optimista en Cosmos mediante el uso de ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-226">Optimistic concurrency is now [supported on Cosmos via the use of ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span></span>
* <span data-ttu-id="0ba5e-227">El nuevo método `WithPartitionKey` permite incluir la [clave de partición](xref:core/providers/cosmos/index#partition-keys) de Cosmos en el modelo y en las consultas.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-227">The new `WithPartitionKey` method allows the Cosmos [partition key](xref:core/providers/cosmos/index#partition-keys) to be included both in the model and in queries.</span></span>
* <span data-ttu-id="0ba5e-228">Ahora se traducen los métodos de cadena [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) y [`EndsWith`](/dotnet/api/system.string.endswith) para Cosmos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-228">The string methods [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) and [`EndsWith`](/dotnet/api/system.string.endswith) are now translated for Cosmos.</span></span>
* <span data-ttu-id="0ba5e-229">El operador `is` de C# ahora se traduce en Cosmos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-229">The C# `is` operator is now translated on Cosmos.</span></span>

### <a name="sqlite"></a><span data-ttu-id="0ba5e-230">SQLite</span><span class="sxs-lookup"><span data-stu-id="0ba5e-230">Sqlite</span></span>

* <span data-ttu-id="0ba5e-231">Ahora se admiten columnas calculadas.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-231">Computed columns are now supported.</span></span>
* <span data-ttu-id="0ba5e-232">La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-232">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="0ba5e-233">La inicialización de SqliteConnection ahora es diferida.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-233">Initialization of SqliteConnection is now lazy.</span></span>

### <a name="other"></a><span data-ttu-id="0ba5e-234">Otros</span><span class="sxs-lookup"><span data-stu-id="0ba5e-234">Other</span></span>

* <span data-ttu-id="0ba5e-235">Se pueden generar proxies de seguimiento de cambios que implementen automáticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) y [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-235">Change-tracking proxies can be generated that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="0ba5e-236">Esto proporciona un enfoque alternativo para el seguimiento de cambios que no busca cambios cuando se llama a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-236">This provides an alternative approach to change-tracking that doesn't scan for changes when `SaveChanges` is called.</span></span>
* <span data-ttu-id="0ba5e-237">Ahora se puede cambiar un objeto <xref:System.Data.Common.DbConnection> o una cadena de conexión en una instancia ya inicializada de DbContext.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-237">A <xref:System.Data.Common.DbConnection> or connection string can now be changed on an already-initialized DbContext.</span></span>
* <span data-ttu-id="0ba5e-238">El nuevo método <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) borra la instancia de DbContext de todas las entidades sometidas a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-238">The new <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) method clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="0ba5e-239">Normalmente, esto no es necesario si se aplica el procedimiento recomendado de crear una nueva instancia de contexto de corta duración para cada unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-239">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="0ba5e-240">Pero si es necesario restablecer el estado de una instancia de DbContext, el uso del nuevo método `Clear()` es más eficaz y robusto que la separación masiva de todas las entidades.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-240">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more efficient and robust than mass-detaching all entities.</span></span>
* <span data-ttu-id="0ba5e-241">Las herramientas de línea de comandos de EF Core ahora configuran automáticamente las variables de entorno `ASPNETCORE_ENVIRONMENT` _y_ `DOTNET_ENVIRONMENT` en "desarrollo".</span><span class="sxs-lookup"><span data-stu-id="0ba5e-241">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="0ba5e-242">Esto aporta la experiencia al usar el host genérico en línea con la experiencia de ASP.NET Core durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-242">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span>
* <span data-ttu-id="0ba5e-243">Los argumentos personalizados de la línea de comandos pueden fluir en <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, lo que permite que las aplicaciones controlen cómo se crea e inicializa el contexto.</span><span class="sxs-lookup"><span data-stu-id="0ba5e-243">Custom command-line arguments can be flowed into <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, allowing applications to control how the context is created and initialized.</span></span>
* <span data-ttu-id="0ba5e-244">Ahora, el factor de relleno de índices se puede [configurar en SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-244">The index fill factor can now be [configured on SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span></span>
* <span data-ttu-id="0ba5e-245">La nueva propiedad <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> se puede usar para distinguir cuándo se usa un proveedor relacional o uno no relacional (como InMemory).</span><span class="sxs-lookup"><span data-stu-id="0ba5e-245">The new <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> property can be used to distinguish when using a relational provider and a non-relation provider (such as InMemory).</span></span>
