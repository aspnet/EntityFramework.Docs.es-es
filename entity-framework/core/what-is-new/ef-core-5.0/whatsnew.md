---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418951"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="5a8ea-103">Novedades en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="5a8ea-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="5a8ea-104">EF Core 5.0 está actualmente en desarrollo.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="5a8ea-105">Esta página contendrá información general sobre los cambios interesantes introducidos en cada versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="5a8ea-106">Esta página no duplica el [plan para EF Core 5.0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="5a8ea-107">En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="5a8ea-108">A medida que se publique el contenido, se agregarán vínculos que redirigirán de esta página a la documentación oficial.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-5"></a><span data-ttu-id="5a8ea-109">Versión preliminar 5</span><span class="sxs-lookup"><span data-stu-id="5a8ea-109">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="5a8ea-110">Intercalaciones de bases de datos</span><span class="sxs-lookup"><span data-stu-id="5a8ea-110">Database collations</span></span>

<span data-ttu-id="5a8ea-111">La intercalación predeterminada de una base de datos puede especificarse ahora en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-111">The default collation for a database can now be specified in the EF model.</span></span>
<span data-ttu-id="5a8ea-112">Este pasará a las migraciones generadas para establecer la intercalación cuando se cree la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-112">This will flow through to generated migrations to set the collation when the database is created.</span></span>
<span data-ttu-id="5a8ea-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-113">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="5a8ea-114">A continuación, las migraciones generan lo siguiente para crear la base de datos en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-114">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="5a8ea-115">También se puede especificar la intercalación que se va a usar para columnas de bases de datos concretas.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-115">The collation to use for specific database columns can also be specified.</span></span>
<span data-ttu-id="5a8ea-116">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-116">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="5a8ea-117">En el caso de las que no usan migraciones, se aplica a las intercalaciones ingeniería inversa desde la base de datos, al aplicar scaffolding a DbContext.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-117">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="5a8ea-118">Por último, `EF.Functions.Collate()` permite realizar consultas ad-hoc mediante diferentes intercalaciones.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-118">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span>
<span data-ttu-id="5a8ea-119">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-119">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="5a8ea-120">De este modo se generará la siguiente consulta para SQL Server:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-120">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="5a8ea-121">Tenga en cuenta que las intercalaciones ad-hoc deben usarse con cuidado, ya que pueden afectar negativamente al rendimiento de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-121">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="5a8ea-122">En el problema [n.º 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-122">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="5a8ea-123">Transmisión de argumentos a IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="5a8ea-123">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="5a8ea-124">Los argumentos se transmiten ahora desde la línea de comandos al método de `CreateDbContext` de [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-124">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="5a8ea-125">Por ejemplo, para indicar que se trata de una compilación de desarrollo, se puede pasar un argumento personalizado (por ejemplo, `dev`) en la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-125">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="5a8ea-126">Este argumento pasará entonces a la factoría, donde se puede usar para controlar cómo se crea e inicializa el contexto.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-126">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span>
<span data-ttu-id="5a8ea-127">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-127">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="5a8ea-128">En el problema [n.º 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-128">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="5a8ea-129">Consultas de no seguimiento con resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="5a8ea-129">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="5a8ea-130">Ahora se pueden configurar consultas de no seguimiento para hacer la resolución de identidad.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-130">No-tracking queries can now be configured to perform identity resolution.</span></span>
<span data-ttu-id="5a8ea-131">Por ejemplo, la siguiente consulta creará una instancia de Blog para cada Post, aunque cada blog tenga la misma clave principal.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-131">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="5a8ea-132">Sin embargo, a costa de que por lo general sea ligeramente más lento y siempre use más memoria, se puede cambiar esta consulta para asegurarse de que solo se crea una instancia de Blog:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-132">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="5a8ea-133">Tenga en cuenta que esto solo es útil para consultas de no seguimiento, ya que todas las consultas de seguimiento ya muestran este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-133">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="5a8ea-134">Además, después de la revisión de la API, se cambiará la sintaxis de `PerformIdentityResolution`.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-134">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span>
<span data-ttu-id="5a8ea-135">Consulte el problema [n.º 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-135">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="5a8ea-136">En el problema [n.º 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-136">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="5a8ea-137">Columnas calculadas almacenadas (persistentes)</span><span class="sxs-lookup"><span data-stu-id="5a8ea-137">Stored (persisted) computed columns</span></span>

<span data-ttu-id="5a8ea-138">La mayoría de las bases de datos permiten almacenar valores de columnas calculadas después del cálculo.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-138">Most databases allow computed column values to be stored after computation.</span></span>
<span data-ttu-id="5a8ea-139">Aunque ocupa espacio en disco, la columna se calcula solo una vez al actualizar, en lugar de cada vez que se recupera su valor.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-139">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span>
<span data-ttu-id="5a8ea-140">Esto también permite indizar la columna para algunas bases de datos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-140">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="5a8ea-141">EF Core 5.0 permite configurar las columnas calculadas como almacenadas.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-141">EF Core 5.0 allows computed columns to be configured as stored.</span></span>
<span data-ttu-id="5a8ea-142">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-142">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="5a8ea-143">Columnas calculadas de SQLite</span><span class="sxs-lookup"><span data-stu-id="5a8ea-143">SQLite computed columns</span></span>

<span data-ttu-id="5a8ea-144">EF Core admite ahora columnas calculadas en las bases de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-144">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="5a8ea-145">Versión preliminar 4</span><span class="sxs-lookup"><span data-stu-id="5a8ea-145">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="5a8ea-146">Configuración de la precisión y la escala de la base de datos en el modelo</span><span class="sxs-lookup"><span data-stu-id="5a8ea-146">Configure database precision/scale in model</span></span>

<span data-ttu-id="5a8ea-147">Ahora se puede especificar la precisión y la escala de una propiedad mediante el generador de modelos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-147">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="5a8ea-148">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-148">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="5a8ea-149">La precisión y la escala todavía se pueden establecer mediante el tipo completo de base de datos, como "decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="5a8ea-149">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="5a8ea-150">En el problema [n.º 527](https://github.com/dotnet/EntityFramework.Docs/issues/527) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-150">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="5a8ea-151">Especificación del factor de relleno del índice de SQL Server</span><span class="sxs-lookup"><span data-stu-id="5a8ea-151">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="5a8ea-152">Ahora se puede especificar el factor de relleno al crear un índice en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-152">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="5a8ea-153">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-153">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="5a8ea-154">Versión preliminar 3</span><span class="sxs-lookup"><span data-stu-id="5a8ea-154">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="5a8ea-155">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="5a8ea-155">Filtered Include</span></span>

<span data-ttu-id="5a8ea-156">El método Include ahora admite el filtrado de las entidades incluidas.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-156">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="5a8ea-157">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-157">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="5a8ea-158">Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".</span><span class="sxs-lookup"><span data-stu-id="5a8ea-158">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="5a8ea-159">Para reducir el número de las entidades incluidas, se puede usar Skip y Take.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-159">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="5a8ea-160">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-160">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="5a8ea-161">Esta consulta devolverá los blogs que tengan cinco publicaciones como máximo.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-161">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="5a8ea-162">Vea la [documentación de Include](xref:core/querying/related-data#filtered-include) para obtener información completa.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-162">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="5a8ea-163">Nueva API de ModelBuilder para las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="5a8ea-163">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="5a8ea-164">Las propiedades de navegación se configuran principalmente al [definir relaciones](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-164">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="5a8ea-165">No obstante, se puede usar el nuevo método `Navigation` cuando es necesario realizar una configuración adicional en las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-165">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="5a8ea-166">Por ejemplo, para establecer un campo de respaldo de la navegación cuando el campo no se encuentre por convención:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-166">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="5a8ea-167">Tenga en cuenta que la API de `Navigation` no reemplaza la configuración de la relación,</span><span class="sxs-lookup"><span data-stu-id="5a8ea-167">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="5a8ea-168">sino que permite la configuración adicional de las propiedades de navegación en relaciones ya detectadas o definidas.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-168">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="5a8ea-169">Consulte la [documentación sobre la configuración de las propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-169">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="5a8ea-170">Nuevos parámetros de la línea de comandos para espacios de nombres y cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="5a8ea-170">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="5a8ea-171">Ahora, las migraciones y el scaffolding permiten especificar los espacios de nombres en la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-171">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="5a8ea-172">Esto puede hacerse para, por ejemplo, aplicar ingeniería inversa a una base de datos y colocar las clases Context y Model en diferentes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-172">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="5a8ea-173">Para más información, consulte la documentación sobre [migraciones](xref:core/managing-schemas/migrations/index#namespaces) e [ingeniería inversa](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-173">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="5a8ea-174">Ahora, además, también puede pasarse una cadena de conexión al comando `database-update`:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-174">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="5a8ea-175">Para más información, consulte la [documentación sobre herramientas](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-175">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="5a8ea-176">También se han agregado parámetros equivalentes a los comandos de PowerShell que se usan en la consola del administrador de paquetes de VS.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-176">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="5a8ea-177">EnableDetailedErrors ha vuelto</span><span class="sxs-lookup"><span data-stu-id="5a8ea-177">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="5a8ea-178">Por motivos de rendimiento, EF no realiza comprobaciones de valores NULL adicionales cuando se leen los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-178">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="5a8ea-179">Esto puede dar lugar a excepciones cuya causa principal es difícil de rastrear cuando se encuentra un valor NULL inesperado.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-179">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="5a8ea-180">El uso de `EnableDetailedErrors` agregará una comprobación de valores NULL adicional a las consultas de modo que, para una pequeña sobrecarga de rendimiento, estos errores sean más fáciles de rastrear para averiguar su causa principal.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-180">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="5a8ea-181">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-181">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="5a8ea-182">En el problema [n.º 955](https://github.com/dotnet/EntityFramework.Docs/issues/955) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-182">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="5a8ea-183">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="5a8ea-183">Cosmos partition keys</span></span>

<span data-ttu-id="5a8ea-184">Ahora, es posible especificar en las consultas la clave de partición que va a usarse para una consulta determinada.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-184">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="5a8ea-185">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-185">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="5a8ea-186">En el problema [n.º 2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-186">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="5a8ea-187">Compatibilidad con la función DATALENGTH de SQL Server</span><span class="sxs-lookup"><span data-stu-id="5a8ea-187">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="5a8ea-188">Se puede acceder con el nuevo método `EF.Functions.DataLength`.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-188">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="5a8ea-189">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-189">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="5a8ea-190">Versión preliminar 2</span><span class="sxs-lookup"><span data-stu-id="5a8ea-190">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="5a8ea-191">Uso de un atributo de C# para especificar un campo de respaldo de propiedad</span><span class="sxs-lookup"><span data-stu-id="5a8ea-191">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="5a8ea-192">Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-192">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="5a8ea-193">Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-193">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="5a8ea-194">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-194">For example:</span></span>

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

<span data-ttu-id="5a8ea-195">En el problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-195">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="5a8ea-196">Asignación completa de discriminador</span><span class="sxs-lookup"><span data-stu-id="5a8ea-196">Complete discriminator mapping</span></span>

<span data-ttu-id="5a8ea-197">EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-197">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="5a8ea-198">Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-198">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="5a8ea-199">EF Core 5.0 ahora implementa estas mejoras.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-199">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="5a8ea-200">Por ejemplo, las versiones anteriores de EF Core siempre generaban este SQL para una consulta que devuelve todos los tipos de una jerarquía:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-200">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="5a8ea-201">EF Core 5.0 ahora genera lo siguiente cuando se configura una asignación completa de discriminador:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-201">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="5a8ea-202">Este va a ser el comportamiento predeterminado a partir de la versión preliminar 3.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-202">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="5a8ea-203">Mejoras de rendimiento de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="5a8ea-203">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="5a8ea-204">Se han realizado dos mejoras de rendimiento para SQLIte:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-204">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="5a8ea-205">La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-205">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="5a8ea-206">La inicialización de SqliteConnection ahora es diferida.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-206">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="5a8ea-207">Estas mejoras se encuentran en el proveedor Microsoft.Data.Sqlite de ADO.NET y, por lo tanto, también mejoran el rendimiento fuera de EF Core.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-207">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="5a8ea-208">Versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="5a8ea-208">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="5a8ea-209">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="5a8ea-209">Simple logging</span></span>

<span data-ttu-id="5a8ea-210">Esta característica agrega funcionalidad similar a `Database.Log` en EF6.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-210">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="5a8ea-211">Es decir, proporciona una manera sencilla de obtener registros de EF Core sin necesidad de configurar ningún tipo de plataforma de registro externa.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-211">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="5a8ea-212">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-212">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="5a8ea-213">En el problema [n.º 2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-213">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="5a8ea-214">Forma sencilla de generar contenido SQL</span><span class="sxs-lookup"><span data-stu-id="5a8ea-214">Simple way to get generated SQL</span></span>

<span data-ttu-id="5a8ea-215">EF Core 5.0 presenta el método de extensión `ToQueryString`, que devuelve el SQL que EF Core genera al ejecutar una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-215">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="5a8ea-216">La documentación preliminar se incluye en el [estado semanal de EF del 9 de enero de 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-216">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="5a8ea-217">En el problema [n.º 1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-217">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="5a8ea-218">Uso de un atributo de C# para indicar que una entidad no tiene clave</span><span class="sxs-lookup"><span data-stu-id="5a8ea-218">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="5a8ea-219">Ahora se pueden configurar los tipos de entidad para indicar que no tienen clave mediante el nuevo valor `KeylessAttribute`.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-219">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="5a8ea-220">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-220">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="5a8ea-221">En el problema [n.º 2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-221">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="5a8ea-222">Posibilidad de cambiar la conexión o la cadena de conexión en una instancia inicializada de DbContext</span><span class="sxs-lookup"><span data-stu-id="5a8ea-222">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="5a8ea-223">Ahora es más fácil crear una instancia de DbContext sin ninguna conexión o cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-223">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="5a8ea-224">Además, la conexión o la cadena de conexión ahora también se pueden mutar en la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-224">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="5a8ea-225">Esta característica permite que la misma instancia de contexto se conecte de forma dinámica a diferentes bases de datos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-225">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="5a8ea-226">En el problema [n.º 2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-226">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="5a8ea-227">Proxies de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="5a8ea-227">Change-tracking proxies</span></span>

<span data-ttu-id="5a8ea-228">Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) y [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-228">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="5a8ea-229">A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-229">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="5a8ea-230">Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-230">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="5a8ea-231">En el problema [n.º 2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-231">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="5a8ea-232">Vistas de depuración mejoradas</span><span class="sxs-lookup"><span data-stu-id="5a8ea-232">Enhanced debug views</span></span>

<span data-ttu-id="5a8ea-233">Las vistas de depuración son una forma fácil de consultar los aspectos internos de EF Core al depurar problemas.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-233">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="5a8ea-234">Hace algún tiempo ya se implementó una vista de depuración para el modelo.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-234">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="5a8ea-235">En el caso de EF Core 5.0, hemos facilitado la lectura de la vista de modelo y hemos agregado una nueva vista de depuración para las entidades de las que se ha realizado un seguimiento en el administrador de estado.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-235">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="5a8ea-236">La documentación preliminar se incluye en el [estado semanal de EF del 12 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-236">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="5a8ea-237">En el problema [n.º 2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-237">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="5a8ea-238">Control mejorado de la semántica de valores NULL de base de datos</span><span class="sxs-lookup"><span data-stu-id="5a8ea-238">Improved handling of database null semantics</span></span>

<span data-ttu-id="5a8ea-239">Normalmente, las bases de datos relacionales tratan NULL como valores desconocidos y, por lo tanto, no son iguales a otros valores NULL.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-239">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="5a8ea-240">Mientras, C# trata NULL como un valor definido que se compara igual que cualquier otro valor NULL.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-240">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="5a8ea-241">De forma predeterminada, EF Core traduce las consultas para que usen la semántica de valores NULL de C#.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-241">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="5a8ea-242">EF Core 5.0 mejora en gran medida la eficacia de dichas traducciones.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-242">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="5a8ea-243">En el problema [n.º 1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-243">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="5a8ea-244">Propiedades del indizador</span><span class="sxs-lookup"><span data-stu-id="5a8ea-244">Indexer properties</span></span>

<span data-ttu-id="5a8ea-245">EF Core 5.0 admite la asignación de propiedades de indizador de C#.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-245">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="5a8ea-246">Estas propiedades permiten a las entidades actuar como bolsas de propiedades en las que las columnas se asignan a propiedades con nombre de la bolsa.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-246">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="5a8ea-247">En el problema [n.º 2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-247">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="5a8ea-248">Generación de restricciones CHECK para las asignaciones de enumeración</span><span class="sxs-lookup"><span data-stu-id="5a8ea-248">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="5a8ea-249">Las migraciones de EF Core 5.0 ahora pueden generar restricciones CHECK para las asignaciones de propiedades de enumeración.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-249">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="5a8ea-250">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-250">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="5a8ea-251">En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-251">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="5a8ea-252">IsRelational</span><span class="sxs-lookup"><span data-stu-id="5a8ea-252">IsRelational</span></span>

<span data-ttu-id="5a8ea-253">Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-253">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="5a8ea-254">Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-254">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="5a8ea-255">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-255">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="5a8ea-256">En el problema [n.º 2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-256">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="5a8ea-257">Simultaneidad optimista de Cosmos con mecanismos ETag</span><span class="sxs-lookup"><span data-stu-id="5a8ea-257">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="5a8ea-258">El proveedor de bases de datos de Azure Cosmos DB ya es compatible con la simultaneidad optimista mediante mecanismos ETag.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-258">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="5a8ea-259">Utilice el generador de modelos de OnModelCreating para confirmar un mecanismo ETag:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-259">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="5a8ea-260">Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](https://docs.microsoft.com/ef/core/saving/concurrency), por ejemplo, para implementar reintentos.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-260">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="5a8ea-261">En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-261">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="5a8ea-262">Traducciones de consultas para más construcciones DateTime</span><span class="sxs-lookup"><span data-stu-id="5a8ea-262">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="5a8ea-263">Ahora las consultas que contienen la nueva construcción DateTime se traducen.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-263">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="5a8ea-264">Además, ahora se asignan las funciones de SQL Server que hay a continuación:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-264">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="5a8ea-265">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="5a8ea-265">DateDiffWeek</span></span>
* <span data-ttu-id="5a8ea-266">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="5a8ea-266">DateFromParts</span></span>

<span data-ttu-id="5a8ea-267">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-267">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="5a8ea-268">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-268">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="5a8ea-269">Traducciones de consultas para más construcciones de matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="5a8ea-269">Query translations for more byte array constructs</span></span>

<span data-ttu-id="5a8ea-270">Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-270">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="5a8ea-271">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="5a8ea-271">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="5a8ea-272">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-272">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="5a8ea-273">Traducción de consultas para Reverse</span><span class="sxs-lookup"><span data-stu-id="5a8ea-273">Query translation for Reverse</span></span>

<span data-ttu-id="5a8ea-274">Las consultas que usan `Reverse` ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-274">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="5a8ea-275">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-275">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="5a8ea-276">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-276">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="5a8ea-277">Traducción de consultas para operadores bit a bit</span><span class="sxs-lookup"><span data-stu-id="5a8ea-277">Query translation for bitwise operators</span></span>

<span data-ttu-id="5a8ea-278">Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5a8ea-278">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="5a8ea-279">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-279">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="5a8ea-280">Traducción de consultas para cadenas en Cosmos</span><span class="sxs-lookup"><span data-stu-id="5a8ea-280">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="5a8ea-281">Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-281">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="5a8ea-282">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="5a8ea-282">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
