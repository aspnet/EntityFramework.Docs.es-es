---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: f822e3ae776778749a654377cbd9d9814ca40972
ms.sourcegitcommit: 12d257db4786487a0c28e9ddd79f176f7cf6edb1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2020
ms.locfileid: "89043602"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="d9a77-103">Novedades en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d9a77-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="d9a77-104">EF Core 5.0 está actualmente en desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d9a77-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="d9a77-105">Esta página contendrá información general sobre los cambios interesantes introducidos en cada versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="d9a77-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="d9a77-106">Esta página no duplica el [plan para EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="d9a77-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="d9a77-107">En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.</span><span class="sxs-lookup"><span data-stu-id="d9a77-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="d9a77-108">A medida que se publique el contenido, se agregarán vínculos que redirigirán de esta página a la documentación oficial.</span><span class="sxs-lookup"><span data-stu-id="d9a77-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-8"></a><span data-ttu-id="d9a77-109">Versión preliminar 8</span><span class="sxs-lookup"><span data-stu-id="d9a77-109">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="d9a77-110">Asignación de tabla por tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="d9a77-110">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="d9a77-111">De forma predeterminada, EF Core asigna una jerarquía de herencia de tipos .NET a una tabla de base de datos única.</span><span class="sxs-lookup"><span data-stu-id="d9a77-111">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="d9a77-112">Esto se conoce como asignación de tabla por jerarquía (TPH).</span><span class="sxs-lookup"><span data-stu-id="d9a77-112">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="d9a77-113">EF Core 5.0 también permite asignar cada tipo .NET de una jerarquía de herencia a una tabla de base de datos diferente; esto se conoce como asignación de tabla por tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="d9a77-113">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="d9a77-114">Por ejemplo, piense en este modelo con una jerarquía asignada:</span><span class="sxs-lookup"><span data-stu-id="d9a77-114">For example, consider this model with a mapped hierarchy:</span></span>

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

<span data-ttu-id="d9a77-115">De forma predeterminada, EF Core lo asignará a una tabla única:</span><span class="sxs-lookup"><span data-stu-id="d9a77-115">By default, EF Core will map this to a single table:</span></span>

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

<span data-ttu-id="d9a77-116">Sin embargo, la asignación de cada tipo de entidad a una tabla diferente dará como resultado en su lugar una tabla por tipo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-116">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

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

<span data-ttu-id="d9a77-117">Tenga en cuenta que la creación de las restricciones de clave externa mostradas anteriormente se han agregado después de bifurcar el código para la versión preliminar 8.</span><span class="sxs-lookup"><span data-stu-id="d9a77-117">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="d9a77-118">Los tipos de entidad pueden asignarse a tablas diferentes mediante atributos de asignación:</span><span class="sxs-lookup"><span data-stu-id="d9a77-118">Entity types can be mapped to different tables using mapping attributes:</span></span>

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

