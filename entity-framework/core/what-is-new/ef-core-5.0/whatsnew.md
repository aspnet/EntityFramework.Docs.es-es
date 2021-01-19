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
# <a name="whats-new-in-ef-core-50"></a>Novedades en EF Core 5.0

En la lista siguiente se incluyen las principales características nuevas de EF Core 5.0. Para obtener una lista completa de los problemas de la versión, consulte nuestro sitio de [seguimiento de problemas](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).

Como versión principal, EF Core 5.0 también presenta varios [cambios importantes](xref:core/what-is-new/ef-core-5.0/breaking-changes), que son mejoras en la API o cambios de comportamiento que podrían afectar negativamente a las aplicaciones existentes.

## <a name="many-to-many"></a>Varios a varios

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

EF Core 5.0 reconoce esto como una relación de varios a varios por convención y crea automáticamente una tabla combinada `PostTag` en la base de datos. Los datos se pueden consultar y actualizar sin hacer referencia explícita a la tabla combinada, lo que simplifica considerablemente el código. La tabla combinada se sigue pudiendo personalizar y consultar explícitamente si es necesario.

Para obtener más información, [vea la documentación completa sobre las relaciones de varios a varios](xref:core/modeling/relationships#many-to-many).

## <a name="split-queries"></a>Consultas divididas

A partir de EF Core 3.0, EF Core siempre genera una única consulta SQL para cada consulta LINQ. Esto garantiza la coherencia de los datos devueltos de acuerdo con las restricciones del modo de transacción en uso. Sin embargo, esta operación puede ser muy lenta si la consulta utiliza `Include` o una proyección para devolver varias colecciones relacionadas.

Ahora, EF Core 5.0 permite dividir en varias consultas SQL una única consulta LINQ con colecciones relacionadas. Esto puede mejorar de forma significativa el rendimiento, pero puede dar lugar a incoherencias en los resultados devueltos si los datos de las dos consultas son diferentes. Las transacciones serializables o de instantáneas se pueden usar para mitigar esta situación y lograr la coherencia con las consultas divididas, pero esto puede conllevar otros costos de rendimiento y diferencias de comportamiento.

Imaginemos, por ejemplo, que tenemos una consulta que extrae dos niveles de colecciones relacionadas mediante `Include`:

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

De forma predeterminada, EF Core generará el siguiente código SQL al usar el proveedor SQLite:

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

Con las consultas divididas, en su lugar se genera este código SQL:

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

Las consultas divididas se pueden habilitar colocando el nuevo operador `AsSplitQuery` en cualquier parte de la consulta LINQ o globalmente en el método `OnConfiguring` del modelo. Para obtener más información, [vea la documentación completa sobre las consultas divididas](xref:core/querying/single-split-queries).

## <a name="simple-logging-and-improved-diagnostics"></a>Registro sencillo y diagnósticos mejorados

EF Core 5.0 presenta una forma sencilla de configurar el registro a través del nuevo método `LogTo`. Lo siguiente hará que los mensajes de registro se escriban en la consola, incluido todo el código SQL generado por EF Core:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

Además, ahora es posible llamar a `ToQueryString` en cualquier consulta LINQ recuperando el código SQL que ejecutaría la consulta:

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

Por último, se han incorporado varios tipos de EF Core con una propiedad `DebugView` mejorada, la cual proporciona una vista detallada de los elementos internos. Por ejemplo, se puede consultar la propiedad <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> para ver exactamente las entidades de las que se realiza un seguimiento en un momento determinado.

Para obtener más información, [consulte la documentación sobre el registro y la interceptación](xref:core/logging-events-diagnostics/index).

## <a name="filtered-include"></a>Inclusión filtrada

El método `Include` ahora admite el filtrado de las entidades incluidas:

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".

Para obtener más información, [vea la documentación completa sobre las consultas divididas](xref:core/querying/related-data/eager#filtered-include).

## <a name="table-per-type-tpt-mapping"></a>Asignación de tabla por tipo (TPT)

De forma predeterminada, EF Core asigna una jerarquía de herencia de tipos .NET a una tabla de base de datos única. Esto se conoce como asignación de tabla por jerarquía (TPH). EF Core 5.0 también permite asignar cada tipo .NET de una jerarquía de herencia a una tabla de base de datos diferente; esto se conoce como asignación de tabla por tipo (TPT).

Por ejemplo, piense en este modelo con una jerarquía asignada:

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

Con TPT, se crea una tabla de base de datos para cada tipo en la jerarquía:

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

Para obtener más información, [vea la documentación completa sobre la característica de tabla por tipo](xref:core/modeling/inheritance).

## <a name="flexible-entity-mapping"></a>Asignación de entidades flexible

Normalmente, los tipos de entidad se asignan a tablas o vistas, de modo que EF Core extraerá el contenido de la tabla o vista al consultar ese tipo. EF Core 5.0 incluye opciones de asignación adicionales, donde una entidad se puede asignar a una consulta SQL (denominada "consulta de definición") o a una función con valores de tabla (TVF):

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

Las funciones con valores de tabla también se pueden asignar a un método de .NET en lugar de a una instancia de DbSet, lo que permite pasar parámetros; la asignación se puede configurar con <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.

Por último, ahora es posible asignar una entidad a una vista cuando se consulta (o a una función o una consulta de definición) y a una tabla cuando se actualiza:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a>Tipos de entidad de tipo compartido y contenedores de propiedades

EF Core 5.0 permite asignar el mismo tipo de CLR a varios tipos distintos de entidad; estos tipos se conocen como tipos de entidad de tipo compartido. Aunque se puede usar cualquier tipo de CLR con esta característica, el diccionario de .NET (`Dictionary`) ofrece un caso de uso especialmente atractivo al que llamamos "contenedores de propiedades":

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

Después, estas entidades se pueden consultar y actualizar como los tipos de entidad normales con su propio tipo de CLR dedicado. Para obtener más información, vea la documentación sobre [contenedores de propiedades](xref:core/modeling/shadow-properties).

## <a name="required-11-dependents"></a>Dependientes de 1:1 requeridos

En EF Core 3.1, el extremo dependiente de una relación uno a uno se consideraba siempre opcional. Esto era más que evidente al usar entidades en propiedad, ya que todas las columnas de una entidad en propiedad se creaban para aceptar valores NULL en la base de datos, incluso si se habían configurado como requeridas en el modelo.

En EF Core 5.0, la navegación a una entidad en propiedad se puede configurar como una entidad dependiente requerida. Por ejemplo:

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

## <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5.0 introduce `AddDbContextFactory` y `AddPooledDbContextFactory` para registrar una fábrica que crea instancias de DbContext en el contenedor de inserción de dependencias de la aplicación. Esto puede ser útil cuando el código de la aplicación necesita crear y eliminar instancias de contexto manualmente.

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

En este punto, los servicios de aplicaciones, como los controladores de ASP.NET Core, se pueden insertar con la interfaz `IDbContextFactory<TContext>` y pueden usarla para crear instancias de contexto:

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

Para obtener más información, [vea la documentación completa sobre DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).

## <a name="sqlite-table-rebuilds"></a>Recompilaciones de tabla de SQLite

En comparación con otras bases de datos, SQLite está relativamente limitada en sus capacidades de manipulación de esquemas; por ejemplo, no se admite la eliminación de una columna de una tabla existente. EF Core 5.0 pone fin a estas limitaciones mediante la creación de una nueva tabla, la copia de los datos de la tabla anterior, la eliminación de dicha tabla anterior y el cambio de nombre de la nueva tabla, todo esto forma automática. Esto "recompila" la tabla y permite aplicar de forma segura operaciones de migración no admitidas previamente.

Para obtener más detalles sobre qué operaciones de migración se admiten ahora a través de las recompilaciones de tabla, [consulte esta página de documentación](xref:core/providers/sqlite/limitations#migrations-limitations).

## <a name="database-collations"></a>Intercalaciones de bases de datos

EF Core 5.0 incluye compatibilidad con la especificación de intercalaciones de texto a nivel de base de datos, columna o consulta. Esto permite que la distinción de mayúsculas y minúsculas y otros aspectos textuales se configuren de forma flexible y no afecten al rendimiento de las consultas.

Por ejemplo, el siguiente código configurará la columna `Name` para que distinga entre mayúsculas y minúsculas en SQL Server, y los índices creados en la columna funcionarán según corresponda:

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

Para obtener más información, [vea la documentación completa sobre intercalaciones y distinción entre mayúsculas y minúsculas](xref:core/miscellaneous/collations-and-case-sensitivity).

## <a name="event-counters"></a>Contadores de eventos

EF Core 5.0 expone [contadores de eventos](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) que se pueden usar para realizar un seguimiento del rendimiento de la aplicación y detectar diversas anomalías. Solo es necesario asociar a un proceso que ejecuta EF con la herramienta [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters):

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

Para obtener más información, [vea la documentación completa sobre los contadores de eventos](xref:core/logging-events-diagnostics/event-counters).

## <a name="other-features"></a>Otras características

### <a name="model-building"></a>Creación de modelos

* Las API de creación de modelos se han agregado para facilitar la configuración de [comparadores de valores](xref:core/modeling/value-comparers).
* Ahora, las columnas calculadas se pueden configurar como [*almacenadas* o *virtuales*](xref:core/modeling/generated-properties#computed-columns).
* Ahora la precisión y la escala se pueden configurar [a través de la API fluida](xref:core/modeling/entity-properties#precision-and-scale).
* Se han introducido nuevas API de creación de modelos para las [propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).
* Se han agregado nuevas API de creación de modelos para los campos, de forma similar a las propiedades.
* Los tipos de .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) y [IPAddress](/dotnet/api/system.net.ipaddress) ahora se pueden asignar a columnas de cadena de la base de datos.
* Ahora se puede configurar un campo de respaldo a través del [nuevo atributo `[BackingField]`](xref:core/modeling/backing-field).
* Ahora se permiten los campos de respaldo que aceptan valores NULL, lo que proporciona una mejor compatibilidad con los valores predeterminados generados por el almacén donde el valor predeterminado de CLR no es un buen centinela (en particular, los valores `bool`).
* Se puede usar un nuevo atributo `[Index]` en un tipo de entidad para especificar un índice, en lugar de usar la API fluida.
* Se puede usar un nuevo atributo `[Keyless]` para configurar un tipo de entidad [sin clave](xref:core/modeling/keyless-entity-types).
* De forma predeterminada, [EF Core ahora se refiere a los discriminadores como *completos*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), lo que significa que espera no ver nunca valores de discriminador no configurados por la aplicación en el modelo. Esto permite algunas mejoras en el rendimiento y se puede deshabilitar en caso de que la columna discriminadora pueda contener valores desconocidos.

### <a name="query"></a>Consultar

* Las excepciones de errores de traducción de consultas ahora contienen motivos más explícitos sobre las causas del error para ayudar a identificar el problema.
* Las consultas de no seguimiento ahora pueden realizar la [resolución de identidad](xref:core/querying/tracking#identity-resolution), lo que evita que se devuelvan varias instancias de entidad para el mismo objeto de base de datos.
* Se ha agregado compatibilidad con GroupBy con agregados condicionales (por ejemplo, `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).
* Se ha agregado compatibilidad con la traducción del operador Distinct en los elementos de grupo antes del agregado.
* Traducción de [`Reverse`](/dotnet/api/system.linq.queryable.reverse).
* Traducción de objetos `DateTime` mejorada para SQL Server (por ejemplo, [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) y [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).
* Traducción de nuevos métodos en matrices de bytes (por ejemplo, [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length) y [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).
* Traducción de algunos operadores bit a bit adicionales, como el complemento de dos.
* Traducción de `FirstOrDefault` en las cadenas.
* Traducción de consultas mejorada en cuanto a la semántica de valores NULL, lo que da lugar a consultas más precisas y eficaces.
* Ahora se pueden anotar funciones asignadas por el usuario para controlar la propagación de valores NULL, lo que también da como resultado consultas más precisas y eficaces.
* El código SQL que contiene bloques de casos ahora es bastante más conciso.
* Ahora se puede llamar a la función [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) de SQL Server en las consultas a través del nuevo método [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A).
* `EnableDetailedErrors` agrega [detalles adicionales a las excepciones](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).

### <a name="saving"></a>Guardando

* [Interceptación](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) y [eventos](xref:core/logging-events-diagnostics/events) de SaveChanges.
* Se han incorporado API para controlar [puntos de retorno de transacciones](xref:core/saving/transactions#savepoints). Además, EF Core creará automáticamente un punto de retorno cuando se llame a `SaveChanges` y ya haya una transacción en curso, y se revertirá a él en caso de error.
* La aplicación puede establecer explícitamente un identificador de transacción, lo que permite una correlación más sencilla de los eventos de transacción en el registro y en cualquier otro lugar.
* El tamaño máximo predeterminado del lote de SQL Server ha cambiado a 42 debido a un análisis del rendimiento del procesamiento por lotes.

### <a name="migrations-and-scaffolding"></a>Migraciones y scaffolding

* Ahora, las tablas se pueden [excluir de las migraciones](xref:core/modeling/entity-types#excluding-from-migrations).
* Un nuevo comando [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) muestra las migraciones que todavía no se han aplicado a la base de datos ([`Get-Migration`](xref:core/cli/powershell#get-migration) hace lo mismo en la consola de Administración de paquetes).
* Ahora, los scripts de migración contienen instrucciones de transacción cuando corresponde para mejorar la administración de los casos en los que se producen errores al aplicar la migración.
* Las columnas de las clases base no asignadas se ordenan ahora después de otras columnas para los tipos de entidad asignados. Tenga en cuenta que esto solo afecta a las tablas recién creadas; el orden de las columnas de las tablas existentes permanece sin cambios.
* La generación de migraciones ahora puede tener en cuenta si la migración que se está generando es idempotente y si la salida se ejecutará inmediatamente o se generará como un script.
* Se han agregado nuevos parámetros de la línea de comandos para especificar los espacios de nombres en [migraciones](xref:core/managing-schemas/migrations/index#namespaces) y [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).
* El comando [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) ahora acepta un nuevo parámetro `--connection` para especificar la cadena de conexión.
* La aplicación de scaffolding a las bases de datos existentes ahora pone en singular los nombres de las tablas, por lo que las tablas denominadas `People` y `Addresses` pasarán a ser tipos de entidad denominados `Person` y `Address` al aplicarles scaffolding. [Los nombres de las bases de datos originales se pueden conservar](xref:core/managing-schemas/scaffolding#preserving-names).
* La nueva opción [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) puede indicar a EF Core que excluya el método `OnModelConfiguring` cuando se aplique scaffolding a un modelo.

### <a name="cosmos"></a>Cosmos

* Se han ampliado las [opciones de conexión de Cosmos](xref:core/providers/cosmos/index#cosmos-options).
* Ahora se admite la [simultaneidad optimista en Cosmos mediante el uso de ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).
* El nuevo método `WithPartitionKey` permite incluir la [clave de partición](xref:core/providers/cosmos/index#partition-keys) de Cosmos en el modelo y en las consultas.
* Ahora se traducen los métodos de cadena [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) y [`EndsWith`](/dotnet/api/system.string.endswith) para Cosmos.
* El operador `is` de C# ahora se traduce en Cosmos.

### <a name="sqlite"></a>SQLite

* Ahora se admiten columnas calculadas.
* La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.
* La inicialización de SqliteConnection ahora es diferida.

### <a name="other"></a>Otros

* Se pueden generar proxies de seguimiento de cambios que implementen automáticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) y [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged). Esto proporciona un enfoque alternativo para el seguimiento de cambios que no busca cambios cuando se llama a `SaveChanges`.
* Ahora se puede cambiar un objeto <xref:System.Data.Common.DbConnection> o una cadena de conexión en una instancia ya inicializada de DbContext.
* El nuevo método <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) borra la instancia de DbContext de todas las entidades sometidas a seguimiento. Normalmente, esto no es necesario si se aplica el procedimiento recomendado de crear una nueva instancia de contexto de corta duración para cada unidad de trabajo. Pero si es necesario restablecer el estado de una instancia de DbContext, el uso del nuevo método `Clear()` es más eficaz y robusto que la separación masiva de todas las entidades.
* Las herramientas de línea de comandos de EF Core ahora configuran automáticamente las variables de entorno `ASPNETCORE_ENVIRONMENT` _y_ `DOTNET_ENVIRONMENT` en "desarrollo". Esto aporta la experiencia al usar el host genérico en línea con la experiencia de ASP.NET Core durante el desarrollo.
* Los argumentos personalizados de la línea de comandos pueden fluir en <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, lo que permite que las aplicaciones controlen cómo se crea e inicializa el contexto.
* Ahora, el factor de relleno de índices se puede [configurar en SQL Server](xref:core/providers/sql-server/indexes#fill-factor).
* La nueva propiedad <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> se puede usar para distinguir cuándo se usa un proveedor relacional o uno no relacional (como InMemory).
