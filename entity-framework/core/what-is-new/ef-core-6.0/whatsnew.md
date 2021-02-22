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
# <a name="whats-new-in-ef-core-60"></a><span data-ttu-id="d7181-103">Novedades en EF Core 6.0</span><span class="sxs-lookup"><span data-stu-id="d7181-103">What's New in EF Core 6.0</span></span>

<span data-ttu-id="d7181-104">EF Core 6.0 está actualmente en desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d7181-104">EF Core 6.0 is currently in development.</span></span> <span data-ttu-id="d7181-105">Esta página contiene información general sobre los cambios interesantes introducidos en cada versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="d7181-105">This contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="d7181-106">Esta página no duplica el [plan para EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span><span class="sxs-lookup"><span data-stu-id="d7181-106">This page does not duplicate the [plan for EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span></span> <span data-ttu-id="d7181-107">En el plan se describen los temas generales relativos a EF Core 6.0, incluido todo lo que se piensa incluir antes de publicar la versión final.</span><span class="sxs-lookup"><span data-stu-id="d7181-107">The plan describes the overall themes for EF Core 6.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="ef-core-60-preview-1"></a><span data-ttu-id="d7181-108">EF Core 6.0, versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="d7181-108">EF Core 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="d7181-109">Puede ejecutar todos los ejemplos de la versión preliminar 1 que se muestran a continuación y depurar en ellos. Para hacerlo, [descargue el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="d7181-109">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="unicodeattribute"></a><span data-ttu-id="d7181-110">UnicodeAttribute</span><span class="sxs-lookup"><span data-stu-id="d7181-110">UnicodeAttribute</span></span>