<span data-ttu-id="d9a77-119">O mediante la configuración `ModelBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d9a77-119">Or using `ModelBuilder` configuration:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="d9a77-120">El seguimiento de la documentación se realiza mediante el número de incidencia [1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span><span class="sxs-lookup"><span data-stu-id="d9a77-120">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="d9a77-121">Migraciones: recompilación de tablas de SQLite</span><span class="sxs-lookup"><span data-stu-id="d9a77-121">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="d9a77-122">En comparación con otras bases de datos, SQLite está relativamente limitada en sus capacidades de manipulación de esquemas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-122">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="d9a77-123">Por ejemplo, quitar una columna de una tabla existente requiere quitar y volver a crear la tabla completa.</span><span class="sxs-lookup"><span data-stu-id="d9a77-123">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="d9a77-124">Las migraciones de EF Core 5.0 ahora admiten la recompilación automática de la tabla para los cambios de esquema que la necesiten.</span><span class="sxs-lookup"><span data-stu-id="d9a77-124">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="d9a77-125">Por ejemplo, imagine que tenemos una tabla `Unicorns` creada para un tipo de entidad `Unicorn`:</span><span class="sxs-lookup"><span data-stu-id="d9a77-125">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

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

<span data-ttu-id="d9a77-126">Posteriormente, veremos que el almacenamiento de la edad de un unicornio se considera muy superficial, así que vamos a quitar esa propiedad, a agregar una nueva migración y a actualizar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-126">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="d9a77-127">Esta actualización producirá un error al usar EF Core 3.1 porque no se puede quitar la columna.</span><span class="sxs-lookup"><span data-stu-id="d9a77-127">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="d9a77-128">En EF Core 5.0, en lugar de eso, las migraciones recompilarán la tabla:</span><span class="sxs-lookup"><span data-stu-id="d9a77-128">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

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

<span data-ttu-id="d9a77-129">Tenga en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d9a77-129">Notice that:</span></span>
* <span data-ttu-id="d9a77-130">Se crea una tabla temporal con el esquema deseado para la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="d9a77-130">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="d9a77-131">Los datos se copian de la tabla actual en la tabla temporal.</span><span class="sxs-lookup"><span data-stu-id="d9a77-131">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="d9a77-132">La aplicación de clave externa está desactivada.</span><span class="sxs-lookup"><span data-stu-id="d9a77-132">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="d9a77-133">Se quita la tabla actual.</span><span class="sxs-lookup"><span data-stu-id="d9a77-133">The current table is dropped</span></span>
* <span data-ttu-id="d9a77-134">Se cambia el nombre de la tabla temporal para hacerla la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="d9a77-134">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="d9a77-135">El seguimiento de la documentación se realiza mediante el número de incidencia [2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span><span class="sxs-lookup"><span data-stu-id="d9a77-135">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="d9a77-136">Funciones con valores de tabla</span><span class="sxs-lookup"><span data-stu-id="d9a77-136">Table-valued functions</span></span>

<span data-ttu-id="d9a77-137">El miembro de la comunidad [@pmiddleton](https://github.com/pmiddleton) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="d9a77-137">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="d9a77-138">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-138">Many thanks for the contribution!</span></span>

<span data-ttu-id="d9a77-139">EF Core 5.0 incluye compatibilidad de primera clase para asignar métodos .NET a funciones con valores de tabla (TVF).</span><span class="sxs-lookup"><span data-stu-id="d9a77-139">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="d9a77-140">Estas funciones se pueden usar en consultas LINQ, donde la composición adicional de los resultados de la función también se traducirá a SQL.</span><span class="sxs-lookup"><span data-stu-id="d9a77-140">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="d9a77-141">Por ejemplo, considere esta TVF definida en una base de datos SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d9a77-141">For example, consider this TVF defined in a SQL Server database:</span></span>

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

<span data-ttu-id="d9a77-142">El modelo de EF Core requiere dos tipos de entidad para usar esta TVF:</span><span class="sxs-lookup"><span data-stu-id="d9a77-142">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="d9a77-143">Un tipo `Employee` que se asigna a la tabla Employees de la forma habitual.</span><span class="sxs-lookup"><span data-stu-id="d9a77-143">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="d9a77-144">Un tipo `Report` que coincide con la forma que devuelve la función TVF.</span><span class="sxs-lookup"><span data-stu-id="d9a77-144">A `Report` type that matches the shape returned by the TVF</span></span>

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

<span data-ttu-id="d9a77-145">Estos tipos se deben incluir en el modelo de EF Core:</span><span class="sxs-lookup"><span data-stu-id="d9a77-145">These types must be included in the EF Core model:</span></span>

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="d9a77-146">Observe que `Report` no tiene ninguna clave principal, por lo que debe configurarse como tal.</span><span class="sxs-lookup"><span data-stu-id="d9a77-146">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="d9a77-147">Por último, se debe asignar un método .NET a la función TVF en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-147">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="d9a77-148">Este método se puede definir en DbContext mediante el método `FromExpression` nuevo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-148">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="d9a77-149">Este método usa un parámetro y un tipo de valor devuelto que coinciden con la función TVF definida anteriormente.</span><span class="sxs-lookup"><span data-stu-id="d9a77-149">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="d9a77-150">Después, el método se agrega al modelo de EF Core en OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="d9a77-150">The method is then added to the EF Core model in OnModelCreating:</span></span>

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="d9a77-151">(Usar una expresión lambda aquí es una manera sencilla de pasar `MethodInfo` a EF Core.</span><span class="sxs-lookup"><span data-stu-id="d9a77-151">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="d9a77-152">Se omiten los argumentos que se pasan al método).</span><span class="sxs-lookup"><span data-stu-id="d9a77-152">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="d9a77-153">Ahora podemos escribir consultas que llamen a `GetReports` y se creen sobre los resultados.</span><span class="sxs-lookup"><span data-stu-id="d9a77-153">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="d9a77-154">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-154">For example:</span></span>

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

<span data-ttu-id="d9a77-155">En SQL Server, esto se traduce en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d9a77-155">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="d9a77-156">Observe que SQL se ha modificado en la tabla `Employees`, llama a `GetReports` y, luego, agrega una cláusula WHERE adicional a los resultados de la función.</span><span class="sxs-lookup"><span data-stu-id="d9a77-156">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="d9a77-157">Asignación flexible de consultas y actualizaciones</span><span class="sxs-lookup"><span data-stu-id="d9a77-157">Flexible query/update mapping</span></span>

<span data-ttu-id="d9a77-158">EF Core 5.0 permite asignar el mismo tipo de entidad a objetos de base de datos diferentes.</span><span class="sxs-lookup"><span data-stu-id="d9a77-158">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="d9a77-159">Estos objetos pueden ser tablas, vistas o funciones.</span><span class="sxs-lookup"><span data-stu-id="d9a77-159">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="d9a77-160">Por ejemplo, un tipo de entidad se puede asignar a una vista de base de datos y una tabla de base de datos:</span><span class="sxs-lookup"><span data-stu-id="d9a77-160">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="d9a77-161">De forma predeterminada, EF Core consultará desde la vista y enviará actualizaciones a la tabla.</span><span class="sxs-lookup"><span data-stu-id="d9a77-161">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="d9a77-162">Por ejemplo, ejecutar el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d9a77-162">For example, executing the following code:</span></span>

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="d9a77-163">Da como resultado una consulta en la vista y, luego, una actualización de la tabla:</span><span class="sxs-lookup"><span data-stu-id="d9a77-163">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="d9a77-164">Configuración de la consulta dividida en todo el contexto</span><span class="sxs-lookup"><span data-stu-id="d9a77-164">Context-wide split-query configuration</span></span>

<span data-ttu-id="d9a77-165">Las consultas divididas (vea más abajo) ahora se pueden configurar como valor predeterminado para cualquier consulta que ejecute DbContext.</span><span class="sxs-lookup"><span data-stu-id="d9a77-165">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="d9a77-166">Esta configuración solo está disponible para los proveedores relacionales, por lo que debe especificarse como parte de la configuración de `UseProvider`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-166">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="d9a77-167">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-167">For example:</span></span>

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="d9a77-168">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="d9a77-168">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="d9a77-169">Asignación de PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="d9a77-169">PhysicalAddress mapping</span></span>

<span data-ttu-id="d9a77-170">El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="d9a77-170">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="d9a77-171">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-171">Many thanks for the contribution!</span></span>

<span data-ttu-id="d9a77-172">Ahora, la [clase PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa.</span><span class="sxs-lookup"><span data-stu-id="d9a77-172">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="d9a77-173">Para obtener más información, vea más abajo los ejemplos para `IPAddress`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-173">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="d9a77-174">Versión preliminar 7</span><span class="sxs-lookup"><span data-stu-id="d9a77-174">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="d9a77-175">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="d9a77-175">DbContextFactory</span></span>

<span data-ttu-id="d9a77-176">EF Core 5.0 presenta `AddDbContextFactory` y `AddPooledDbContextFactory` para registrar una factoría que crea instancias de DbContext en el contenedor de inserción de dependencias (DI) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-176">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="d9a77-177">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-177">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="d9a77-178">Los servicios de aplicaciones, como los controladores de ASP.NET Core, pueden depender de `IDbContextFactory<TContext>` en el constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="d9a77-178">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="d9a77-179">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-179">For example:</span></span>

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

<span data-ttu-id="d9a77-180">A continuación, las instancias de DbContext se pueden crear y usar según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="d9a77-180">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="d9a77-181">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-181">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="d9a77-182">Tenga en cuenta que las instancias de DbContext creadas de este modo _no_ están administradas por el proveedor de servicios de la aplicación y, por lo tanto, la aplicación debe desecharlas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-182">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="d9a77-183">Este desacoplamiento es muy útil para las aplicaciones Blazor, en las que se recomienda usar `IDbContextFactory`, pero también puede ser práctico en otros escenarios.</span><span class="sxs-lookup"><span data-stu-id="d9a77-183">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="d9a77-184">Las instancias DbContext se pueden agrupar mediante una llamada a `AddPooledDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-184">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="d9a77-185">Esta agrupación funciona de la misma manera que para `AddDbContextPool`, y también tiene las mismas limitaciones.</span><span class="sxs-lookup"><span data-stu-id="d9a77-185">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="d9a77-186">En el problema [n.º 2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-186">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="d9a77-187">Restablecimiento del estado de DbContext</span><span class="sxs-lookup"><span data-stu-id="d9a77-187">Reset DbContext state</span></span>

