---
title: Novedades en EF Core 6.0
description: Información general sobre las nuevas características de EF Core 6.0
author: ajcvickers
ms.date: 01/28/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: bcc2b3ce9047a2c6b5a89e99b96919914bcf42fe
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543203"
---
# <a name="whats-new-in-ef-core-60"></a>Novedades en EF Core 6.0

EF Core 6.0 está actualmente en desarrollo. Esta página contiene información general sobre los cambios interesantes introducidos en cada versión preliminar.

Esta página no duplica el [plan para EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan). En el plan se describen los temas generales relativos a EF Core 6.0, incluido todo lo que se piensa incluir antes de publicar la versión final.

## <a name="ef-core-60-preview-1"></a>EF Core 6.0, versión preliminar 1

> [!TIP]
> Puede ejecutar todos los ejemplos de la versión preliminar 1 que se muestran a continuación y depurar en ellos. Para hacerlo, [descargue el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).

### <a name="unicodeattribute"></a>UnicodeAttribute

Problema de GitHub [número 19794](https://github.com/dotnet/efcore/issues/19794). Esta característica la ha aportado [@RaymondHuy](https://github.com/RaymondHuy).

A partir de EF Core 6.0, una propiedad de cadena ahora puede asignarse a una columna no Unicode mediante un atributo de asignación _sin especificar el tipo de base de datos directamente_. Por ejemplo, considere un tipo de entidad `Book` con una propiedad para el [ISBN (Número Internacional Normalizado del Libro)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) con el formato "ISBN 978-3-16-148410-0":

<!--
    public class Book
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [Unicode(false)]
        [MaxLength(22)]
        public string Isbn { get; set; }
    }
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/UnicodeAttributeSample.cs?name=BookEntityType)]

Dado que los ISBN no pueden contener caracteres no Unicode, el atributo `Unicode` hará que se use un tipo de cadena no Unicode. Además, se usa `MaxLength` para limitar el tamaño de la columna de base de datos. Por ejemplo, al usar SQL Server, esto da como resultado una columna de base de datos `varchar(22)`:

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> De forma predeterminada, EF Core asigna propiedades de cadena a las columnas Unicode. `UnicodeAttribute` se omite cuando el sistema de base de datos solo admite tipos Unicode.

### <a name="precisionattribute"></a>PrecisionAttribute

Problema de GitHub [número 17914](https://github.com/dotnet/efcore/issues/17914). Esta característica la ha aportado [@RaymondHuy](https://github.com/RaymondHuy).

Ahora se pueden configurar la precisión y la escala de una columna de base de datos mediante atributos de asignación _sin especificar el tipo de base de datos directamente_. Por ejemplo, considere un tipo de entidad `Product` con una propiedad `Price` decimal:

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

EF Core asignará esta propiedad a una columna de base de datos con una precisión de 10 y una escala de 2. Por ejemplo, en SQL Server:

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a>EntityTypeConfigurationAttribute

Problema de GitHub [número 23163](https://github.com/dotnet/efcore/issues/23163). Esta característica la ha aportado [@KaloyanIT](https://github.com/KaloyanIT).

Las instancias de <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> permiten la configuración de <xref:Microsoft.EntityFrameworkCore.ModelBuilder> para cada tipo de entidad que se incluya en su propia clase de configuración. Por ejemplo:

<!--
public class BookConfiguration : IEntityTypeConfiguration<Book>
{
    public void Configure(EntityTypeBuilder<Book> builder)
    {
        builder
            .Property(e => e.Isbn)
            .IsUnicode(false)
            .HasMaxLength(22);
    }
}
-->
[!code-csharp[BookConfiguration](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookConfiguration)]

Normalmente, es necesario crear una instancia de esta clase de configuración y llamarla desde <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>. Por ejemplo:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

A partir de EF Core 6.0, se puede colocar un elemento `EntityTypeConfigurationAttribute` en el tipo de entidad para que EF Core pueda encontrar y usar la configuración adecuada. Por ejemplo:

<!--
[EntityTypeConfiguration(typeof(BookConfiguration))]
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookEntityType)]

Este atributo significa que EF Core usará la implementación de `IEntityTypeConfiguration` especificada siempre que el tipo de entidad `Book` se incluya en un modelo. El tipo de entidad se incluye en un modelo mediante uno de los mecanismos normales. Por ejemplo, mediante la creación de una propiedad <xref:Microsoft.EntityFrameworkCore.DbSet%601> para el tipo de entidad:

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

O bien, mediante su registro en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> Los tipos `EntityTypeConfigurationAttribute` no se detectarán automáticamente en un ensamblado. Los tipos de entidad se deben agregar al modelo antes de que se detecte el atributo en ese tipo de entidad.

