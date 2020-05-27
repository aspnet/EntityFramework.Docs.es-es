---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 05/11/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: fcb2eb8df99a06eaf3459835347a4027a363b86b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672852"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="ca366-103">Novedades en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="ca366-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="ca366-104">EF Core 5.0 está actualmente en desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ca366-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="ca366-105">Esta página contendrá información general sobre los cambios interesantes introducidos en cada versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="ca366-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="ca366-106">Esta página no duplica el [plan para EF Core 5.0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="ca366-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="ca366-107">En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.</span><span class="sxs-lookup"><span data-stu-id="ca366-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="ca366-108">A medida que se publique el contenido, se agregarán vínculos que redirigirán de esta página a la documentación oficial.</span><span class="sxs-lookup"><span data-stu-id="ca366-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-4"></a><span data-ttu-id="ca366-109">Versión preliminar 4</span><span class="sxs-lookup"><span data-stu-id="ca366-109">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="ca366-110">Configuración de la precisión y la escala de la base de datos en el modelo</span><span class="sxs-lookup"><span data-stu-id="ca366-110">Configure database precision/scale in model</span></span>

<span data-ttu-id="ca366-111">Ahora se puede especificar la precisión y la escala de una propiedad mediante el generador de modelos.</span><span class="sxs-lookup"><span data-stu-id="ca366-111">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="ca366-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-112">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="ca366-113">La precisión y la escala todavía se pueden establecer mediante el tipo completo de base de datos, como "decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="ca366-113">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="ca366-114">En el problema [n.º 527](https://github.com/dotnet/EntityFramework.Docs/issues/527) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-114">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="ca366-115">Especificación del factor de relleno del índice de SQL Server</span><span class="sxs-lookup"><span data-stu-id="ca366-115">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="ca366-116">Ahora se puede especificar el factor de relleno al crear un índice en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ca366-116">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="ca366-117">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-117">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