<span data-ttu-id="d9a77-188">EF Core 5.0 presenta `ChangeTracker.Clear()`, que borra la instancia de DbContext de todas las entidades de las que se ha realizado un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="d9a77-188">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="d9a77-189">Normalmente, esto no es necesario si se aplica el procedimiento recomendado de crear una nueva instancia de contexto de corta duración para cada unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="d9a77-189">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="d9a77-190">No obstante, si es necesario restablecer el estado de una instancia de DbContext, el uso del método `Clear()` es más eficaz y robusto que la separación masiva de todas las entidades.</span><span class="sxs-lookup"><span data-stu-id="d9a77-190">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="d9a77-191">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="d9a77-191">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="d9a77-192">Nuevo patrón para los valores predeterminados generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="d9a77-192">New pattern for store-generated defaults</span></span>

<span data-ttu-id="d9a77-193">EF Core permite establecer un valor explícito para una columna que también puede tener una restricción de valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d9a77-193">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="d9a77-194">EF Core usa el valor predeterminado de CLR de tipo de propiedad Type como centinela para este. Si el valor no es el predeterminado de CLR, se inserta; de lo contrario, se usa el valor predeterminado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-194">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="d9a77-195">Esto crea problemas en los tipos en los que el valor predeterminado de CLR no es un buen centinela, en particular, las propiedades `bool`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-195">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="d9a77-196">EF Core 5.0 ahora permite que la variable de respaldo acepte valores NULL para los casos como este.</span><span class="sxs-lookup"><span data-stu-id="d9a77-196">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="d9a77-197">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-197">For example:</span></span>

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

<span data-ttu-id="d9a77-198">Tenga en cuenta que la variable de respaldo admite valores NULL, pero la propiedad expuesta públicamente, no.</span><span class="sxs-lookup"><span data-stu-id="d9a77-198">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="d9a77-199">Esto permite que el valor de centinela sea `null` sin afectar a la superficie pública del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="d9a77-199">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="d9a77-200">En este caso, si `IsValid` nunca se establece, se usará el valor predeterminado de la base de datos, ya que el campo de respaldo sigue siendo NULL.</span><span class="sxs-lookup"><span data-stu-id="d9a77-200">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="d9a77-201">Si se establece `true` o `false`, este valor se guarda explícitamente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-201">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="d9a77-202">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="d9a77-202">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="d9a77-203">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="d9a77-203">Cosmos partition keys</span></span>

<span data-ttu-id="d9a77-204">EF Core permite que la clave de partición de Cosmos se incluya en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="d9a77-204">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="d9a77-205">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-205">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="d9a77-206">A partir de la versión preliminar 7, la clave de partición se incluye en la clave pública del tipo de entidad y se usa para mejorar el rendimiento en algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-206">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="d9a77-207">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="d9a77-207">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="d9a77-208">Configuración de Cosmos</span><span class="sxs-lookup"><span data-stu-id="d9a77-208">Cosmos configuration</span></span>

<span data-ttu-id="d9a77-209">EF Core 5.0 mejora la configuración de Cosmos y de las conexiones Cosmos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-209">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="d9a77-210">Anteriormente, EF Core requería especificar explícitamente el punto de conexión y la clave al conectarse a una base de datos de Cosmos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-210">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="d9a77-211">En su lugar, EF Core 5.0 permite usar una cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="d9a77-211">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="d9a77-212">Además, EF Core 5.0 permite establecer explícitamente la instancia de WebProxy.</span><span class="sxs-lookup"><span data-stu-id="d9a77-212">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="d9a77-213">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-213">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="d9a77-214">Ahora también se pueden configurar muchos otros valores de tiempo de espera, límites, etc.</span><span class="sxs-lookup"><span data-stu-id="d9a77-214">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="d9a77-215">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-215">For example:</span></span>

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

<span data-ttu-id="d9a77-216">Por último, el modo de conexión predeterminado es ahora `ConnectionMode.Gateway`, que suele ofrecer mayor compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="d9a77-216">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="d9a77-217">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="d9a77-217">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="d9a77-218">Singularización en scaffolding de DbContext</span><span class="sxs-lookup"><span data-stu-id="d9a77-218">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="d9a77-219">Anteriormente, al aplicar scaffolding a un elemento DbContext de una base de datos existente, EF Core creaba nombres de tipo de entidad que coincidían con los de las tablas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-219">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="d9a77-220">Por ejemplo, las tablas `People` y `Addresses` producían tipos de entidad denominados `People` y `Addresses`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-220">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="d9a77-221">En versiones anteriores, este comportamiento se podía configurar mediante el registro de un servicio de pluralización.</span><span class="sxs-lookup"><span data-stu-id="d9a77-221">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="d9a77-222">Ahora, en EF Core 5.0, el paquete [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) se usa como servicio de pluralización predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d9a77-222">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="d9a77-223">Esto significa que, ahora, en las tablas `People` y `Addresses`, se aplicarán técnicas de ingeniería inversa para obtener los tipos de entidad denominados `Person` y `Address`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-223">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="d9a77-224">Puntos de retorno</span><span class="sxs-lookup"><span data-stu-id="d9a77-224">Savepoints</span></span>

<span data-ttu-id="d9a77-225">EF Core admite ahora [puntos de retorno](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) para un mayor control sobre las transacciones que ejecutan varias operaciones.</span><span class="sxs-lookup"><span data-stu-id="d9a77-225">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="d9a77-226">Los puntos de retorno se pueden crear, liberar y revertir manualmente.</span><span class="sxs-lookup"><span data-stu-id="d9a77-226">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="d9a77-227">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-227">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="d9a77-228">Además, EF Core se revierte ahora al último punto de retorno cuando se produce un error al ejecutar `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-228">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="d9a77-229">Esto permite que se vuelva a intentar SaveChanges sin volver a probar toda la transacción.</span><span class="sxs-lookup"><span data-stu-id="d9a77-229">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="d9a77-230">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="d9a77-230">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="d9a77-231">Versión preliminar 6</span><span class="sxs-lookup"><span data-stu-id="d9a77-231">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="d9a77-232">División de consultas para colecciones relacionadas</span><span class="sxs-lookup"><span data-stu-id="d9a77-232">Split queries for related collections</span></span>