### <a name="translate-tostring-on-sqlite"></a>Traducción de ToString en SQLite

Problema de GitHub [número 17223](https://github.com/dotnet/efcore/issues/17223). Esta característica la ha aportado [@ralmsdeveloper](https://github.com/ralmsdeveloper).

Las llamadas a <xref:System.Object.ToString> ahora se traducen a SQL cuando se usa el proveedor de base de datos de SQLite. Esto puede ser útil para las búsquedas de texto que implican columnas que no son de cadena. Por ejemplo, considere un tipo de entidad `User` que almacena los números de teléfono como valores numéricos:

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

Se puede usar `ToString` para convertir el número en una cadena de la base de datos. Después, se puede usar esta cadena con una función como `LIKE` para buscar números que coincidan con un patrón. Por ejemplo, para buscar todos los números que contienen 555:

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

Esto se traduce en el siguiente código SQL cuando se usa una base de datos de SQLite:

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

Tenga en cuenta que la traducción de <xref:System.Object.ToString> para SQL Server ya se admite en EF Core 5.0 y también puede ser compatible con otros proveedores de bases de datos.

### <a name="effunctionsrandom"></a>EF.Functions.Random

Problema de GitHub [número 16141](https://github.com/dotnet/efcore/issues/16141). Esta característica la ha aportado [@RaymondHuy](https://github.com/RaymondHuy).

`EF.Functions.Random` se asigna a una función de base de datos que devuelve un número seudoaleatorio entre 0 y 1, ambos no incluidos. Las traducciones se han implementado en el repositorio de EF Core para SQL Server, SQLite y Cosmos. Por ejemplo, considere un tipo de entidad `User` con una propiedad `Popularity`:

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

`Popularity` puede tener valores entre 1 y 5, ambos incluidos. Con `EF.Functions.Random`, podemos escribir una consulta para devolver todos los usuarios con una popularidad elegida aleatoriamente:

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

Esto se traduce en el siguiente código SQL cuando se usa una base de datos de SQL Server:

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a>Compatibilidad con columnas dispersas de SQL Server

Problema de GitHub [número 8023](https://github.com/dotnet/efcore/issues/8023).

Las [columnas dispersas](/sql/relational-databases/tables/use-sparse-columns) de SQL Server son columnas ordinarias que se optimizan para almacenar valores NULL. Esto puede ser útil cuando se usa la [asignación de herencia de tabla por jerarquía](xref:core/modeling/inheritance), donde las propiedades de un subtipo poco usado darán como resultado valores de columna NULL para la mayoría de las filas de la tabla. Por ejemplo, considere una clase `ForumModerator` que se extiende desde `ForumUser`:

<!--
    public class ForumUser
    {
        public int Id { get; set; }
        public string Username { get; set; }
    }

    public class ForumModerator : ForumUser
    {
        public string ForumName { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=UserEntityType)]

Puede haber millones de usuarios, pero solo unos pocos de ellos son moderadores. Esto significa que, en este caso, puede tener sentido la asignación de `ForumName` como disperso. Esto ahora puede configurarse mediante `IsSparse` en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>. Por ejemplo:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<ForumModerator>()
                .Property(e => e.ForumName)
                .IsSparse();
        }
-->
[!code-csharp[OnModelCreating](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=OnModelCreating)]

Después, las migraciones de EF Core marcarán la columna como dispersa. Por ejemplo:

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> Las columnas dispersas tienen limitaciones. Le recomendamos que lea la [documentación sobre columnas dispersas de SQL Server](/sql/relational-databases/tables/use-sparse-columns) para asegurarse de que las columnas dispersas son la opción correcta para su escenario.

### <a name="in-memory-database-validate-required-properties-are-not-null"></a>Base de datos en memoria: valide que las propiedades obligatorias no son NULL

Problema de GitHub [número 10613](https://github.com/dotnet/efcore/issues/10613). Esta característica la ha aportado [@fagnercarvalho](https://github.com/fagnercarvalho).

La base de datos en memoria de EF Core ahora producirá una excepción si se intenta guardar un valor NULL para una propiedad marcada como obligatoria. Por ejemplo, considere un tipo `User` con una propiedad `Username` obligatoria:

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

Si intenta guardar una entidad con un valor `Username` NULL, se producirá la siguiente excepción:

> Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}' (Microsoft.EntityFrameworkCore.DbUpdateException: faltan las propiedades "{'Username'}" obligatorias para la instancia de tipo de entidad "User" con el valor de clave "{Id: 1}").

Esta validación se puede deshabilitar si es necesario. Por ejemplo:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .LogTo(Console.WriteLine, new[] { InMemoryEventId.ChangesSaved })
                .UseInMemoryDatabase("UserContextWithNullCheckingDisabled")
                .EnableNullabilityCheck(false);
        }
-->
[!code-csharp[OnConfiguring](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=OnConfiguring)]

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a>Mejora de la traducción de SQL Server para IsNullOrWhitespace

Problema de GitHub [número 22916](https://github.com/dotnet/efcore/issues/22916). Esta característica la ha aportado [@Marusyk](https://github.com/Marusyk).

Considere la consulta siguiente:

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

Antes de EF Core 6.0, esto se traducía a lo siguiente en SQL Server:

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

Esta traducción se ha mejorado para EF Core 6.0 a:

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a>Cambio de los comentarios de las bases de datos a comentarios de código con scaffolding

Problema de GitHub [número 19113](https://github.com/dotnet/efcore/issues/19113). Esta característica la ha aportado [@ErikEJ](https://github.com/ErikEJ).

Ahora se aplica scaffolding a los comentarios en tablas y columnas de SQL para convertirlos en los tipos de entidad que se crean al aplicar [ingeniería inversa a un modelo de EF Core](xref:core/managing-schemas/scaffolding) de una base de datos de SQL Server existente. Por ejemplo:

```csharp
/// <summary>
/// The Blog table.
/// </summary>
public partial class Blog
{
    /// <summary>
    /// The primary key.
    /// </summary>
    [Key]
    public int Id { get; set; }
}
```

## <a name="microsoftdatasqlite-60-preview-1"></a>Microsoft.Data.Sqlite 6.0, versión preliminar 1

> [!TIP]
> Puede ejecutar todos los ejemplos de la versión preliminar 1 que se muestran a continuación y depurar en ellos. Para hacerlo, [descargue el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).

### <a name="savepoints-api"></a>API de puntos de retorno

Problema de GitHub [número 20228](https://github.com/dotnet/efcore/issues/20228).

Hemos estandarizado [una API común para puntos de retorno en proveedores de ADO.NET](https://github.com/dotnet/runtime/issues/33397). Microsoft.Data.Sqlite ahora admite esta API, incluido lo siguiente:

- <xref:System.Data.Common.DbTransaction.Save(System.String)> para crear un punto de retorno en la transacción
- <xref:System.Data.Common.DbTransaction.Rollback(System.String)> para revertir a un punto de retorno anterior
- <xref:System.Data.Common.DbTransaction.Release(System.String)> para liberar un punto de retorno

El uso de un punto de retorno permite que parte de una transacción se revierta sin necesidad de revertirla entera. Por ejemplo, el código siguiente:

- Crea una transacción.
- Envía una actualización a la base de datos.
- Crea un punto de retorno.
- Envía otra actualización a la base de datos.
- Revierte al punto de retorno creado previamente.
- Confirma la transacción.

<!--
        using var connection = new SqliteConnection("DataSource=test.db");
        connection.Open();

        using var transaction = connection.BeginTransaction();

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'ajcvickers' WHERE Id = 1";
            command.ExecuteNonQuery();
        }

        transaction.Save("MySavepoint");

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'wfvickers' WHERE Id = 2";
            command.ExecuteNonQuery();
        }

        transaction.Rollback("MySavepoint");

        transaction.Commit();
-->
[!code-csharp[PerformUpdates](../../../../samples/core/Miscellaneous/NewInEFCore6/SqliteSamples.cs?name=PerformUpdates)]

Esto hará que la primera actualización se confirme en la base de datos, pero no la segunda porque el punto de retorno se revirtió antes de confirmar la transacción.

### <a name="command-timeout-in-the-connection-string"></a>Tiempo de espera del comando en la cadena de conexión

Problema de GitHub [número 22505](https://github.com/dotnet/efcore/issues/22505). Esta característica la ha aportado [@nmichels](https://github.com/nmichels).

Los proveedores de ADO.NET admiten dos tiempos de espera distintos:

- El tiempo de espera de conexión, que determina el tiempo máximo de espera al establecer una conexión con la base de datos.
- El tiempo de espera del comando, que determina el tiempo máximo que hay que esperar hasta que se complete la ejecución de un comando.

El tiempo de espera del comando se puede establecer desde el código mediante <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>. Muchos proveedores ahora también exponen el tiempo de espera del comando en la cadena de conexión. Microsoft.Data.Sqlite sigue esta tendencia con la palabra clave `Command Timeout` de la cadena de conexión. Por ejemplo, `"Command Timeout=60;DataSource=test.db"` usará 60 segundos como tiempo de espera predeterminado para los comandos que cree la conexión.

> [!TIP]
> SQLite trata `Default Timeout` como sinónimo de `Command Timeout`, por lo que se puede usar en su lugar si se prefiere.
