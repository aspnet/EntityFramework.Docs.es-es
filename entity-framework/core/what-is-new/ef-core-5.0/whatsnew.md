---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 3a1f5c7d44ad0e4d648492c4edcf14678c73538e
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847601"
---
# <a name="whats-new-in-ef-core-50"></a>Novedades en EF Core 5.0

EF Core 5.0 está actualmente en desarrollo. Esta página contendrá información general sobre los cambios interesantes introducidos en cada versión preliminar.

Esta página no duplica el [plan para EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan). En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.

A medida que se publique el contenido, se agregarán vínculos que redirigirán de esta página a la documentación oficial.

## <a name="preview-8"></a>Versión preliminar 8

## <a name="table-per-type-tpt-mapping"></a>Asignación de tabla por tipo (TPT)

De forma predeterminada, EF Core asigna una jerarquía de herencia de tipos .NET a una tabla de base de datos única. Esto se conoce como asignación de tabla por jerarquía (TPH). EF Core 5.0 también permite asignar cada tipo .NET de una jerarquía de herencia a una tabla de base de datos diferente; esto se conoce como asignación de tabla por tipo (TPT).

Por ejemplo, piense en este modelo con una jerarquía asignada:

```c#
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
    public string EdcuationLevel { get; set; }
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

```c#
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

```c#
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

```c#
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

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

Estos tipos se deben incluir en el modelo de EF Core:

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

Observe que `Report` no tiene ninguna clave principal, por lo que debe configurarse como tal.

Por último, se debe asignar un método .NET a la función TVF en la base de datos. Este método se puede definir en DbContext mediante el método `FromExpression` nuevo:

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

Este método usa un parámetro y un tipo de valor devuelto que coinciden con la función TVF definida anteriormente. Después, el método se agrega al modelo de EF Core en OnModelCreating:

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

(Usar una expresión lambda aquí es una manera sencilla de pasar `MethodInfo` a EF Core. Se omiten los argumentos que se pasan al método).

Ahora podemos escribir consultas que llamen a `GetReports` y se creen sobre los resultados. Por ejemplo:

```c#
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

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

De forma predeterminada, EF Core consultará desde la vista y enviará actualizaciones a la tabla. Por ejemplo, ejecutar el código siguiente:

```c#
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

```c#
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

EF Core admite ahora [puntos de retorno](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) para un mayor control sobre las transacciones que ejecutan varias operaciones.

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

```CSharp
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

```CSharp
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

```CSharp
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

```CSharp
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

```CSharp
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

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core producirá la siguiente excepción que indica que se produjo un error de traducción porque no se asignó `IsSigned`:

> Excepción no controlada. System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.IsSigned)". Información adicional: Error al traducir el miembro "IsSigned" en el tipo de entidad "Artist". Es posible que el miembro especificado no se haya asignado. Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync(). Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.

Del mismo modo, ahora se generan mejores mensajes de excepción al intentar traducir comparaciones de cadenas con una semántica dependiente de la referencia cultural. Por ejemplo, esta consulta intenta usar `StringComparison.CurrentCulture`:

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

Ahora, EF Core producirá la siguiente excepción:

> Excepción no controlada. System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.Name.Equals(value: 'The Unicorns', comparisonType: CurrentCulture))". Información adicional: No se admite la traducción del método "string.Equals" que usa el argumento "StringComparison". Vea https://go.microsoft.com/fwlink/?linkid=2129535 para obtener más información. Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync(). Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.

### <a name="specify-transaction-id"></a>Especificación del id. de la transacción