<span data-ttu-id="d9a77-233">A partir de EF Core 3.0, EF Core siempre genera una única consulta SQL para cada consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="d9a77-233">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="d9a77-234">Esto garantiza la coherencia de los datos devueltos de acuerdo con las restricciones del modo de transacción en uso.</span><span class="sxs-lookup"><span data-stu-id="d9a77-234">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="d9a77-235">Sin embargo, esta operación puede ser muy lenta si la consulta utiliza `Include` o una proyección para devolver varias colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-235">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="d9a77-236">Ahora, EF Core 5.0 permite dividir en varias consultas SQL una única consulta LINQ con colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-236">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="d9a77-237">Esto puede mejorar de forma significativa el rendimiento, pero puede dar lugar a incoherencias en los resultados devueltos si los datos de las dos consultas son diferentes.</span><span class="sxs-lookup"><span data-stu-id="d9a77-237">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="d9a77-238">Las transacciones serializables o de instantáneas se pueden usar para mitigar esta situación y lograr la coherencia con las consultas divididas, pero esto puede conllevar otros costos de rendimiento y diferencias de comportamiento.</span><span class="sxs-lookup"><span data-stu-id="d9a77-238">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="d9a77-239">División de consultas con Include</span><span class="sxs-lookup"><span data-stu-id="d9a77-239">Split queries with Include</span></span>

<span data-ttu-id="d9a77-240">Imaginemos, por ejemplo, que tenemos una consulta que extrae dos niveles de colecciones relacionadas mediante `Include`:</span><span class="sxs-lookup"><span data-stu-id="d9a77-240">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="d9a77-241">De forma predeterminada, EF Core generará el siguiente código SQL al usar el proveedor SQLite:</span><span class="sxs-lookup"><span data-stu-id="d9a77-241">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="d9a77-242">La nueva API `AsSplitQuery` se puede usar para cambiar este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="d9a77-242">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="d9a77-243">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-243">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="d9a77-244">AsSplitQuery está disponible para todos los proveedores de bases de datos relacionales y se puede usar en cualquier parte de la consulta, igual que AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="d9a77-244">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="d9a77-245">EF Core generará ahora las tres consultas SQL siguientes:</span><span class="sxs-lookup"><span data-stu-id="d9a77-245">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="d9a77-246">Se admiten todas las operaciones en la raíz de la consulta.</span><span class="sxs-lookup"><span data-stu-id="d9a77-246">All operations on the query root are supported.</span></span> <span data-ttu-id="d9a77-247">Esto incluye las operaciones OrderBy, Skip, Take y Join, así como FirstOrDefault y operaciones de selección similares de resultado único.</span><span class="sxs-lookup"><span data-stu-id="d9a77-247">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="d9a77-248">Tenga en cuenta que Inclusión filtrada con OrderBy, Skip o Take no se admite en la versión preliminar 6, pero está disponible en las compilaciones diarias y se incluirá en la versión preliminar 7.</span><span class="sxs-lookup"><span data-stu-id="d9a77-248">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="d9a77-249">División de consultas con proyecciones de colecciones</span><span class="sxs-lookup"><span data-stu-id="d9a77-249">Split queries with collection projections</span></span>

<span data-ttu-id="d9a77-250">`AsSplitQuery` también se puede usar cuando se carguen colecciones en proyecciones.</span><span class="sxs-lookup"><span data-stu-id="d9a77-250">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="d9a77-251">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-251">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="d9a77-252">Esta consulta LINQ genera las dos consultas SQL siguientes cuando se usa el proveedor de SQLite:</span><span class="sxs-lookup"><span data-stu-id="d9a77-252">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="d9a77-253">Tenga en cuenta que solo se admite la materialización de la colección.</span><span class="sxs-lookup"><span data-stu-id="d9a77-253">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="d9a77-254">Las composiciones posteriores a `e.Albums` en el caso anterior no generarán consultas divididas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-254">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="d9a77-255">El número de seguimiento de las mejoras de esta área es el [21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="d9a77-255">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="d9a77-256">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="d9a77-256">IndexAttribute</span></span>

<span data-ttu-id="d9a77-257">El nuevo IndexAttribute se puede colocar en un tipo de entidad a fin de especificar un índice para una sola columna.</span><span class="sxs-lookup"><span data-stu-id="d9a77-257">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="d9a77-258">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-258">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="d9a77-259">Para SQL Server, las migraciones generarán el siguiente código SQL:</span><span class="sxs-lookup"><span data-stu-id="d9a77-259">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="d9a77-260">IndexAttribute también se puede usar para especificar un índice que abarque varias columnas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-260">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="d9a77-261">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-261">For example:</span></span>

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

<span data-ttu-id="d9a77-262">Para SQL Server, esto dará como resultado lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d9a77-262">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="d9a77-263">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="d9a77-263">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="d9a77-264">Mejora de las excepciones de traducción de las consultas</span><span class="sxs-lookup"><span data-stu-id="d9a77-264">Improved query translation exceptions</span></span>

<span data-ttu-id="d9a77-265">Seguimos mejorando los mensajes de excepción que se generan cuando se produce un error en la traducción de una consulta.</span><span class="sxs-lookup"><span data-stu-id="d9a77-265">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="d9a77-266">Por ejemplo, esta consulta usa la propiedad no asignada `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="d9a77-266">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="d9a77-267">EF Core producirá la siguiente excepción que indica que se produjo un error de traducción porque no se asignó `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="d9a77-267">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="d9a77-268">Excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="d9a77-268">Unhandled exception.</span></span> <span data-ttu-id="d9a77-269">System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.IsSigned)".</span><span class="sxs-lookup"><span data-stu-id="d9a77-269">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="d9a77-270">Información adicional: Error al traducir el miembro "IsSigned" en el tipo de entidad "Artist".</span><span class="sxs-lookup"><span data-stu-id="d9a77-270">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="d9a77-271">Es posible que el miembro especificado no se haya asignado.</span><span class="sxs-lookup"><span data-stu-id="d9a77-271">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="d9a77-272">Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync().</span><span class="sxs-lookup"><span data-stu-id="d9a77-272">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="d9a77-273">Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d9a77-273">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="d9a77-274">Del mismo modo, ahora se generan mejores mensajes de excepción al intentar traducir comparaciones de cadenas con una semántica dependiente de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d9a77-274">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="d9a77-275">Por ejemplo, esta consulta intenta usar `StringComparison.CurrentCulture`:</span><span class="sxs-lookup"><span data-stu-id="d9a77-275">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="d9a77-276">Ahora, EF Core producirá la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="d9a77-276">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="d9a77-277">Excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="d9a77-277">Unhandled exception.</span></span> <span data-ttu-id="d9a77-278">System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.Name.Equals(value: 'The Unicorns', comparisonType: CurrentCulture))".</span><span class="sxs-lookup"><span data-stu-id="d9a77-278">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="d9a77-279">Información adicional: No se admite la traducción del método "string.Equals" que usa el argumento "StringComparison".</span><span class="sxs-lookup"><span data-stu-id="d9a77-279">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="d9a77-280">Vea https://go.microsoft.com/fwlink/?linkid=2129535 para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d9a77-280">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="d9a77-281">Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync().</span><span class="sxs-lookup"><span data-stu-id="d9a77-281">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="d9a77-282">Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d9a77-282">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="d9a77-283">Especificación del id. de la transacción</span><span class="sxs-lookup"><span data-stu-id="d9a77-283">Specify transaction ID</span></span>