<span data-ttu-id="ca366-118">En el problema [n.º 2378](https://github.com/dotnet/EntityFramework.Docs/issues/2378) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-118">Documentation is tracked by issue [#2378](https://github.com/dotnet/EntityFramework.Docs/issues/2378).</span></span>

## <a name="preview-3"></a><span data-ttu-id="ca366-119">Versión preliminar 3</span><span class="sxs-lookup"><span data-stu-id="ca366-119">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="ca366-120">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="ca366-120">Filtered Include</span></span>

<span data-ttu-id="ca366-121">El método Include ahora admite el filtrado de las entidades incluidas.</span><span class="sxs-lookup"><span data-stu-id="ca366-121">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="ca366-122">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-122">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="ca366-123">Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".</span><span class="sxs-lookup"><span data-stu-id="ca366-123">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="ca366-124">Para reducir el número de las entidades incluidas, se puede usar Skip y Take.</span><span class="sxs-lookup"><span data-stu-id="ca366-124">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="ca366-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-125">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="ca366-126">Esta consulta devolverá los blogs que tengan cinco publicaciones como máximo.</span><span class="sxs-lookup"><span data-stu-id="ca366-126">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="ca366-127">Vea la [documentación de Include](xref:core/querying/related-data#filtered-include) para obtener información completa.</span><span class="sxs-lookup"><span data-stu-id="ca366-127">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="ca366-128">Nueva API de ModelBuilder para las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="ca366-128">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="ca366-129">Las propiedades de navegación se configuran principalmente al [definir relaciones](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ca366-129">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="ca366-130">No obstante, se puede usar el nuevo método `Navigation` cuando es necesario realizar una configuración adicional en las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="ca366-130">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="ca366-131">Por ejemplo, para establecer un campo de respaldo de la navegación cuando el campo no se encuentre por convención:</span><span class="sxs-lookup"><span data-stu-id="ca366-131">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="ca366-132">Tenga en cuenta que la API de `Navigation` no reemplaza la configuración de la relación,</span><span class="sxs-lookup"><span data-stu-id="ca366-132">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="ca366-133">sino que permite la configuración adicional de las propiedades de navegación en relaciones ya detectadas o definidas.</span><span class="sxs-lookup"><span data-stu-id="ca366-133">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="ca366-134">Consulte la [documentación sobre la configuración de las propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="ca366-134">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="ca366-135">Nuevos parámetros de la línea de comandos para espacios de nombres y cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="ca366-135">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="ca366-136">Ahora, las migraciones y el scaffolding permiten especificar los espacios de nombres en la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="ca366-136">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="ca366-137">Esto puede hacerse para, por ejemplo, aplicar ingeniería inversa a una base de datos y colocar las clases Context y Model en diferentes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="ca366-137">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="ca366-138">Para más información, consulte la documentación sobre [migraciones](xref:core/managing-schemas/migrations/index#namespaces) e [ingeniería inversa](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="ca366-138">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="ca366-139">Ahora, además, también puede pasarse una cadena de conexión al comando `database-update`:</span><span class="sxs-lookup"><span data-stu-id="ca366-139">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="ca366-140">Para más información, consulte la [documentación sobre herramientas](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="ca366-140">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="ca366-141">También se han agregado parámetros equivalentes a los comandos de PowerShell que se usan en la consola del administrador de paquetes de VS.</span><span class="sxs-lookup"><span data-stu-id="ca366-141">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="ca366-142">EnableDetailedErrors ha vuelto</span><span class="sxs-lookup"><span data-stu-id="ca366-142">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="ca366-143">Por motivos de rendimiento, EF no realiza comprobaciones de valores NULL adicionales cuando se leen los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ca366-143">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="ca366-144">Esto puede dar lugar a excepciones cuya causa principal es difícil de rastrear cuando se encuentra un valor NULL inesperado.</span><span class="sxs-lookup"><span data-stu-id="ca366-144">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="ca366-145">El uso de `EnableDetailedErrors` agregará una comprobación de valores NULL adicional a las consultas de modo que, para una pequeña sobrecarga de rendimiento, estos errores sean más fáciles de rastrear para averiguar su causa principal.</span><span class="sxs-lookup"><span data-stu-id="ca366-145">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="ca366-146">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-146">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="ca366-147">En el problema [n.º 955](https://github.com/dotnet/EntityFramework.Docs/issues/955) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-147">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="ca366-148">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="ca366-148">Cosmos partition keys</span></span>

<span data-ttu-id="ca366-149">Ahora, es posible especificar en las consultas la clave de partición que va a usarse para una consulta determinada.</span><span class="sxs-lookup"><span data-stu-id="ca366-149">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="ca366-150">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-150">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="ca366-151">En el problema [n.º 2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-151">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="ca366-152">Compatibilidad con la función DATALENGTH de SQL Server</span><span class="sxs-lookup"><span data-stu-id="ca366-152">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="ca366-153">Se puede acceder con el nuevo método `EF.Functions.DataLength`.</span><span class="sxs-lookup"><span data-stu-id="ca366-153">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="ca366-154">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-154">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="ca366-155">Versión preliminar 2</span><span class="sxs-lookup"><span data-stu-id="ca366-155">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="ca366-156">Uso de un atributo de C# para especificar un campo de respaldo de propiedad</span><span class="sxs-lookup"><span data-stu-id="ca366-156">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="ca366-157">Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="ca366-157">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="ca366-158">Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente.</span><span class="sxs-lookup"><span data-stu-id="ca366-158">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="ca366-159">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-159">For example:</span></span>

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

<span data-ttu-id="ca366-160">En el problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-160">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="ca366-161">Asignación completa de discriminador</span><span class="sxs-lookup"><span data-stu-id="ca366-161">Complete discriminator mapping</span></span>

<span data-ttu-id="ca366-162">EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="ca366-162">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="ca366-163">Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador.</span><span class="sxs-lookup"><span data-stu-id="ca366-163">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="ca366-164">EF Core 5.0 ahora implementa estas mejoras.</span><span class="sxs-lookup"><span data-stu-id="ca366-164">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="ca366-165">Por ejemplo, las versiones anteriores de EF Core siempre generaban este SQL para una consulta que devuelve todos los tipos de una jerarquía:</span><span class="sxs-lookup"><span data-stu-id="ca366-165">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="ca366-166">EF Core 5.0 ahora genera lo siguiente cuando se configura una asignación completa de discriminador:</span><span class="sxs-lookup"><span data-stu-id="ca366-166">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="ca366-167">Este va a ser el comportamiento predeterminado a partir de la versión preliminar 3.</span><span class="sxs-lookup"><span data-stu-id="ca366-167">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="ca366-168">Mejoras de rendimiento de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="ca366-168">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="ca366-169">Se han realizado dos mejoras de rendimiento para SQLIte:</span><span class="sxs-lookup"><span data-stu-id="ca366-169">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="ca366-170">La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.</span><span class="sxs-lookup"><span data-stu-id="ca366-170">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="ca366-171">La inicialización de SqliteConnection ahora es diferida.</span><span class="sxs-lookup"><span data-stu-id="ca366-171">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="ca366-172">Estas mejoras se encuentran en el proveedor Microsoft.Data.Sqlite de ADO.NET y, por lo tanto, también mejoran el rendimiento fuera de EF Core.</span><span class="sxs-lookup"><span data-stu-id="ca366-172">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="ca366-173">Versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="ca366-173">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="ca366-174">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="ca366-174">Simple logging</span></span>

<span data-ttu-id="ca366-175">Esta característica agrega funcionalidad similar a `Database.Log` en EF6.</span><span class="sxs-lookup"><span data-stu-id="ca366-175">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="ca366-176">Es decir, proporciona una manera sencilla de obtener registros de EF Core sin necesidad de configurar ningún tipo de plataforma de registro externa.</span><span class="sxs-lookup"><span data-stu-id="ca366-176">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="ca366-177">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="ca366-177">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="ca366-178">En el problema [n.º 2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="ca366-178">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="ca366-179">Forma sencilla de generar contenido SQL</span><span class="sxs-lookup"><span data-stu-id="ca366-179">Simple way to get generated SQL</span></span>

<span data-ttu-id="ca366-180">EF Core 5.0 presenta el método de extensión `ToQueryString`, que devuelve el SQL que EF Core genera al ejecutar una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="ca366-180">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="ca366-181">La documentación preliminar se incluye en el [estado semanal de EF del 9 de enero de 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="ca366-181">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="ca366-182">En el problema [n.º 1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="ca366-182">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="ca366-183">Uso de un atributo de C# para indicar que una entidad no tiene clave</span><span class="sxs-lookup"><span data-stu-id="ca366-183">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="ca366-184">Ahora se pueden configurar los tipos de entidad para indicar que no tienen clave mediante el nuevo valor `KeylessAttribute`.</span><span class="sxs-lookup"><span data-stu-id="ca366-184">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="ca366-185">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-185">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="ca366-186">En el problema [n.º 2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-186">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="ca366-187">Posibilidad de cambiar la conexión o la cadena de conexión en una instancia inicializada de DbContext</span><span class="sxs-lookup"><span data-stu-id="ca366-187">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="ca366-188">Ahora es más fácil crear una instancia de DbContext sin ninguna conexión o cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="ca366-188">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="ca366-189">Además, la conexión o la cadena de conexión ahora también se pueden mutar en la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="ca366-189">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="ca366-190">Esta característica permite que la misma instancia de contexto se conecte de forma dinámica a diferentes bases de datos.</span><span class="sxs-lookup"><span data-stu-id="ca366-190">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="ca366-191">En el problema [n.º 2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-191">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="ca366-192">Proxies de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="ca366-192">Change-tracking proxies</span></span>

<span data-ttu-id="ca366-193">Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) y [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="ca366-193">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="ca366-194">A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios.</span><span class="sxs-lookup"><span data-stu-id="ca366-194">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="ca366-195">Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.</span><span class="sxs-lookup"><span data-stu-id="ca366-195">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="ca366-196">En el problema [n.º 2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-196">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="ca366-197">Vistas de depuración mejoradas</span><span class="sxs-lookup"><span data-stu-id="ca366-197">Enhanced debug views</span></span>

<span data-ttu-id="ca366-198">Las vistas de depuración son una forma fácil de consultar los aspectos internos de EF Core al depurar problemas.</span><span class="sxs-lookup"><span data-stu-id="ca366-198">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="ca366-199">Hace algún tiempo ya se implementó una vista de depuración para el modelo.</span><span class="sxs-lookup"><span data-stu-id="ca366-199">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="ca366-200">En el caso de EF Core 5.0, hemos facilitado la lectura de la vista de modelo y hemos agregado una nueva vista de depuración para las entidades de las que se ha realizado un seguimiento en el administrador de estado.</span><span class="sxs-lookup"><span data-stu-id="ca366-200">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="ca366-201">La documentación preliminar se incluye en el [estado semanal de EF del 12 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="ca366-201">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="ca366-202">En el problema [n.º 2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="ca366-202">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="ca366-203">Control mejorado de la semántica de valores NULL de base de datos</span><span class="sxs-lookup"><span data-stu-id="ca366-203">Improved handling of database null semantics</span></span>

<span data-ttu-id="ca366-204">Normalmente, las bases de datos relacionales tratan NULL como valores desconocidos y, por lo tanto, no son iguales a otros valores NULL.</span><span class="sxs-lookup"><span data-stu-id="ca366-204">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="ca366-205">Mientras, C# trata NULL como un valor definido que se compara igual que cualquier otro valor NULL.</span><span class="sxs-lookup"><span data-stu-id="ca366-205">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="ca366-206">De forma predeterminada, EF Core traduce las consultas para que usen la semántica de valores NULL de C#.</span><span class="sxs-lookup"><span data-stu-id="ca366-206">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="ca366-207">EF Core 5.0 mejora en gran medida la eficacia de dichas traducciones.</span><span class="sxs-lookup"><span data-stu-id="ca366-207">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="ca366-208">En el problema [n.º 1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-208">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="ca366-209">Propiedades del indizador</span><span class="sxs-lookup"><span data-stu-id="ca366-209">Indexer properties</span></span>

<span data-ttu-id="ca366-210">EF Core 5.0 admite la asignación de propiedades de indizador de C#.</span><span class="sxs-lookup"><span data-stu-id="ca366-210">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="ca366-211">Estas propiedades permiten a las entidades actuar como bolsas de propiedades en las que las columnas se asignan a propiedades con nombre de la bolsa.</span><span class="sxs-lookup"><span data-stu-id="ca366-211">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="ca366-212">En el problema [n.º 2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-212">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="ca366-213">Generación de restricciones CHECK para las asignaciones de enumeración</span><span class="sxs-lookup"><span data-stu-id="ca366-213">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="ca366-214">Las migraciones de EF Core 5.0 ahora pueden generar restricciones CHECK para las asignaciones de propiedades de enumeración.</span><span class="sxs-lookup"><span data-stu-id="ca366-214">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="ca366-215">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-215">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="ca366-216">En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-216">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="ca366-217">IsRelational</span><span class="sxs-lookup"><span data-stu-id="ca366-217">IsRelational</span></span>

<span data-ttu-id="ca366-218">Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`.</span><span class="sxs-lookup"><span data-stu-id="ca366-218">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="ca366-219">Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="ca366-219">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="ca366-220">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-220">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="ca366-221">En el problema [n.º 2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-221">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="ca366-222">Simultaneidad optimista de Cosmos con mecanismos ETag</span><span class="sxs-lookup"><span data-stu-id="ca366-222">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="ca366-223">El proveedor de bases de datos de Azure Cosmos DB ya es compatible con la simultaneidad optimista mediante mecanismos ETag.</span><span class="sxs-lookup"><span data-stu-id="ca366-223">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="ca366-224">Utilice el generador de modelos de OnModelCreating para confirmar un mecanismo ETag:</span><span class="sxs-lookup"><span data-stu-id="ca366-224">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="ca366-225">Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](https://docs.microsoft.com/ef/core/saving/concurrency), por ejemplo, para implementar reintentos.</span><span class="sxs-lookup"><span data-stu-id="ca366-225">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="ca366-226">En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-226">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="ca366-227">Traducciones de consultas para más construcciones DateTime</span><span class="sxs-lookup"><span data-stu-id="ca366-227">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="ca366-228">Ahora las consultas que contienen la nueva construcción DateTime se traducen.</span><span class="sxs-lookup"><span data-stu-id="ca366-228">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="ca366-229">Además, ahora se asignan las funciones de SQL Server que hay a continuación:</span><span class="sxs-lookup"><span data-stu-id="ca366-229">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="ca366-230">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="ca366-230">DateDiffWeek</span></span>
* <span data-ttu-id="ca366-231">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="ca366-231">DateFromParts</span></span>

<span data-ttu-id="ca366-232">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-232">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="ca366-233">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-233">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="ca366-234">Traducciones de consultas para más construcciones de matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="ca366-234">Query translations for more byte array constructs</span></span>

<span data-ttu-id="ca366-235">Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.</span><span class="sxs-lookup"><span data-stu-id="ca366-235">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="ca366-236">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="ca366-236">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="ca366-237">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="ca366-237">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="ca366-238">Traducción de consultas para Reverse</span><span class="sxs-lookup"><span data-stu-id="ca366-238">Query translation for Reverse</span></span>

<span data-ttu-id="ca366-239">Las consultas que usan `Reverse` ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="ca366-239">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="ca366-240">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-240">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="ca366-241">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-241">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="ca366-242">Traducción de consultas para operadores bit a bit</span><span class="sxs-lookup"><span data-stu-id="ca366-242">Query translation for bitwise operators</span></span>

<span data-ttu-id="ca366-243">Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ca366-243">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="ca366-244">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-244">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="ca366-245">Traducción de consultas para cadenas en Cosmos</span><span class="sxs-lookup"><span data-stu-id="ca366-245">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="ca366-246">Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="ca366-246">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="ca366-247">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="ca366-247">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