El miembro de la comunidad [@Marusyk](https://github.com/Marusyk) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

EF Core expone un id. de transacción para la correlación de las transacciones entre llamadas. Este id. lo establece normalmente EF Core cuando se inicia una transacción. Si es la aplicación la que inicia la transacción, esta característica permite que la aplicación establezca de forma explícita el id. de transacción para que se ponga en correlación de forma correcta en todas las ubicaciones donde se use. Por ejemplo:

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>Asignación de IPAddress

El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

Ahora, la [clase estándar IPAddress](/dotnet/api/system.net.ipaddress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa. Por ejemplo, valore la posibilidad de asignar este tipo de entidad:

```CSharp
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

```CSharp
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

Tenga en cuenta que se recomienda usar [una cadena de conexión con nombre y almacenamiento seguro, como Secretos de usuario](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).

### <a name="translations-for-firstordefault-on-strings"></a>Traducciones de FirstOrDefault en las cadenas

El miembro de la comunidad [@dvoreckyaa](https://github.com/dvoreckyaa) ha contribuido en el desarrollo de esta característica. Muchas gracias por la aportación.

Ahora se traducen FirstOrDefault y operadores similares para los caracteres de las cadenas. Por ejemplo, esta consulta LINQ:

```CSharp
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

```CSharp
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

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

A continuación, las migraciones generan lo siguiente para crear la base de datos en SQL Server:

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

También se puede especificar la intercalación que se va a usar para columnas de bases de datos concretas. Por ejemplo:

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

En el caso de las que no usan migraciones, se aplica a las intercalaciones ingeniería inversa desde la base de datos, al aplicar scaffolding a DbContext.

Por último, `EF.Functions.Collate()` permite realizar consultas ad-hoc mediante diferentes intercalaciones. Por ejemplo:

```CSharp
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

Los argumentos se transmiten ahora desde la línea de comandos al método de `CreateDbContext` de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1). Por ejemplo, para indicar que se trata de una compilación de desarrollo, se puede pasar un argumento personalizado (por ejemplo, `dev`) en la línea de comandos:

```
dotnet ef migrations add two --verbose --dev
```

Este argumento pasará entonces a la factoría, donde se puede usar para controlar cómo se crea e inicializa el contexto. Por ejemplo:

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

En el problema [n.º 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) se hace el seguimiento de la documentación.

### <a name="no-tracking-queries-with-identity-resolution"></a>Consultas de no seguimiento con resolución de la identidad

Ahora se pueden configurar consultas de no seguimiento para hacer la resolución de identidad. Por ejemplo, la siguiente consulta creará una instancia de Blog para cada Post, aunque cada blog tenga la misma clave principal.

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Sin embargo, a costa de que por lo general sea ligeramente más lento y siempre use más memoria, se puede cambiar esta consulta para asegurarse de que solo se crea una instancia de Blog:

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Tenga en cuenta que esto solo es útil para consultas de no seguimiento, ya que todas las consultas de seguimiento ya muestran este comportamiento. Además, después de la revisión de la API, se cambiará la sintaxis de `PerformIdentityResolution`. Consulte el problema [n.º 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

En el problema [n.º 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) se realiza el seguimiento de la documentación.

### <a name="stored-persisted-computed-columns"></a>Columnas calculadas almacenadas (persistentes)

La mayoría de las bases de datos permiten almacenar valores de columnas calculadas después del cálculo. Aunque ocupa espacio en disco, la columna se calcula solo una vez al actualizar, en lugar de cada vez que se recupera su valor. Esto también permite indizar la columna para algunas bases de datos.

EF Core 5.0 permite configurar las columnas calculadas como almacenadas. Por ejemplo:

```CSharp
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

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

La precisión y la escala todavía se pueden establecer mediante el tipo completo de base de datos, como "decimal (16, 4)".

En el problema [n.º 527](https://github.com/dotnet/EntityFramework.Docs/issues/527) se realiza el seguimiento de la documentación.

### <a name="specify-sql-server-index-fill-factor"></a>Especificación del factor de relleno del índice de SQL Server

Ahora se puede especificar el factor de relleno al crear un índice en SQL Server. Por ejemplo:

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Versión preliminar 3

### <a name="filtered-include"></a>Inclusión filtrada

El método Include ahora admite el filtrado de las entidades incluidas. Por ejemplo:

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".

Para reducir el número de las entidades incluidas, se puede usar Skip y Take. Por ejemplo:

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
Esta consulta devolverá los blogs que tengan cinco publicaciones como máximo.

Vea la [documentación de Include](xref:core/querying/related-data#filtered-include) para obtener información completa.

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nueva API de ModelBuilder para las propiedades de navegación

Las propiedades de navegación se configuran principalmente al [definir relaciones](xref:core/modeling/relationships). No obstante, se puede usar el nuevo método `Navigation` cuando es necesario realizar una configuración adicional en las propiedades de navegación. Por ejemplo, para establecer un campo de respaldo de la navegación cuando el campo no se encuentre por convención:

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Tenga en cuenta que la API de `Navigation` no reemplaza la configuración de la relación, sino que permite la configuración adicional de las propiedades de navegación en relaciones ya detectadas o definidas.

Consulte la [documentación sobre la configuración de las propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nuevos parámetros de la línea de comandos para espacios de nombres y cadenas de conexión

Ahora, las migraciones y el scaffolding permiten especificar los espacios de nombres en la línea de comandos. Esto puede hacerse para, por ejemplo, aplicar ingeniería inversa a una base de datos y colocar las clases Context y Model en diferentes espacios de nombres:

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

Para más información, consulte la documentación sobre [migraciones](xref:core/managing-schemas/migrations/index#namespaces) e [ingeniería inversa](xref:core/managing-schemas/scaffolding#directories-and-namespaces).

---
Ahora, además, también puede pasarse una cadena de conexión al comando `database-update`:

```
dotnet ef database update --connection "connection string"
```

Para más información, consulte la [documentación sobre herramientas](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).

También se han agregado parámetros equivalentes a los comandos de PowerShell que se usan en la consola del administrador de paquetes de VS.

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors ha vuelto

Por motivos de rendimiento, EF no realiza comprobaciones de valores NULL adicionales cuando se leen los valores de la base de datos. Esto puede dar lugar a excepciones cuya causa principal es difícil de rastrear cuando se encuentra un valor NULL inesperado.

El uso de `EnableDetailedErrors` agregará una comprobación de valores NULL adicional a las consultas de modo que, para una pequeña sobrecarga de rendimiento, estos errores sean más fáciles de rastrear para averiguar su causa principal.

Por ejemplo:
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

En el problema [n.º 955](https://github.com/dotnet/EntityFramework.Docs/issues/955) se realiza el seguimiento de la documentación.

### <a name="cosmos-partition-keys"></a>Claves de partición de Cosmos

Ahora, es posible especificar en las consultas la clave de partición que va a usarse para una consulta determinada. Por ejemplo:

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

En el problema [n.º 2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) se realiza el seguimiento de la documentación.

### <a name="support-for-the-sql-server-datalength-function"></a>Compatibilidad con la función DATALENGTH de SQL Server

Se puede acceder con el nuevo método `EF.Functions.DataLength`. Por ejemplo:
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Versión preliminar 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Uso de un atributo de C# para especificar un campo de respaldo de propiedad

Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad. Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente. Por ejemplo:

```CSharp
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

EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](/ef/core/modeling/inheritance). Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador. EF Core 5.0 ahora implementa estas mejoras.

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

```CSharp
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

Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) y [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1). A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios. Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.

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

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.

### <a name="isrelational"></a>IsRelational

Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`. Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales. Por ejemplo:

```CSharp
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

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](/ef/core/saving/concurrency), por ejemplo, para implementar reintentos.

En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.

### <a name="query-translations-for-more-datetime-constructs"></a>Traducciones de consultas para más construcciones DateTime

Ahora las consultas que contienen la nueva construcción DateTime se traducen.

Además, ahora se asignan las funciones de SQL Server que hay a continuación:

* DateDiffWeek
* DateFromParts

Por ejemplo:

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.

### <a name="query-translations-for-more-byte-array-constructs"></a>Traducciones de consultas para más construcciones de matriz de bytes

Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.

La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.

### <a name="query-translation-for-reverse"></a>Traducción de consultas para Reverse

Las consultas que usan `Reverse` ahora se traducen. Por ejemplo:

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.

### <a name="query-translation-for-bitwise-operators"></a>Traducción de consultas para operadores bit a bit

Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.

### <a name="query-translation-for-strings-on-cosmos"></a>Traducción de consultas para cadenas en Cosmos

Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.

En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.