<span data-ttu-id="d7181-111">Problema de GitHub [número 19794](https://github.com/dotnet/efcore/issues/19794).</span><span class="sxs-lookup"><span data-stu-id="d7181-111">GitHub Issue: [#19794](https://github.com/dotnet/efcore/issues/19794).</span></span> <span data-ttu-id="d7181-112">Esta característica la ha aportado [@RaymondHuy](https://github.com/RaymondHuy).</span><span class="sxs-lookup"><span data-stu-id="d7181-112">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="d7181-113">A partir de EF Core 6.0, una propiedad de cadena ahora puede asignarse a una columna no Unicode mediante un atributo de asignación _sin especificar el tipo de base de datos directamente_.</span><span class="sxs-lookup"><span data-stu-id="d7181-113">Starting with EF Core 6.0, a string property can now be mapped to a non-Unicode column using a mapping attribute _without specifying the database type directly_.</span></span> <span data-ttu-id="d7181-114">Por ejemplo, considere un tipo de entidad `Book` con una propiedad para el [ISBN (Número Internacional Normalizado del Libro)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) con el formato "ISBN 978-3-16-148410-0":</span><span class="sxs-lookup"><span data-stu-id="d7181-114">For example, consider a `Book` entity type with a property for the [International Standard Book Number (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) in the form "ISBN 978-3-16-148410-0":</span></span>

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

<span data-ttu-id="d7181-115">Dado que los ISBN no pueden contener caracteres no Unicode, el atributo `Unicode` hará que se use un tipo de cadena no Unicode.</span><span class="sxs-lookup"><span data-stu-id="d7181-115">Since ISBNs cannot contain any non-unicode characters, the `Unicode` attribute will cause a non-Unicode string type to be used.</span></span> <span data-ttu-id="d7181-116">Además, se usa `MaxLength` para limitar el tamaño de la columna de base de datos.</span><span class="sxs-lookup"><span data-stu-id="d7181-116">In addition, `MaxLength` is used to limit the size of the database column.</span></span> <span data-ttu-id="d7181-117">Por ejemplo, al usar SQL Server, esto da como resultado una columna de base de datos `varchar(22)`:</span><span class="sxs-lookup"><span data-stu-id="d7181-117">For example, when using SQL Server, this results in a database column of `varchar(22)`:</span></span>

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="d7181-118">De forma predeterminada, EF Core asigna propiedades de cadena a las columnas Unicode.</span><span class="sxs-lookup"><span data-stu-id="d7181-118">EF Core maps string properties to Unicode columns by default.</span></span> <span data-ttu-id="d7181-119">`UnicodeAttribute` se omite cuando el sistema de base de datos solo admite tipos Unicode.</span><span class="sxs-lookup"><span data-stu-id="d7181-119">`UnicodeAttribute` is ignored when the database system supports only Unicode types.</span></span>

### <a name="precisionattribute"></a><span data-ttu-id="d7181-120">PrecisionAttribute</span><span class="sxs-lookup"><span data-stu-id="d7181-120">PrecisionAttribute</span></span>

<span data-ttu-id="d7181-121">Problema de GitHub [número 17914](https://github.com/dotnet/efcore/issues/17914).</span><span class="sxs-lookup"><span data-stu-id="d7181-121">GitHub Issue: [#17914](https://github.com/dotnet/efcore/issues/17914).</span></span> <span data-ttu-id="d7181-122">Esta característica la ha aportado [@RaymondHuy](https://github.com/RaymondHuy).</span><span class="sxs-lookup"><span data-stu-id="d7181-122">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="d7181-123">Ahora se pueden configurar la precisión y la escala de una columna de base de datos mediante atributos de asignación _sin especificar el tipo de base de datos directamente_.</span><span class="sxs-lookup"><span data-stu-id="d7181-123">The precision and scale of a database column can now be configured using mapping attributes _without specifying the database type directly_.</span></span> <span data-ttu-id="d7181-124">Por ejemplo, considere un tipo de entidad `Product` con una propiedad `Price` decimal:</span><span class="sxs-lookup"><span data-stu-id="d7181-124">For example, consider a `Product` entity type with a decimal `Price` property:</span></span>

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

<span data-ttu-id="d7181-125">EF Core asignará esta propiedad a una columna de base de datos con una precisión de 10 y una escala de 2.</span><span class="sxs-lookup"><span data-stu-id="d7181-125">EF Core will map this property to a database column with precision 10 and scale 2.</span></span> <span data-ttu-id="d7181-126">Por ejemplo, en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d7181-126">For example, on SQL Server:</span></span>

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a><span data-ttu-id="d7181-127">EntityTypeConfigurationAttribute</span><span class="sxs-lookup"><span data-stu-id="d7181-127">EntityTypeConfigurationAttribute</span></span>

<span data-ttu-id="d7181-128">Problema de GitHub [número 23163](https://github.com/dotnet/efcore/issues/23163).</span><span class="sxs-lookup"><span data-stu-id="d7181-128">GitHub Issue: [#23163](https://github.com/dotnet/efcore/issues/23163).</span></span> <span data-ttu-id="d7181-129">Esta característica la ha aportado [@KaloyanIT](https://github.com/KaloyanIT).</span><span class="sxs-lookup"><span data-stu-id="d7181-129">This feature was contributed by [@KaloyanIT](https://github.com/KaloyanIT).</span></span>

<span data-ttu-id="d7181-130">Las instancias de <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> permiten la configuración de <xref:Microsoft.EntityFrameworkCore.ModelBuilder> para cada tipo de entidad que se incluya en su propia clase de configuración.</span><span class="sxs-lookup"><span data-stu-id="d7181-130"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> instances allow <xref:Microsoft.EntityFrameworkCore.ModelBuilder> configuration for a each entity type to be contained in its own configuration class.</span></span> <span data-ttu-id="d7181-131">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7181-131">For example:</span></span>

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

<span data-ttu-id="d7181-132">Normalmente, es necesario crear una instancia de esta clase de configuración y llamarla desde <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="d7181-132">Normally, this configuration class must be instantiated and called into from <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d7181-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7181-133">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

<span data-ttu-id="d7181-134">A partir de EF Core 6.0, se puede colocar un elemento `EntityTypeConfigurationAttribute` en el tipo de entidad para que EF Core pueda encontrar y usar la configuración adecuada.</span><span class="sxs-lookup"><span data-stu-id="d7181-134">Starting with EF Core 6.0, an `EntityTypeConfigurationAttribute` can be placed on the entity type such that EF Core can find and use appropriate configuration.</span></span> <span data-ttu-id="d7181-135">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7181-135">For example:</span></span>

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

<span data-ttu-id="d7181-136">Este atributo significa que EF Core usará la implementación de `IEntityTypeConfiguration` especificada siempre que el tipo de entidad `Book` se incluya en un modelo.</span><span class="sxs-lookup"><span data-stu-id="d7181-136">This attribute means that EF Core will use the specified `IEntityTypeConfiguration` implementation whenever the `Book` entity type is included in a model.</span></span> <span data-ttu-id="d7181-137">El tipo de entidad se incluye en un modelo mediante uno de los mecanismos normales.</span><span class="sxs-lookup"><span data-stu-id="d7181-137">The entity type is included in a model using one of the normal mechanisms.</span></span> <span data-ttu-id="d7181-138">Por ejemplo, mediante la creación de una propiedad <xref:Microsoft.EntityFrameworkCore.DbSet%601> para el tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="d7181-138">For example, by creating a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the entity type:</span></span>

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

<span data-ttu-id="d7181-139">O bien, mediante su registro en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span><span class="sxs-lookup"><span data-stu-id="d7181-139">Or by registering it in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> <span data-ttu-id="d7181-140">Los tipos `EntityTypeConfigurationAttribute` no se detectarán automáticamente en un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="d7181-140">`EntityTypeConfigurationAttribute` types will not be automatically discovered in an assembly.</span></span> <span data-ttu-id="d7181-141">Los tipos de entidad se deben agregar al modelo antes de que se detecte el atributo en ese tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="d7181-141">Entity types must be added to the model before the attribute will be discovered on that entity type.</span></span>

### <a name="translate-tostring-on-sqlite"></a><span data-ttu-id="d7181-142">Traducción de ToString en SQLite</span><span class="sxs-lookup"><span data-stu-id="d7181-142">Translate ToString on SQLite</span></span>

<span data-ttu-id="d7181-143">Problema de GitHub [número 17223](https://github.com/dotnet/efcore/issues/17223).</span><span class="sxs-lookup"><span data-stu-id="d7181-143">GitHub Issue: [#17223](https://github.com/dotnet/efcore/issues/17223).</span></span> <span data-ttu-id="d7181-144">Esta característica la ha aportado [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span><span class="sxs-lookup"><span data-stu-id="d7181-144">This feature was contributed by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span>

<span data-ttu-id="d7181-145">Las llamadas a <xref:System.Object.ToString> ahora se traducen a SQL cuando se usa el proveedor de base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="d7181-145">Calls to <xref:System.Object.ToString> are now translated to SQL when using the SQLite database provider.</span></span> <span data-ttu-id="d7181-146">Esto puede ser útil para las búsquedas de texto que implican columnas que no son de cadena.</span><span class="sxs-lookup"><span data-stu-id="d7181-146">This can be useful for text searches involving non-string columns.</span></span> <span data-ttu-id="d7181-147">Por ejemplo, considere un tipo de entidad `User` que almacena los números de teléfono como valores numéricos:</span><span class="sxs-lookup"><span data-stu-id="d7181-147">For example, consider a `User` entity type that stores phone numbers as numeric values:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

<span data-ttu-id="d7181-148">Se puede usar `ToString` para convertir el número en una cadena de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d7181-148">`ToString` can be used to convert the number to a string in the database.</span></span> <span data-ttu-id="d7181-149">Después, se puede usar esta cadena con una función como `LIKE` para buscar números que coincidan con un patrón.</span><span class="sxs-lookup"><span data-stu-id="d7181-149">We can then use this string with a function such as `LIKE` to find numbers that match a pattern.</span></span> <span data-ttu-id="d7181-150">Por ejemplo, para buscar todos los números que contienen 555:</span><span class="sxs-lookup"><span data-stu-id="d7181-150">For example, to find all numbers containing 555:</span></span>

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

<span data-ttu-id="d7181-151">Esto se traduce en el siguiente código SQL cuando se usa una base de datos de SQLite:</span><span class="sxs-lookup"><span data-stu-id="d7181-151">This translates to the following SQL when using a SQLite database:</span></span>

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

<span data-ttu-id="d7181-152">Tenga en cuenta que la traducción de <xref:System.Object.ToString> para SQL Server ya se admite en EF Core 5.0 y también puede ser compatible con otros proveedores de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="d7181-152">Note that translation of <xref:System.Object.ToString> for SQL Server is already supported in EF Core 5.0, and may also be supported by other database providers.</span></span>

### <a name="effunctionsrandom"></a><span data-ttu-id="d7181-153">EF.Functions.Random</span><span class="sxs-lookup"><span data-stu-id="d7181-153">EF.Functions.Random</span></span>

<span data-ttu-id="d7181-154">Problema de GitHub [número 16141](https://github.com/dotnet/efcore/issues/16141).</span><span class="sxs-lookup"><span data-stu-id="d7181-154">GitHub Issue: [#16141](https://github.com/dotnet/efcore/issues/16141).</span></span> <span data-ttu-id="d7181-155">Esta característica la ha aportado [@RaymondHuy](https://github.com/RaymondHuy).</span><span class="sxs-lookup"><span data-stu-id="d7181-155">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="d7181-156">`EF.Functions.Random` se asigna a una función de base de datos que devuelve un número seudoaleatorio entre 0 y 1, ambos no incluidos.</span><span class="sxs-lookup"><span data-stu-id="d7181-156">`EF.Functions.Random` maps to a database function returning a pseudo-random number between 0 and 1 exclusive.</span></span> <span data-ttu-id="d7181-157">Las traducciones se han implementado en el repositorio de EF Core para SQL Server, SQLite y Cosmos.</span><span class="sxs-lookup"><span data-stu-id="d7181-157">Translations have been implemented in the EF Core repo for SQL Server, SQLite, and Cosmos.</span></span> <span data-ttu-id="d7181-158">Por ejemplo, considere un tipo de entidad `User` con una propiedad `Popularity`:</span><span class="sxs-lookup"><span data-stu-id="d7181-158">For example, consider a `User` entity type with a `Popularity` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

<span data-ttu-id="d7181-159">`Popularity` puede tener valores entre 1 y 5, ambos incluidos.</span><span class="sxs-lookup"><span data-stu-id="d7181-159">`Popularity` can have values from 1 to 5 inclusive.</span></span> <span data-ttu-id="d7181-160">Con `EF.Functions.Random`, podemos escribir una consulta para devolver todos los usuarios con una popularidad elegida aleatoriamente:</span><span class="sxs-lookup"><span data-stu-id="d7181-160">Using `EF.Functions.Random` we can write a query to return all users with a randomly chosen popularity:</span></span>

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

<span data-ttu-id="d7181-161">Esto se traduce en el siguiente código SQL cuando se usa una base de datos de SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d7181-161">This translates to the following SQL when using a SQL Server database:</span></span>

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a><span data-ttu-id="d7181-162">Compatibilidad con columnas dispersas de SQL Server</span><span class="sxs-lookup"><span data-stu-id="d7181-162">Support for SQL Server sparse columns</span></span>

<span data-ttu-id="d7181-163">Problema de GitHub [número 8023](https://github.com/dotnet/efcore/issues/8023).</span><span class="sxs-lookup"><span data-stu-id="d7181-163">GitHub Issue: [#8023](https://github.com/dotnet/efcore/issues/8023).</span></span>

<span data-ttu-id="d7181-164">Las [columnas dispersas](/sql/relational-databases/tables/use-sparse-columns) de SQL Server son columnas ordinarias que se optimizan para almacenar valores NULL.</span><span class="sxs-lookup"><span data-stu-id="d7181-164">SQL Server [sparse columns](/sql/relational-databases/tables/use-sparse-columns) are ordinary columns that are optimized to store null values.</span></span> <span data-ttu-id="d7181-165">Esto puede ser útil cuando se usa la [asignación de herencia de tabla por jerarquía](xref:core/modeling/inheritance), donde las propiedades de un subtipo poco usado darán como resultado valores de columna NULL para la mayoría de las filas de la tabla.</span><span class="sxs-lookup"><span data-stu-id="d7181-165">This can be useful when using [TPH inheritance mapping](xref:core/modeling/inheritance) where properties of a rarely used subtype will result in null column values for most rows in the table.</span></span> <span data-ttu-id="d7181-166">Por ejemplo, considere una clase `ForumModerator` que se extiende desde `ForumUser`:</span><span class="sxs-lookup"><span data-stu-id="d7181-166">For example, consider a `ForumModerator` class that extends from `ForumUser`:</span></span>

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

<span data-ttu-id="d7181-167">Puede haber millones de usuarios, pero solo unos pocos de ellos son moderadores.</span><span class="sxs-lookup"><span data-stu-id="d7181-167">There may be millions of users, with only a handful of these being moderators.</span></span> <span data-ttu-id="d7181-168">Esto significa que, en este caso, puede tener sentido la asignación de `ForumName` como disperso.</span><span class="sxs-lookup"><span data-stu-id="d7181-168">This means mapping the `ForumName` as sparse might make sense here.</span></span> <span data-ttu-id="d7181-169">Esto ahora puede configurarse mediante `IsSparse` en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="d7181-169">This can now be configured using `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="d7181-170">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7181-170">For example:</span></span>

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

<span data-ttu-id="d7181-171">Después, las migraciones de EF Core marcarán la columna como dispersa.</span><span class="sxs-lookup"><span data-stu-id="d7181-171">EF Core migrations will then mark the column as sparse.</span></span> <span data-ttu-id="d7181-172">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7181-172">For example:</span></span>

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="d7181-173">Las columnas dispersas tienen limitaciones.</span><span class="sxs-lookup"><span data-stu-id="d7181-173">Sparse columns have limitations.</span></span> <span data-ttu-id="d7181-174">Le recomendamos que lea la [documentación sobre columnas dispersas de SQL Server](/sql/relational-databases/tables/use-sparse-columns) para asegurarse de que las columnas dispersas son la opción correcta para su escenario.</span><span class="sxs-lookup"><span data-stu-id="d7181-174">Make sure to read the [SQL Server sparse columns documentation](/sql/relational-databases/tables/use-sparse-columns) to ensure that sparse columns are the right choice for your scenario.</span></span>

### <a name="in-memory-database-validate-required-properties-are-not-null"></a><span data-ttu-id="d7181-175">Base de datos en memoria: valide que las propiedades obligatorias no son NULL</span><span class="sxs-lookup"><span data-stu-id="d7181-175">In-memory database: validate required properties are not null</span></span>

<span data-ttu-id="d7181-176">Problema de GitHub [número 10613](https://github.com/dotnet/efcore/issues/10613).</span><span class="sxs-lookup"><span data-stu-id="d7181-176">GitHub Issue: [#10613](https://github.com/dotnet/efcore/issues/10613).</span></span> <span data-ttu-id="d7181-177">Esta característica la ha aportado [@fagnercarvalho](https://github.com/fagnercarvalho).</span><span class="sxs-lookup"><span data-stu-id="d7181-177">This feature was contributed by [@fagnercarvalho](https://github.com/fagnercarvalho).</span></span>

<span data-ttu-id="d7181-178">La base de datos en memoria de EF Core ahora producirá una excepción si se intenta guardar un valor NULL para una propiedad marcada como obligatoria.</span><span class="sxs-lookup"><span data-stu-id="d7181-178">The EF Core in-memory database will now throw an exception if an attempt is made to save a null value for a property marked as required.</span></span> <span data-ttu-id="d7181-179">Por ejemplo, considere un tipo `User` con una propiedad `Username` obligatoria:</span><span class="sxs-lookup"><span data-stu-id="d7181-179">For example, consider a `User` type with a required `Username` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

<span data-ttu-id="d7181-180">Si intenta guardar una entidad con un valor `Username` NULL, se producirá la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="d7181-180">Attempting to save an entity with a null `Username` will result in the following exception:</span></span>

> <span data-ttu-id="d7181-181">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}' (Microsoft.EntityFrameworkCore.DbUpdateException: faltan las propiedades "{'Username'}" obligatorias para la instancia de tipo de entidad "User" con el valor de clave "{Id: 1}").</span><span class="sxs-lookup"><span data-stu-id="d7181-181">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'.</span></span>

<span data-ttu-id="d7181-182">Esta validación se puede deshabilitar si es necesario.</span><span class="sxs-lookup"><span data-stu-id="d7181-182">This validation can be disabled if necessary.</span></span> <span data-ttu-id="d7181-183">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7181-183">For example:</span></span>

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

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a><span data-ttu-id="d7181-184">Mejora de la traducción de SQL Server para IsNullOrWhitespace</span><span class="sxs-lookup"><span data-stu-id="d7181-184">Improved SQL Server translation for IsNullOrWhitespace</span></span>

<span data-ttu-id="d7181-185">Problema de GitHub [número 22916](https://github.com/dotnet/efcore/issues/22916).</span><span class="sxs-lookup"><span data-stu-id="d7181-185">GitHub Issue: [#22916](https://github.com/dotnet/efcore/issues/22916).</span></span> <span data-ttu-id="d7181-186">Esta característica la ha aportado [@Marusyk](https://github.com/Marusyk).</span><span class="sxs-lookup"><span data-stu-id="d7181-186">This feature was contributed by [@Marusyk](https://github.com/Marusyk).</span></span>

<span data-ttu-id="d7181-187">Considere la consulta siguiente:</span><span class="sxs-lookup"><span data-stu-id="d7181-187">Consider the following query:</span></span>

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

<span data-ttu-id="d7181-188">Antes de EF Core 6.0, esto se traducía a lo siguiente en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d7181-188">Before EF Core 6.0, this was translated to the following on SQL Server:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

<span data-ttu-id="d7181-189">Esta traducción se ha mejorado para EF Core 6.0 a:</span><span class="sxs-lookup"><span data-stu-id="d7181-189">This translation has been improved for EF Core 6.0 to:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a><span data-ttu-id="d7181-190">Cambio de los comentarios de las bases de datos a comentarios de código con scaffolding</span><span class="sxs-lookup"><span data-stu-id="d7181-190">Database comments are scaffolded to code comments</span></span>

<span data-ttu-id="d7181-191">Problema de GitHub [número 19113](https://github.com/dotnet/efcore/issues/19113).</span><span class="sxs-lookup"><span data-stu-id="d7181-191">GitHub Issue: [#19113](https://github.com/dotnet/efcore/issues/19113).</span></span> <span data-ttu-id="d7181-192">Esta característica la ha aportado [@ErikEJ](https://github.com/ErikEJ).</span><span class="sxs-lookup"><span data-stu-id="d7181-192">This feature was contributed by [@ErikEJ](https://github.com/ErikEJ).</span></span>

<span data-ttu-id="d7181-193">Ahora se aplica scaffolding a los comentarios en tablas y columnas de SQL para convertirlos en los tipos de entidad que se crean al aplicar [ingeniería inversa a un modelo de EF Core](xref:core/managing-schemas/scaffolding) de una base de datos de SQL Server existente.</span><span class="sxs-lookup"><span data-stu-id="d7181-193">Comments on SQL tables and columns are now scaffolded into the entity types created when [reverse-engineering an EF Core model](xref:core/managing-schemas/scaffolding) from an existing SQL Server database.</span></span> <span data-ttu-id="d7181-194">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7181-194">For example:</span></span>

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

## <a name="microsoftdatasqlite-60-preview-1"></a><span data-ttu-id="d7181-195">Microsoft.Data.Sqlite 6.0, versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="d7181-195">Microsoft.Data.Sqlite 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="d7181-196">Puede ejecutar todos los ejemplos de la versión preliminar 1 que se muestran a continuación y depurar en ellos. Para hacerlo, [descargue el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="d7181-196">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="savepoints-api"></a><span data-ttu-id="d7181-197">API de puntos de retorno</span><span class="sxs-lookup"><span data-stu-id="d7181-197">Savepoints API</span></span>

<span data-ttu-id="d7181-198">Problema de GitHub [número 20228](https://github.com/dotnet/efcore/issues/20228).</span><span class="sxs-lookup"><span data-stu-id="d7181-198">GitHub Issue: [#20228](https://github.com/dotnet/efcore/issues/20228).</span></span>

<span data-ttu-id="d7181-199">Hemos estandarizado [una API común para puntos de retorno en proveedores de ADO.NET](https://github.com/dotnet/runtime/issues/33397).</span><span class="sxs-lookup"><span data-stu-id="d7181-199">We have been standardizing on [a common API for savepoints in ADO.NET providers](https://github.com/dotnet/runtime/issues/33397).</span></span> <span data-ttu-id="d7181-200">Microsoft.Data.Sqlite ahora admite esta API, incluido lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d7181-200">Microsoft.Data.Sqlite now supports this API, including:</span></span>

- <span data-ttu-id="d7181-201"><xref:System.Data.Common.DbTransaction.Save(System.String)> para crear un punto de retorno en la transacción</span><span class="sxs-lookup"><span data-stu-id="d7181-201"><xref:System.Data.Common.DbTransaction.Save(System.String)> to create a savepoint in the transaction</span></span>
- <span data-ttu-id="d7181-202"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> para revertir a un punto de retorno anterior</span><span class="sxs-lookup"><span data-stu-id="d7181-202"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> to roll back to a previous savepoint</span></span>
- <span data-ttu-id="d7181-203"><xref:System.Data.Common.DbTransaction.Release(System.String)> para liberar un punto de retorno</span><span class="sxs-lookup"><span data-stu-id="d7181-203"><xref:System.Data.Common.DbTransaction.Release(System.String)> to release a savepoint</span></span>

<span data-ttu-id="d7181-204">El uso de un punto de retorno permite que parte de una transacción se revierta sin necesidad de revertirla entera.</span><span class="sxs-lookup"><span data-stu-id="d7181-204">Using a savepoint allows part of a transaction to be rolled back without rolling back the entire transaction.</span></span> <span data-ttu-id="d7181-205">Por ejemplo, el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d7181-205">For example, the code below:</span></span>

- <span data-ttu-id="d7181-206">Crea una transacción.</span><span class="sxs-lookup"><span data-stu-id="d7181-206">Creates a transaction</span></span>
- <span data-ttu-id="d7181-207">Envía una actualización a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d7181-207">Sends an update to the database</span></span>
- <span data-ttu-id="d7181-208">Crea un punto de retorno.</span><span class="sxs-lookup"><span data-stu-id="d7181-208">Creates a savepoint</span></span>
- <span data-ttu-id="d7181-209">Envía otra actualización a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d7181-209">Sends another update to the database</span></span>
- <span data-ttu-id="d7181-210">Revierte al punto de retorno creado previamente.</span><span class="sxs-lookup"><span data-stu-id="d7181-210">Rolls back to the savepoint previous created</span></span>
- <span data-ttu-id="d7181-211">Confirma la transacción.</span><span class="sxs-lookup"><span data-stu-id="d7181-211">Commits the transaction</span></span>

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

<span data-ttu-id="d7181-212">Esto hará que la primera actualización se confirme en la base de datos, pero no la segunda porque el punto de retorno se revirtió antes de confirmar la transacción.</span><span class="sxs-lookup"><span data-stu-id="d7181-212">This will result in the first update being committed to the database, while the second update is not committed since the savepoint was rolled back before committing the transaction.</span></span>

### <a name="command-timeout-in-the-connection-string"></a><span data-ttu-id="d7181-213">Tiempo de espera del comando en la cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="d7181-213">Command timeout in the connection string</span></span>

<span data-ttu-id="d7181-214">Problema de GitHub [número 22505](https://github.com/dotnet/efcore/issues/22505).</span><span class="sxs-lookup"><span data-stu-id="d7181-214">GitHub Issue: [#22505](https://github.com/dotnet/efcore/issues/22505).</span></span> <span data-ttu-id="d7181-215">Esta característica la ha aportado [@nmichels](https://github.com/nmichels).</span><span class="sxs-lookup"><span data-stu-id="d7181-215">This feature was contributed by [@nmichels](https://github.com/nmichels).</span></span>

<span data-ttu-id="d7181-216">Los proveedores de ADO.NET admiten dos tiempos de espera distintos:</span><span class="sxs-lookup"><span data-stu-id="d7181-216">ADO.NET providers support two distinct timeouts:</span></span>

- <span data-ttu-id="d7181-217">El tiempo de espera de conexión, que determina el tiempo máximo de espera al establecer una conexión con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d7181-217">The connection timeout, which determines the maximum time to wait when making a connection to the database.</span></span>
- <span data-ttu-id="d7181-218">El tiempo de espera del comando, que determina el tiempo máximo que hay que esperar hasta que se complete la ejecución de un comando.</span><span class="sxs-lookup"><span data-stu-id="d7181-218">The command timeout, which determines the maximum time to wait for a command to complete executing.</span></span>

<span data-ttu-id="d7181-219">El tiempo de espera del comando se puede establecer desde el código mediante <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="d7181-219">The command timeout can be set from code using <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d7181-220">Muchos proveedores ahora también exponen el tiempo de espera del comando en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="d7181-220">Many providers are now also exposing this command timeout in the connection string.</span></span> <span data-ttu-id="d7181-221">Microsoft.Data.Sqlite sigue esta tendencia con la palabra clave `Command Timeout` de la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="d7181-221">Microsoft.Data.Sqlite is following this trend with the `Command Timeout` connection string keyword.</span></span> <span data-ttu-id="d7181-222">Por ejemplo, `"Command Timeout=60;DataSource=test.db"` usará 60 segundos como tiempo de espera predeterminado para los comandos que cree la conexión.</span><span class="sxs-lookup"><span data-stu-id="d7181-222">For example, `"Command Timeout=60;DataSource=test.db"` will use 60 seconds as the default timeout for commands created by the connection.</span></span>

> [!TIP]
> <span data-ttu-id="d7181-223">SQLite trata `Default Timeout` como sinónimo de `Command Timeout`, por lo que se puede usar en su lugar si se prefiere.</span><span class="sxs-lookup"><span data-stu-id="d7181-223">Sqlite treats `Default Timeout` as a synonym for `Command Timeout` and so can be used instead if preferred.</span></span>