<span data-ttu-id="d9a77-284">El miembro de la comunidad [@Marusyk](https://github.com/Marusyk) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="d9a77-284">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="d9a77-285">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-285">Many thanks for the contribution!</span></span>

<span data-ttu-id="d9a77-286">EF Core expone un id. de transacción para la correlación de las transacciones entre llamadas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-286">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="d9a77-287">Este id. lo establece normalmente EF Core cuando se inicia una transacción.</span><span class="sxs-lookup"><span data-stu-id="d9a77-287">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="d9a77-288">Si es la aplicación la que inicia la transacción, esta característica permite que la aplicación establezca de forma explícita el id. de transacción para que se ponga en correlación de forma correcta en todas las ubicaciones donde se use.</span><span class="sxs-lookup"><span data-stu-id="d9a77-288">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="d9a77-289">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-289">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="d9a77-290">Asignación de IPAddress</span><span class="sxs-lookup"><span data-stu-id="d9a77-290">IPAddress mapping</span></span>

<span data-ttu-id="d9a77-291">El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="d9a77-291">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="d9a77-292">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-292">Many thanks for the contribution!</span></span>

<span data-ttu-id="d9a77-293">Ahora, la [clase estándar IPAddress](/dotnet/api/system.net.ipaddress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa.</span><span class="sxs-lookup"><span data-stu-id="d9a77-293">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="d9a77-294">Por ejemplo, valore la posibilidad de asignar este tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="d9a77-294">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="d9a77-295">En SQL Server, esto hará que las migraciones creen la siguiente tabla:</span><span class="sxs-lookup"><span data-stu-id="d9a77-295">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="d9a77-296">Las entidades se pueden agregar de la manera habitual:</span><span class="sxs-lookup"><span data-stu-id="d9a77-296">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="d9a77-297">Y el código SQL resultante insertará la dirección IPv4 o IPv6 normalizada:</span><span class="sxs-lookup"><span data-stu-id="d9a77-297">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="d9a77-298">Exclusión de OnConfiguring al aplicar scaffolding</span><span class="sxs-lookup"><span data-stu-id="d9a77-298">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="d9a77-299">Cuando se aplica scaffolding a un elemento DbContext desde una base de datos existente, EF Core crea de forma predeterminada una sobrecarga de OnConfiguring con una cadena de conexión para que el contexto se pueda usar de inmediato.</span><span class="sxs-lookup"><span data-stu-id="d9a77-299">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="d9a77-300">Sin embargo, esto no es útil si ya tiene una clase parcial con OnConfiguring o si está configurando el contexto de otra manera.</span><span class="sxs-lookup"><span data-stu-id="d9a77-300">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="d9a77-301">Para solucionar esto, ahora se puede indicar a los comandos de scaffolding que omitan la generación de OnConfiguring.</span><span class="sxs-lookup"><span data-stu-id="d9a77-301">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="d9a77-302">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-302">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="d9a77-303">O bien, en la Consola del administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="d9a77-303">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="d9a77-304">Tenga en cuenta que se recomienda usar [una cadena de conexión con nombre y almacenamiento seguro, como Secretos de usuario](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="d9a77-304">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="d9a77-305">Traducciones de FirstOrDefault en las cadenas</span><span class="sxs-lookup"><span data-stu-id="d9a77-305">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="d9a77-306">El miembro de la comunidad [@dvoreckyaa](https://github.com/dvoreckyaa) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="d9a77-306">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="d9a77-307">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-307">Many thanks for the contribution!</span></span>

<span data-ttu-id="d9a77-308">Ahora se traducen FirstOrDefault y operadores similares para los caracteres de las cadenas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-308">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="d9a77-309">Por ejemplo, esta consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="d9a77-309">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="d9a77-310">Se traducirá al siguiente código SQL al usar SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d9a77-310">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="d9a77-311">Simplificación de bloques de casos</span><span class="sxs-lookup"><span data-stu-id="d9a77-311">Simplify case blocks</span></span>

<span data-ttu-id="d9a77-312">Ahora, EF Core genera mejores consultas con bloques de casos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-312">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="d9a77-313">Por ejemplo, esta consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="d9a77-313">For example, this LINQ query:</span></span>

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="d9a77-314">Estaba traducida en SQL Server oficialmente de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="d9a77-314">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="d9a77-315">Pero ahora se traduce así:</span><span class="sxs-lookup"><span data-stu-id="d9a77-315">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="d9a77-316">Versión preliminar 5</span><span class="sxs-lookup"><span data-stu-id="d9a77-316">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="d9a77-317">Intercalaciones de bases de datos</span><span class="sxs-lookup"><span data-stu-id="d9a77-317">Database collations</span></span>

<span data-ttu-id="d9a77-318">La intercalación predeterminada de una base de datos puede especificarse ahora en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="d9a77-318">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="d9a77-319">Este pasará a las migraciones generadas para establecer la intercalación cuando se cree la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-319">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="d9a77-320">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-320">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="d9a77-321">A continuación, las migraciones generan lo siguiente para crear la base de datos en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d9a77-321">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="d9a77-322">También se puede especificar la intercalación que se va a usar para columnas de bases de datos concretas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-322">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="d9a77-323">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-323">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="d9a77-324">En el caso de las que no usan migraciones, se aplica a las intercalaciones ingeniería inversa desde la base de datos, al aplicar scaffolding a DbContext.</span><span class="sxs-lookup"><span data-stu-id="d9a77-324">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="d9a77-325">Por último, `EF.Functions.Collate()` permite realizar consultas ad-hoc mediante diferentes intercalaciones.</span><span class="sxs-lookup"><span data-stu-id="d9a77-325">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="d9a77-326">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-326">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="d9a77-327">De este modo se generará la siguiente consulta para SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d9a77-327">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="d9a77-328">Tenga en cuenta que las intercalaciones ad-hoc deben usarse con cuidado, ya que pueden afectar negativamente al rendimiento de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-328">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="d9a77-329">En el problema [n.º 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-329">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="d9a77-330">Transmisión de argumentos a IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="d9a77-330">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="d9a77-331">Los argumentos se transmiten ahora desde la línea de comandos al método de `CreateDbContext` de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="d9a77-331">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="d9a77-332">Por ejemplo, para indicar que se trata de una compilación de desarrollo, se puede pasar un argumento personalizado (por ejemplo, `dev`) en la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="d9a77-332">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="d9a77-333">Este argumento pasará entonces a la factoría, donde se puede usar para controlar cómo se crea e inicializa el contexto.</span><span class="sxs-lookup"><span data-stu-id="d9a77-333">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="d9a77-334">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-334">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="d9a77-335">En el problema [n.º 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-335">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="d9a77-336">Consultas de no seguimiento con resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="d9a77-336">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="d9a77-337">Ahora se pueden configurar consultas de no seguimiento para hacer la resolución de identidad.</span><span class="sxs-lookup"><span data-stu-id="d9a77-337">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="d9a77-338">Por ejemplo, la siguiente consulta creará una instancia de Blog para cada Post, aunque cada blog tenga la misma clave principal.</span><span class="sxs-lookup"><span data-stu-id="d9a77-338">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="d9a77-339">Sin embargo, a costa de que por lo general sea ligeramente más lento y siempre use más memoria, se puede cambiar esta consulta para asegurarse de que solo se crea una instancia de Blog:</span><span class="sxs-lookup"><span data-stu-id="d9a77-339">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="d9a77-340">Tenga en cuenta que esto solo es útil para consultas de no seguimiento, ya que todas las consultas de seguimiento ya muestran este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="d9a77-340">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="d9a77-341">Además, después de la revisión de la API, se cambiará la sintaxis de `PerformIdentityResolution`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-341">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="d9a77-342">Consulte el problema [n.º 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="d9a77-342">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="d9a77-343">En el problema [n.º 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-343">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="d9a77-344">Columnas calculadas almacenadas (persistentes)</span><span class="sxs-lookup"><span data-stu-id="d9a77-344">Stored (persisted) computed columns</span></span>

<span data-ttu-id="d9a77-345">La mayoría de las bases de datos permiten almacenar valores de columnas calculadas después del cálculo.</span><span class="sxs-lookup"><span data-stu-id="d9a77-345">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="d9a77-346">Aunque ocupa espacio en disco, la columna se calcula solo una vez al actualizar, en lugar de cada vez que se recupera su valor.</span><span class="sxs-lookup"><span data-stu-id="d9a77-346">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="d9a77-347">Esto también permite indizar la columna para algunas bases de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-347">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="d9a77-348">EF Core 5.0 permite configurar las columnas calculadas como almacenadas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-348">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="d9a77-349">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-349">For example:</span></span>

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="d9a77-350">Columnas calculadas de SQLite</span><span class="sxs-lookup"><span data-stu-id="d9a77-350">SQLite computed columns</span></span>

<span data-ttu-id="d9a77-351">EF Core admite ahora columnas calculadas en las bases de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="d9a77-351">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="d9a77-352">Versión preliminar 4</span><span class="sxs-lookup"><span data-stu-id="d9a77-352">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="d9a77-353">Configuración de la precisión y la escala de la base de datos en el modelo</span><span class="sxs-lookup"><span data-stu-id="d9a77-353">Configure database precision/scale in model</span></span>

<span data-ttu-id="d9a77-354">Ahora se puede especificar la precisión y la escala de una propiedad mediante el generador de modelos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-354">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="d9a77-355">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-355">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="d9a77-356">La precisión y la escala todavía se pueden establecer mediante el tipo completo de base de datos, como "decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="d9a77-356">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="d9a77-357">En el problema [n.º 527](https://github.com/dotnet/EntityFramework.Docs/issues/527) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-357">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="d9a77-358">Especificación del factor de relleno del índice de SQL Server</span><span class="sxs-lookup"><span data-stu-id="d9a77-358">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="d9a77-359">Ahora se puede especificar el factor de relleno al crear un índice en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="d9a77-359">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="d9a77-360">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-360">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="d9a77-361">Versión preliminar 3</span><span class="sxs-lookup"><span data-stu-id="d9a77-361">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="d9a77-362">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="d9a77-362">Filtered Include</span></span>

<span data-ttu-id="d9a77-363">El método Include ahora admite el filtrado de las entidades incluidas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-363">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="d9a77-364">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-364">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="d9a77-365">Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".</span><span class="sxs-lookup"><span data-stu-id="d9a77-365">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="d9a77-366">Para reducir el número de las entidades incluidas, se puede usar Skip y Take.</span><span class="sxs-lookup"><span data-stu-id="d9a77-366">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="d9a77-367">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-367">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="d9a77-368">Esta consulta devolverá los blogs que tengan cinco publicaciones como máximo.</span><span class="sxs-lookup"><span data-stu-id="d9a77-368">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="d9a77-369">Vea la [documentación de Include](xref:core/querying/related-data#filtered-include) para obtener información completa.</span><span class="sxs-lookup"><span data-stu-id="d9a77-369">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="d9a77-370">Nueva API de ModelBuilder para las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="d9a77-370">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="d9a77-371">Las propiedades de navegación se configuran principalmente al [definir relaciones](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="d9a77-371">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="d9a77-372">No obstante, se puede usar el nuevo método `Navigation` cuando es necesario realizar una configuración adicional en las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-372">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="d9a77-373">Por ejemplo, para establecer un campo de respaldo de la navegación cuando el campo no se encuentre por convención:</span><span class="sxs-lookup"><span data-stu-id="d9a77-373">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="d9a77-374">Tenga en cuenta que la API de `Navigation` no reemplaza la configuración de la relación,</span><span class="sxs-lookup"><span data-stu-id="d9a77-374">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="d9a77-375">sino que permite la configuración adicional de las propiedades de navegación en relaciones ya detectadas o definidas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-375">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="d9a77-376">Consulte la [documentación sobre la configuración de las propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="d9a77-376">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="d9a77-377">Nuevos parámetros de la línea de comandos para espacios de nombres y cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="d9a77-377">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="d9a77-378">Ahora, las migraciones y el scaffolding permiten especificar los espacios de nombres en la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-378">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="d9a77-379">Esto puede hacerse para, por ejemplo, aplicar ingeniería inversa a una base de datos y colocar las clases Context y Model en diferentes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="d9a77-379">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="d9a77-380">Para más información, consulte la documentación sobre [migraciones](xref:core/managing-schemas/migrations/index#namespaces) e [ingeniería inversa](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="d9a77-380">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="d9a77-381">Ahora, además, también puede pasarse una cadena de conexión al comando `database-update`:</span><span class="sxs-lookup"><span data-stu-id="d9a77-381">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="d9a77-382">Para más información, consulte la [documentación sobre herramientas](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="d9a77-382">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="d9a77-383">También se han agregado parámetros equivalentes a los comandos de PowerShell que se usan en la consola del administrador de paquetes de VS.</span><span class="sxs-lookup"><span data-stu-id="d9a77-383">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="d9a77-384">EnableDetailedErrors ha vuelto</span><span class="sxs-lookup"><span data-stu-id="d9a77-384">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="d9a77-385">Por motivos de rendimiento, EF no realiza comprobaciones de valores NULL adicionales cuando se leen los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-385">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="d9a77-386">Esto puede dar lugar a excepciones cuya causa principal es difícil de rastrear cuando se encuentra un valor NULL inesperado.</span><span class="sxs-lookup"><span data-stu-id="d9a77-386">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="d9a77-387">El uso de `EnableDetailedErrors` agregará una comprobación de valores NULL adicional a las consultas de modo que, para una pequeña sobrecarga de rendimiento, estos errores sean más fáciles de rastrear para averiguar su causa principal.</span><span class="sxs-lookup"><span data-stu-id="d9a77-387">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="d9a77-388">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-388">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="d9a77-389">En el problema [n.º 955](https://github.com/dotnet/EntityFramework.Docs/issues/955) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-389">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="d9a77-390">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="d9a77-390">Cosmos partition keys</span></span>

<span data-ttu-id="d9a77-391">Ahora, es posible especificar en las consultas la clave de partición que va a usarse para una consulta determinada.</span><span class="sxs-lookup"><span data-stu-id="d9a77-391">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="d9a77-392">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-392">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="d9a77-393">En el problema [n.º 2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-393">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="d9a77-394">Compatibilidad con la función DATALENGTH de SQL Server</span><span class="sxs-lookup"><span data-stu-id="d9a77-394">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="d9a77-395">Se puede acceder con el nuevo método `EF.Functions.DataLength`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-395">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="d9a77-396">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-396">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="d9a77-397">Versión preliminar 2</span><span class="sxs-lookup"><span data-stu-id="d9a77-397">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="d9a77-398">Uso de un atributo de C# para especificar un campo de respaldo de propiedad</span><span class="sxs-lookup"><span data-stu-id="d9a77-398">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="d9a77-399">Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="d9a77-399">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="d9a77-400">Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente.</span><span class="sxs-lookup"><span data-stu-id="d9a77-400">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="d9a77-401">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-401">For example:</span></span>

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

<span data-ttu-id="d9a77-402">En el problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-402">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="d9a77-403">Asignación completa de discriminador</span><span class="sxs-lookup"><span data-stu-id="d9a77-403">Complete discriminator mapping</span></span>

<span data-ttu-id="d9a77-404">EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="d9a77-404">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="d9a77-405">Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador.</span><span class="sxs-lookup"><span data-stu-id="d9a77-405">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="d9a77-406">EF Core 5.0 ahora implementa estas mejoras.</span><span class="sxs-lookup"><span data-stu-id="d9a77-406">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="d9a77-407">Por ejemplo, las versiones anteriores de EF Core siempre generaban este SQL para una consulta que devuelve todos los tipos de una jerarquía:</span><span class="sxs-lookup"><span data-stu-id="d9a77-407">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="d9a77-408">EF Core 5.0 ahora genera lo siguiente cuando se configura una asignación completa de discriminador:</span><span class="sxs-lookup"><span data-stu-id="d9a77-408">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="d9a77-409">Este va a ser el comportamiento predeterminado a partir de la versión preliminar 3.</span><span class="sxs-lookup"><span data-stu-id="d9a77-409">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="d9a77-410">Mejoras de rendimiento de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="d9a77-410">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="d9a77-411">Se han realizado dos mejoras de rendimiento para SQLIte:</span><span class="sxs-lookup"><span data-stu-id="d9a77-411">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="d9a77-412">La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-412">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="d9a77-413">La inicialización de SqliteConnection ahora es diferida.</span><span class="sxs-lookup"><span data-stu-id="d9a77-413">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="d9a77-414">Estas mejoras se encuentran en el proveedor Microsoft.Data.Sqlite de ADO.NET y, por lo tanto, también mejoran el rendimiento fuera de EF Core.</span><span class="sxs-lookup"><span data-stu-id="d9a77-414">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="d9a77-415">Versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="d9a77-415">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="d9a77-416">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="d9a77-416">Simple logging</span></span>

<span data-ttu-id="d9a77-417">Esta característica agrega funcionalidad similar a `Database.Log` en EF6.</span><span class="sxs-lookup"><span data-stu-id="d9a77-417">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="d9a77-418">Es decir, proporciona una manera sencilla de obtener registros de EF Core sin necesidad de configurar ningún tipo de plataforma de registro externa.</span><span class="sxs-lookup"><span data-stu-id="d9a77-418">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="d9a77-419">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="d9a77-419">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d9a77-420">En el problema [n.º 2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d9a77-420">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="d9a77-421">Forma sencilla de generar contenido SQL</span><span class="sxs-lookup"><span data-stu-id="d9a77-421">Simple way to get generated SQL</span></span>

<span data-ttu-id="d9a77-422">EF Core 5.0 presenta el método de extensión `ToQueryString`, que devuelve el SQL que EF Core genera al ejecutar una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="d9a77-422">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="d9a77-423">La documentación preliminar se incluye en el [estado semanal de EF del 9 de enero de 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="d9a77-423">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="d9a77-424">En el problema [n.º 1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d9a77-424">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="d9a77-425">Uso de un atributo de C# para indicar que una entidad no tiene clave</span><span class="sxs-lookup"><span data-stu-id="d9a77-425">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="d9a77-426">Ahora se pueden configurar los tipos de entidad para indicar que no tienen clave mediante el nuevo valor `KeylessAttribute`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-426">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="d9a77-427">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-427">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="d9a77-428">En el problema [n.º 2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-428">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="d9a77-429">Posibilidad de cambiar la conexión o la cadena de conexión en una instancia inicializada de DbContext</span><span class="sxs-lookup"><span data-stu-id="d9a77-429">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="d9a77-430">Ahora es más fácil crear una instancia de DbContext sin ninguna conexión o cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="d9a77-430">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="d9a77-431">Además, la conexión o la cadena de conexión ahora también se pueden mutar en la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="d9a77-431">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="d9a77-432">Esta característica permite que la misma instancia de contexto se conecte de forma dinámica a diferentes bases de datos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-432">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="d9a77-433">En el problema [n.º 2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-433">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="d9a77-434">Proxies de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="d9a77-434">Change-tracking proxies</span></span>

<span data-ttu-id="d9a77-435">Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) y [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="d9a77-435">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="d9a77-436">A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios.</span><span class="sxs-lookup"><span data-stu-id="d9a77-436">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="d9a77-437">Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.</span><span class="sxs-lookup"><span data-stu-id="d9a77-437">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="d9a77-438">En el problema [n.º 2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-438">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="d9a77-439">Vistas de depuración mejoradas</span><span class="sxs-lookup"><span data-stu-id="d9a77-439">Enhanced debug views</span></span>

<span data-ttu-id="d9a77-440">Las vistas de depuración son una forma fácil de consultar los aspectos internos de EF Core al depurar problemas.</span><span class="sxs-lookup"><span data-stu-id="d9a77-440">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="d9a77-441">Hace algún tiempo ya se implementó una vista de depuración para el modelo.</span><span class="sxs-lookup"><span data-stu-id="d9a77-441">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="d9a77-442">En el caso de EF Core 5.0, hemos facilitado la lectura de la vista de modelo y hemos agregado una nueva vista de depuración para las entidades de las que se ha realizado un seguimiento en el administrador de estado.</span><span class="sxs-lookup"><span data-stu-id="d9a77-442">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="d9a77-443">La documentación preliminar se incluye en el [estado semanal de EF del 12 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="d9a77-443">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="d9a77-444">En el problema [n.º 2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d9a77-444">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="d9a77-445">Control mejorado de la semántica de valores NULL de base de datos</span><span class="sxs-lookup"><span data-stu-id="d9a77-445">Improved handling of database null semantics</span></span>

<span data-ttu-id="d9a77-446">Normalmente, las bases de datos relacionales tratan NULL como valores desconocidos y, por lo tanto, no son iguales a otros valores NULL.</span><span class="sxs-lookup"><span data-stu-id="d9a77-446">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="d9a77-447">Mientras, C# trata NULL como un valor definido que se compara igual que cualquier otro valor NULL.</span><span class="sxs-lookup"><span data-stu-id="d9a77-447">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="d9a77-448">De forma predeterminada, EF Core traduce las consultas para que usen la semántica de valores NULL de C#.</span><span class="sxs-lookup"><span data-stu-id="d9a77-448">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="d9a77-449">EF Core 5.0 mejora en gran medida la eficacia de dichas traducciones.</span><span class="sxs-lookup"><span data-stu-id="d9a77-449">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="d9a77-450">En el problema [n.º 1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-450">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="d9a77-451">Propiedades del indizador</span><span class="sxs-lookup"><span data-stu-id="d9a77-451">Indexer properties</span></span>

<span data-ttu-id="d9a77-452">EF Core 5.0 admite la asignación de propiedades de indizador de C#.</span><span class="sxs-lookup"><span data-stu-id="d9a77-452">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="d9a77-453">Estas propiedades permiten a las entidades actuar como bolsas de propiedades en las que las columnas se asignan a propiedades con nombre de la bolsa.</span><span class="sxs-lookup"><span data-stu-id="d9a77-453">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="d9a77-454">En el problema [n.º 2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-454">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="d9a77-455">Generación de restricciones CHECK para las asignaciones de enumeración</span><span class="sxs-lookup"><span data-stu-id="d9a77-455">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="d9a77-456">Las migraciones de EF Core 5.0 ahora pueden generar restricciones CHECK para las asignaciones de propiedades de enumeración.</span><span class="sxs-lookup"><span data-stu-id="d9a77-456">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="d9a77-457">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-457">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="d9a77-458">En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-458">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="d9a77-459">IsRelational</span><span class="sxs-lookup"><span data-stu-id="d9a77-459">IsRelational</span></span>

<span data-ttu-id="d9a77-460">Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`.</span><span class="sxs-lookup"><span data-stu-id="d9a77-460">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="d9a77-461">Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="d9a77-461">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="d9a77-462">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-462">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="d9a77-463">En el problema [n.º 2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-463">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="d9a77-464">Simultaneidad optimista de Cosmos con mecanismos ETag</span><span class="sxs-lookup"><span data-stu-id="d9a77-464">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="d9a77-465">El proveedor de bases de datos de Azure Cosmos DB ya es compatible con la simultaneidad optimista mediante mecanismos ETag.</span><span class="sxs-lookup"><span data-stu-id="d9a77-465">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="d9a77-466">Utilice el generador de modelos de OnModelCreating para confirmar un mecanismo ETag:</span><span class="sxs-lookup"><span data-stu-id="d9a77-466">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="d9a77-467">Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](/ef/core/saving/concurrency), por ejemplo, para implementar reintentos.</span><span class="sxs-lookup"><span data-stu-id="d9a77-467">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="d9a77-468">En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-468">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="d9a77-469">Traducciones de consultas para más construcciones DateTime</span><span class="sxs-lookup"><span data-stu-id="d9a77-469">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="d9a77-470">Ahora las consultas que contienen la nueva construcción DateTime se traducen.</span><span class="sxs-lookup"><span data-stu-id="d9a77-470">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="d9a77-471">Además, ahora se asignan las funciones de SQL Server que hay a continuación:</span><span class="sxs-lookup"><span data-stu-id="d9a77-471">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="d9a77-472">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="d9a77-472">DateDiffWeek</span></span>
* <span data-ttu-id="d9a77-473">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="d9a77-473">DateFromParts</span></span>

<span data-ttu-id="d9a77-474">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-474">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="d9a77-475">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-475">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="d9a77-476">Traducciones de consultas para más construcciones de matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="d9a77-476">Query translations for more byte array constructs</span></span>

<span data-ttu-id="d9a77-477">Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.</span><span class="sxs-lookup"><span data-stu-id="d9a77-477">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="d9a77-478">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="d9a77-478">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d9a77-479">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d9a77-479">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="d9a77-480">Traducción de consultas para Reverse</span><span class="sxs-lookup"><span data-stu-id="d9a77-480">Query translation for Reverse</span></span>

<span data-ttu-id="d9a77-481">Las consultas que usan `Reverse` ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="d9a77-481">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="d9a77-482">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-482">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="d9a77-483">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-483">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="d9a77-484">Traducción de consultas para operadores bit a bit</span><span class="sxs-lookup"><span data-stu-id="d9a77-484">Query translation for bitwise operators</span></span>

<span data-ttu-id="d9a77-485">Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d9a77-485">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="d9a77-486">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-486">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="d9a77-487">Traducción de consultas para cadenas en Cosmos</span><span class="sxs-lookup"><span data-stu-id="d9a77-487">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="d9a77-488">Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="d9a77-488">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="d9a77-489">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d9a77-489">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
