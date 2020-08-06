---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: d7f5863e657e243ce733eda5dc8b40c1b92818ce
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526880"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="7a23c-103">Novedades en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="7a23c-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="7a23c-104">EF Core 5.0 está actualmente en desarrollo.</span><span class="sxs-lookup"><span data-stu-id="7a23c-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="7a23c-105">Esta página contendrá información general sobre los cambios interesantes introducidos en cada versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="7a23c-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="7a23c-106">Esta página no duplica el [plan para EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="7a23c-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="7a23c-107">En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.</span><span class="sxs-lookup"><span data-stu-id="7a23c-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="7a23c-108">A medida que se publique el contenido, se agregarán vínculos que redirigirán de esta página a la documentación oficial.</span><span class="sxs-lookup"><span data-stu-id="7a23c-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-7"></a><span data-ttu-id="7a23c-109">Versión preliminar 7</span><span class="sxs-lookup"><span data-stu-id="7a23c-109">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="7a23c-110">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="7a23c-110">DbContextFactory</span></span>

<span data-ttu-id="7a23c-111">EF Core 5.0 presenta `AddDbContextFactory` y `AddPooledDbContextFactory` para registrar una factoría que crea instancias de DbContext en el contenedor de inserción de dependencias (DI) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-111">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="7a23c-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-112">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="7a23c-113">Los servicios de aplicaciones, como los controladores de ASP.NET Core, pueden depender de `IDbContextFactory<TContext>` en el constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="7a23c-113">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="7a23c-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-114">For example:</span></span>

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

<span data-ttu-id="7a23c-115">A continuación, las instancias de DbContext se pueden crear y usar según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="7a23c-115">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="7a23c-116">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-116">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...            
    }
}
```

<span data-ttu-id="7a23c-117">Tenga en cuenta que las instancias de DbContext creadas de este modo _no_ están administradas por el proveedor de servicios de la aplicación y, por lo tanto, la aplicación debe desecharlas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-117">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="7a23c-118">Este desacoplamiento es muy útil para las aplicaciones Blazor, en las que se recomienda usar `IDbContextFactory`, pero también puede ser práctico en otros escenarios.</span><span class="sxs-lookup"><span data-stu-id="7a23c-118">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="7a23c-119">Las instancias DbContext se pueden agrupar mediante una llamada a `AddPooledDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-119">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="7a23c-120">Esta agrupación funciona de la misma manera que para `AddDbContextPool`, y también tiene las mismas limitaciones.</span><span class="sxs-lookup"><span data-stu-id="7a23c-120">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="7a23c-121">En el problema [n.º 2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-121">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="7a23c-122">Restablecimiento del estado de DbContext</span><span class="sxs-lookup"><span data-stu-id="7a23c-122">Reset DbContext state</span></span>

<span data-ttu-id="7a23c-123">EF Core 5.0 presenta `ChangeTracker.Clear()`, que borra la instancia de DbContext de todas las entidades de las que se ha realizado un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="7a23c-123">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="7a23c-124">Normalmente, esto no es necesario si se aplica el procedimiento recomendado de crear una nueva instancia de contexto de corta duración para cada unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="7a23c-124">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="7a23c-125">No obstante, si es necesario restablecer el estado de una instancia de DbContext, el uso del método `Clear()` es más eficaz y robusto que la separación masiva de todas las entidades.</span><span class="sxs-lookup"><span data-stu-id="7a23c-125">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>  

<span data-ttu-id="7a23c-126">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="7a23c-126">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="7a23c-127">Nuevo patrón para los valores predeterminados generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="7a23c-127">New pattern for store-generated defaults</span></span>

<span data-ttu-id="7a23c-128">EF Core permite establecer un valor explícito para una columna que también puede tener una restricción de valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7a23c-128">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="7a23c-129">EF Core usa el valor predeterminado de CLR de tipo de propiedad Type como centinela para este. Si el valor no es el predeterminado de CLR, se inserta; de lo contrario, se usa el valor predeterminado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-129">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="7a23c-130">Esto crea problemas en los tipos en los que el valor predeterminado de CLR no es un buen centinela, en particular, las propiedades `bool`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-130">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="7a23c-131">EF Core 5.0 ahora permite que la variable de respaldo acepte valores NULL para los casos como este.</span><span class="sxs-lookup"><span data-stu-id="7a23c-131">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="7a23c-132">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-132">For example:</span></span>

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

<span data-ttu-id="7a23c-133">Tenga en cuenta que la variable de respaldo admite valores NULL, pero la propiedad expuesta públicamente, no.</span><span class="sxs-lookup"><span data-stu-id="7a23c-133">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="7a23c-134">Esto permite que el valor de centinela sea `null` sin afectar a la superficie pública del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="7a23c-134">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="7a23c-135">En este caso, si `IsValid` nunca se establece, se usará el valor predeterminado de la base de datos, ya que el campo de respaldo sigue siendo NULL.</span><span class="sxs-lookup"><span data-stu-id="7a23c-135">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="7a23c-136">Si se establece `true` o `false`, este valor se guarda explícitamente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-136">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="7a23c-137">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="7a23c-137">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="7a23c-138">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="7a23c-138">Cosmos partition keys</span></span>

<span data-ttu-id="7a23c-139">EF Core permite que la clave de partición de Cosmos se incluya en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="7a23c-139">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="7a23c-140">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-140">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="7a23c-141">A partir de la versión preliminar 7, la clave de partición se incluye en la clave pública del tipo de entidad y se usa para mejorar el rendimiento en algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-141">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="7a23c-142">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="7a23c-142">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="7a23c-143">Configuración de Cosmos</span><span class="sxs-lookup"><span data-stu-id="7a23c-143">Cosmos configuration</span></span>

<span data-ttu-id="7a23c-144">EF Core 5.0 mejora la configuración de Cosmos y de las conexiones Cosmos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-144">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="7a23c-145">Anteriormente, EF Core requería especificar explícitamente el punto de conexión y la clave al conectarse a una base de datos de Cosmos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-145">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="7a23c-146">En su lugar, EF Core 5.0 permite usar una cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="7a23c-146">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="7a23c-147">Además, EF Core 5.0 permite establecer explícitamente la instancia de WebProxy.</span><span class="sxs-lookup"><span data-stu-id="7a23c-147">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="7a23c-148">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-148">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="7a23c-149">Ahora también se pueden configurar muchos otros valores de tiempo de espera, límites, etc.</span><span class="sxs-lookup"><span data-stu-id="7a23c-149">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="7a23c-150">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-150">For example:</span></span>

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

<span data-ttu-id="7a23c-151">Por último, el modo de conexión predeterminado es ahora `ConnectionMode.Gateway`, que suele ofrecer mayor compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="7a23c-151">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="7a23c-152">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="7a23c-152">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="7a23c-153">Singularización en scaffolding de DbContext</span><span class="sxs-lookup"><span data-stu-id="7a23c-153">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="7a23c-154">Anteriormente, al aplicar scaffolding a un elemento DbContext de una base de datos existente, EF Core creaba nombres de tipo de entidad que coincidían con los de las tablas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-154">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="7a23c-155">Por ejemplo, las tablas `People` y `Addresses` producían tipos de entidad denominados `People` y `Addresses`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-155">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="7a23c-156">En versiones anteriores, este comportamiento se podía configurar mediante el registro de un servicio de pluralización.</span><span class="sxs-lookup"><span data-stu-id="7a23c-156">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="7a23c-157">Ahora, en EF Core 5.0, el paquete [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) se usa como servicio de pluralización predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7a23c-157">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="7a23c-158">Esto significa que, ahora, en las tablas `People` y `Addresses`, se aplicarán técnicas de ingeniería inversa para obtener los tipos de entidad denominados `Person` y `Address`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-158">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="7a23c-159">Puntos de retorno</span><span class="sxs-lookup"><span data-stu-id="7a23c-159">Savepoints</span></span>

<span data-ttu-id="7a23c-160">EF Core admite ahora [puntos de retorno](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) para un mayor control sobre las transacciones que ejecutan varias operaciones.</span><span class="sxs-lookup"><span data-stu-id="7a23c-160">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="7a23c-161">Los puntos de retorno se pueden crear, liberar y revertir manualmente.</span><span class="sxs-lookup"><span data-stu-id="7a23c-161">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="7a23c-162">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-162">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint"); 
```

<span data-ttu-id="7a23c-163">Además, EF Core se revierte ahora al último punto de retorno cuando se produce un error al ejecutar `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-163">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="7a23c-164">Esto permite que se vuelva a intentar SaveChanges sin volver a probar toda la transacción.</span><span class="sxs-lookup"><span data-stu-id="7a23c-164">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="7a23c-165">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="7a23c-165">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="7a23c-166">Versión preliminar 6</span><span class="sxs-lookup"><span data-stu-id="7a23c-166">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="7a23c-167">División de consultas para colecciones relacionadas</span><span class="sxs-lookup"><span data-stu-id="7a23c-167">Split queries for related collections</span></span>

<span data-ttu-id="7a23c-168">A partir de EF Core 3.0, EF Core siempre genera una única consulta SQL para cada consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="7a23c-168">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="7a23c-169">Esto garantiza la coherencia de los datos devueltos de acuerdo con las restricciones del modo de transacción en uso.</span><span class="sxs-lookup"><span data-stu-id="7a23c-169">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="7a23c-170">Sin embargo, esta operación puede ser muy lenta si la consulta utiliza `Include` o una proyección para devolver varias colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-170">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="7a23c-171">Ahora, EF Core 5.0 permite dividir en varias consultas SQL una única consulta LINQ con colecciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-171">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="7a23c-172">Esto puede mejorar de forma significativa el rendimiento, pero puede dar lugar a incoherencias en los resultados devueltos si los datos de las dos consultas son diferentes.</span><span class="sxs-lookup"><span data-stu-id="7a23c-172">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="7a23c-173">Las transacciones serializables o de instantáneas se pueden usar para mitigar esta situación y lograr la coherencia con las consultas divididas, pero esto puede conllevar otros costos de rendimiento y diferencias de comportamiento.</span><span class="sxs-lookup"><span data-stu-id="7a23c-173">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="7a23c-174">División de consultas con Include</span><span class="sxs-lookup"><span data-stu-id="7a23c-174">Split queries with Include</span></span>

<span data-ttu-id="7a23c-175">Imaginemos, por ejemplo, que tenemos una consulta que extrae dos niveles de colecciones relacionadas mediante `Include`:</span><span class="sxs-lookup"><span data-stu-id="7a23c-175">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="7a23c-176">De forma predeterminada, EF Core generará el siguiente código SQL al usar el proveedor SQLite:</span><span class="sxs-lookup"><span data-stu-id="7a23c-176">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="7a23c-177">La nueva API `AsSplitQuery` se puede usar para cambiar este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="7a23c-177">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="7a23c-178">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-178">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="7a23c-179">AsSplitQuery está disponible para todos los proveedores de bases de datos relacionales y se puede usar en cualquier parte de la consulta, igual que AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="7a23c-179">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="7a23c-180">EF Core generará ahora las tres consultas SQL siguientes:</span><span class="sxs-lookup"><span data-stu-id="7a23c-180">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="7a23c-181">Se admiten todas las operaciones en la raíz de la consulta.</span><span class="sxs-lookup"><span data-stu-id="7a23c-181">All operations on the query root are supported.</span></span> <span data-ttu-id="7a23c-182">Esto incluye las operaciones OrderBy, Skip, Take y Join, así como FirstOrDefault y operaciones de selección similares de resultado único.</span><span class="sxs-lookup"><span data-stu-id="7a23c-182">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="7a23c-183">Tenga en cuenta que Inclusión filtrada con OrderBy, Skip o Take no se admite en la versión preliminar 6, pero está disponible en las compilaciones diarias y se incluirá en la versión preliminar 7.</span><span class="sxs-lookup"><span data-stu-id="7a23c-183">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="7a23c-184">División de consultas con proyecciones de colecciones</span><span class="sxs-lookup"><span data-stu-id="7a23c-184">Split queries with collection projections</span></span>

<span data-ttu-id="7a23c-185">`AsSplitQuery` también se puede usar cuando se carguen colecciones en proyecciones.</span><span class="sxs-lookup"><span data-stu-id="7a23c-185">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="7a23c-186">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-186">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="7a23c-187">Esta consulta LINQ genera las dos consultas SQL siguientes cuando se usa el proveedor de SQLite:</span><span class="sxs-lookup"><span data-stu-id="7a23c-187">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="7a23c-188">Tenga en cuenta que solo se admite la materialización de la colección.</span><span class="sxs-lookup"><span data-stu-id="7a23c-188">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="7a23c-189">Las composiciones posteriores a `e.Albums` en el caso anterior no generarán consultas divididas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-189">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="7a23c-190">El número de seguimiento de las mejoras de esta área es el [21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="7a23c-190">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="7a23c-191">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="7a23c-191">IndexAttribute</span></span>

<span data-ttu-id="7a23c-192">El nuevo IndexAttribute se puede colocar en un tipo de entidad a fin de especificar un índice para una sola columna.</span><span class="sxs-lookup"><span data-stu-id="7a23c-192">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="7a23c-193">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-193">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="7a23c-194">Para SQL Server, las migraciones generarán el siguiente código SQL:</span><span class="sxs-lookup"><span data-stu-id="7a23c-194">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="7a23c-195">IndexAttribute también se puede usar para especificar un índice que abarque varias columnas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-195">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="7a23c-196">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-196">For example:</span></span>

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

<span data-ttu-id="7a23c-197">Para SQL Server, esto dará como resultado lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a23c-197">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="7a23c-198">El seguimiento de la documentación se realiza mediante la incidencia [n.º 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="7a23c-198">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="7a23c-199">Mejora de las excepciones de traducción de las consultas</span><span class="sxs-lookup"><span data-stu-id="7a23c-199">Improved query translation exceptions</span></span>

<span data-ttu-id="7a23c-200">Seguimos mejorando los mensajes de excepción que se generan cuando se produce un error en la traducción de una consulta.</span><span class="sxs-lookup"><span data-stu-id="7a23c-200">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="7a23c-201">Por ejemplo, esta consulta usa la propiedad no asignada `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="7a23c-201">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="7a23c-202">EF Core producirá la siguiente excepción que indica que se produjo un error de traducción porque no se asignó `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="7a23c-202">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="7a23c-203">Excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="7a23c-203">Unhandled exception.</span></span> <span data-ttu-id="7a23c-204">System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.IsSigned)".</span><span class="sxs-lookup"><span data-stu-id="7a23c-204">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="7a23c-205">Información adicional: Error al traducir el miembro "IsSigned" en el tipo de entidad "Artist".</span><span class="sxs-lookup"><span data-stu-id="7a23c-205">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="7a23c-206">Es posible que el miembro especificado no se haya asignado.</span><span class="sxs-lookup"><span data-stu-id="7a23c-206">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="7a23c-207">Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync().</span><span class="sxs-lookup"><span data-stu-id="7a23c-207">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="7a23c-208">Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7a23c-208">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="7a23c-209">Del mismo modo, ahora se generan mejores mensajes de excepción al intentar traducir comparaciones de cadenas con una semántica dependiente de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="7a23c-209">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="7a23c-210">Por ejemplo, esta consulta intenta usar `StringComparison.CurrentCulture`:</span><span class="sxs-lookup"><span data-stu-id="7a23c-210">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="7a23c-211">Ahora, EF Core producirá la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="7a23c-211">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="7a23c-212">Excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="7a23c-212">Unhandled exception.</span></span> <span data-ttu-id="7a23c-213">System.InvalidOperationException: No se ha podido traducir la expresión LINQ "DbSet<Artist>().Where(a => a.Name.Equals(value: 'The Unicorns', comparisonType: CurrentCulture))".</span><span class="sxs-lookup"><span data-stu-id="7a23c-213">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="7a23c-214">Información adicional: No se admite la traducción del método "string.Equals" que usa el argumento "StringComparison".</span><span class="sxs-lookup"><span data-stu-id="7a23c-214">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="7a23c-215">Vea https://go.microsoft.com/fwlink/?linkid=2129535 para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7a23c-215">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="7a23c-216">Vuelva a escribir la consulta de forma que se pueda traducir o cambie a la evaluación de cliente de manera explícita mediante la inserción de una llamada a AsEnumerable(), AsAsyncEnumerable(), ToList() o ToListAsync().</span><span class="sxs-lookup"><span data-stu-id="7a23c-216">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="7a23c-217">Vea https://go.microsoft.com/fwlink/?linkid=2101038 para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7a23c-217">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="7a23c-218">Especificación del id. de la transacción</span><span class="sxs-lookup"><span data-stu-id="7a23c-218">Specify transaction ID</span></span>

<span data-ttu-id="7a23c-219">El miembro de la comunidad [@Marusyk](https://github.com/Marusyk) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7a23c-219">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="7a23c-220">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-220">Many thanks for the contribution!</span></span>

<span data-ttu-id="7a23c-221">EF Core expone un id. de transacción para la correlación de las transacciones entre llamadas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-221">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="7a23c-222">Este id. lo establece normalmente EF Core cuando se inicia una transacción.</span><span class="sxs-lookup"><span data-stu-id="7a23c-222">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="7a23c-223">Si es la aplicación la que inicia la transacción, esta característica permite que la aplicación establezca de forma explícita el id. de transacción para que se ponga en correlación de forma correcta en todas las ubicaciones donde se use.</span><span class="sxs-lookup"><span data-stu-id="7a23c-223">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="7a23c-224">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-224">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="7a23c-225">Asignación de IPAddress</span><span class="sxs-lookup"><span data-stu-id="7a23c-225">IPAddress mapping</span></span>

<span data-ttu-id="7a23c-226">El miembro de la comunidad [@ralmsdeveloper](https://github.com/ralmsdeveloper) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7a23c-226">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="7a23c-227">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-227">Many thanks for the contribution!</span></span>

<span data-ttu-id="7a23c-228">Ahora, la [clase estándar IPAddress](/dotnet/api/system.net.ipaddress) de .NET se asigna automáticamente a una columna de cadena para las bases de datos que aún no tienen compatibilidad nativa.</span><span class="sxs-lookup"><span data-stu-id="7a23c-228">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="7a23c-229">Por ejemplo, valore la posibilidad de asignar este tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="7a23c-229">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="7a23c-230">En SQL Server, esto hará que las migraciones creen la siguiente tabla:</span><span class="sxs-lookup"><span data-stu-id="7a23c-230">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="7a23c-231">Las entidades se pueden agregar de la manera habitual:</span><span class="sxs-lookup"><span data-stu-id="7a23c-231">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="7a23c-232">Y el código SQL resultante insertará la dirección IPv4 o IPv6 normalizada:</span><span class="sxs-lookup"><span data-stu-id="7a23c-232">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="7a23c-233">Exclusión de OnConfiguring al aplicar scaffolding</span><span class="sxs-lookup"><span data-stu-id="7a23c-233">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="7a23c-234">Cuando se aplica scaffolding a un elemento DbContext desde una base de datos existente, EF Core crea de forma predeterminada una sobrecarga de OnConfiguring con una cadena de conexión para que el contexto se pueda usar de inmediato.</span><span class="sxs-lookup"><span data-stu-id="7a23c-234">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="7a23c-235">Sin embargo, esto no es útil si ya tiene una clase parcial con OnConfiguring o si está configurando el contexto de otra manera.</span><span class="sxs-lookup"><span data-stu-id="7a23c-235">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="7a23c-236">Para solucionar esto, ahora se puede indicar a los comandos de scaffolding que omitan la generación de OnConfiguring.</span><span class="sxs-lookup"><span data-stu-id="7a23c-236">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="7a23c-237">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-237">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="7a23c-238">O bien, en la Consola del administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="7a23c-238">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="7a23c-239">Tenga en cuenta que se recomienda usar [una cadena de conexión con nombre y almacenamiento seguro, como Secretos de usuario](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="7a23c-239">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="7a23c-240">Traducciones de FirstOrDefault en las cadenas</span><span class="sxs-lookup"><span data-stu-id="7a23c-240">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="7a23c-241">El miembro de la comunidad [@dvoreckyaa](https://github.com/dvoreckyaa) ha contribuido en el desarrollo de esta característica.</span><span class="sxs-lookup"><span data-stu-id="7a23c-241">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="7a23c-242">Muchas gracias por la aportación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-242">Many thanks for the contribution!</span></span>

<span data-ttu-id="7a23c-243">Ahora se traducen FirstOrDefault y operadores similares para los caracteres de las cadenas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-243">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="7a23c-244">Por ejemplo, esta consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="7a23c-244">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="7a23c-245">Se traducirá al siguiente código SQL al usar SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7a23c-245">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="7a23c-246">Simplificación de bloques de casos</span><span class="sxs-lookup"><span data-stu-id="7a23c-246">Simplify case blocks</span></span>

<span data-ttu-id="7a23c-247">Ahora, EF Core genera mejores consultas con bloques de casos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-247">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="7a23c-248">Por ejemplo, esta consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="7a23c-248">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="7a23c-249">Estaba traducida en SQL Server oficialmente de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="7a23c-249">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="7a23c-250">Pero ahora se traduce así:</span><span class="sxs-lookup"><span data-stu-id="7a23c-250">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="7a23c-251">Versión preliminar 5</span><span class="sxs-lookup"><span data-stu-id="7a23c-251">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="7a23c-252">Intercalaciones de bases de datos</span><span class="sxs-lookup"><span data-stu-id="7a23c-252">Database collations</span></span>

<span data-ttu-id="7a23c-253">La intercalación predeterminada de una base de datos puede especificarse ahora en el modelo EF.</span><span class="sxs-lookup"><span data-stu-id="7a23c-253">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="7a23c-254">Este pasará a las migraciones generadas para establecer la intercalación cuando se cree la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-254">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="7a23c-255">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-255">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="7a23c-256">A continuación, las migraciones generan lo siguiente para crear la base de datos en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7a23c-256">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="7a23c-257">También se puede especificar la intercalación que se va a usar para columnas de bases de datos concretas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-257">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="7a23c-258">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-258">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="7a23c-259">En el caso de las que no usan migraciones, se aplica a las intercalaciones ingeniería inversa desde la base de datos, al aplicar scaffolding a DbContext.</span><span class="sxs-lookup"><span data-stu-id="7a23c-259">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="7a23c-260">Por último, `EF.Functions.Collate()` permite realizar consultas ad-hoc mediante diferentes intercalaciones.</span><span class="sxs-lookup"><span data-stu-id="7a23c-260">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="7a23c-261">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-261">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="7a23c-262">De este modo se generará la siguiente consulta para SQL Server:</span><span class="sxs-lookup"><span data-stu-id="7a23c-262">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="7a23c-263">Tenga en cuenta que las intercalaciones ad-hoc deben usarse con cuidado, ya que pueden afectar negativamente al rendimiento de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-263">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="7a23c-264">En el problema [n.º 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-264">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="7a23c-265">Transmisión de argumentos a IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="7a23c-265">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="7a23c-266">Los argumentos se transmiten ahora desde la línea de comandos al método de `CreateDbContext` de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="7a23c-266">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="7a23c-267">Por ejemplo, para indicar que se trata de una compilación de desarrollo, se puede pasar un argumento personalizado (por ejemplo, `dev`) en la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="7a23c-267">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="7a23c-268">Este argumento pasará entonces a la factoría, donde se puede usar para controlar cómo se crea e inicializa el contexto.</span><span class="sxs-lookup"><span data-stu-id="7a23c-268">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="7a23c-269">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-269">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="7a23c-270">En el problema [n.º 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) se hace el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-270">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="7a23c-271">Consultas de no seguimiento con resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="7a23c-271">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="7a23c-272">Ahora se pueden configurar consultas de no seguimiento para hacer la resolución de identidad.</span><span class="sxs-lookup"><span data-stu-id="7a23c-272">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="7a23c-273">Por ejemplo, la siguiente consulta creará una instancia de Blog para cada Post, aunque cada blog tenga la misma clave principal.</span><span class="sxs-lookup"><span data-stu-id="7a23c-273">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="7a23c-274">Sin embargo, a costa de que por lo general sea ligeramente más lento y siempre use más memoria, se puede cambiar esta consulta para asegurarse de que solo se crea una instancia de Blog:</span><span class="sxs-lookup"><span data-stu-id="7a23c-274">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="7a23c-275">Tenga en cuenta que esto solo es útil para consultas de no seguimiento, ya que todas las consultas de seguimiento ya muestran este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="7a23c-275">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="7a23c-276">Además, después de la revisión de la API, se cambiará la sintaxis de `PerformIdentityResolution`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-276">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="7a23c-277">Consulte el problema [n.º 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="7a23c-277">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="7a23c-278">En el problema [n.º 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-278">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="7a23c-279">Columnas calculadas almacenadas (persistentes)</span><span class="sxs-lookup"><span data-stu-id="7a23c-279">Stored (persisted) computed columns</span></span>

<span data-ttu-id="7a23c-280">La mayoría de las bases de datos permiten almacenar valores de columnas calculadas después del cálculo.</span><span class="sxs-lookup"><span data-stu-id="7a23c-280">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="7a23c-281">Aunque ocupa espacio en disco, la columna se calcula solo una vez al actualizar, en lugar de cada vez que se recupera su valor.</span><span class="sxs-lookup"><span data-stu-id="7a23c-281">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="7a23c-282">Esto también permite indizar la columna para algunas bases de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-282">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="7a23c-283">EF Core 5.0 permite configurar las columnas calculadas como almacenadas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-283">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="7a23c-284">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-284">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="7a23c-285">Columnas calculadas de SQLite</span><span class="sxs-lookup"><span data-stu-id="7a23c-285">SQLite computed columns</span></span>

<span data-ttu-id="7a23c-286">EF Core admite ahora columnas calculadas en las bases de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="7a23c-286">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="7a23c-287">Versión preliminar 4</span><span class="sxs-lookup"><span data-stu-id="7a23c-287">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="7a23c-288">Configuración de la precisión y la escala de la base de datos en el modelo</span><span class="sxs-lookup"><span data-stu-id="7a23c-288">Configure database precision/scale in model</span></span>

<span data-ttu-id="7a23c-289">Ahora se puede especificar la precisión y la escala de una propiedad mediante el generador de modelos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-289">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="7a23c-290">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-290">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="7a23c-291">La precisión y la escala todavía se pueden establecer mediante el tipo completo de base de datos, como "decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="7a23c-291">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="7a23c-292">En el problema [n.º 527](https://github.com/dotnet/EntityFramework.Docs/issues/527) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-292">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="7a23c-293">Especificación del factor de relleno del índice de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7a23c-293">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="7a23c-294">Ahora se puede especificar el factor de relleno al crear un índice en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7a23c-294">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="7a23c-295">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-295">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="7a23c-296">Versión preliminar 3</span><span class="sxs-lookup"><span data-stu-id="7a23c-296">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="7a23c-297">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="7a23c-297">Filtered Include</span></span>

<span data-ttu-id="7a23c-298">El método Include ahora admite el filtrado de las entidades incluidas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-298">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="7a23c-299">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-299">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="7a23c-300">Esta consulta devolverá los blogs y las publicaciones asociadas, pero solo cuando el título de la publicación contenga la palabra "Cheese".</span><span class="sxs-lookup"><span data-stu-id="7a23c-300">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="7a23c-301">Para reducir el número de las entidades incluidas, se puede usar Skip y Take.</span><span class="sxs-lookup"><span data-stu-id="7a23c-301">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="7a23c-302">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-302">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="7a23c-303">Esta consulta devolverá los blogs que tengan cinco publicaciones como máximo.</span><span class="sxs-lookup"><span data-stu-id="7a23c-303">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="7a23c-304">Vea la [documentación de Include](xref:core/querying/related-data#filtered-include) para obtener información completa.</span><span class="sxs-lookup"><span data-stu-id="7a23c-304">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="7a23c-305">Nueva API de ModelBuilder para las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="7a23c-305">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="7a23c-306">Las propiedades de navegación se configuran principalmente al [definir relaciones](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7a23c-306">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="7a23c-307">No obstante, se puede usar el nuevo método `Navigation` cuando es necesario realizar una configuración adicional en las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-307">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="7a23c-308">Por ejemplo, para establecer un campo de respaldo de la navegación cuando el campo no se encuentre por convención:</span><span class="sxs-lookup"><span data-stu-id="7a23c-308">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="7a23c-309">Tenga en cuenta que la API de `Navigation` no reemplaza la configuración de la relación,</span><span class="sxs-lookup"><span data-stu-id="7a23c-309">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="7a23c-310">sino que permite la configuración adicional de las propiedades de navegación en relaciones ya detectadas o definidas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-310">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="7a23c-311">Consulte la [documentación sobre la configuración de las propiedades de navegación](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="7a23c-311">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="7a23c-312">Nuevos parámetros de la línea de comandos para espacios de nombres y cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="7a23c-312">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="7a23c-313">Ahora, las migraciones y el scaffolding permiten especificar los espacios de nombres en la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-313">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="7a23c-314">Esto puede hacerse para, por ejemplo, aplicar ingeniería inversa a una base de datos y colocar las clases Context y Model en diferentes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="7a23c-314">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="7a23c-315">Para más información, consulte la documentación sobre [migraciones](xref:core/managing-schemas/migrations/index#namespaces) e [ingeniería inversa](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="7a23c-315">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="7a23c-316">Ahora, además, también puede pasarse una cadena de conexión al comando `database-update`:</span><span class="sxs-lookup"><span data-stu-id="7a23c-316">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="7a23c-317">Para más información, consulte la [documentación sobre herramientas](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="7a23c-317">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="7a23c-318">También se han agregado parámetros equivalentes a los comandos de PowerShell que se usan en la consola del administrador de paquetes de VS.</span><span class="sxs-lookup"><span data-stu-id="7a23c-318">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="7a23c-319">EnableDetailedErrors ha vuelto</span><span class="sxs-lookup"><span data-stu-id="7a23c-319">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="7a23c-320">Por motivos de rendimiento, EF no realiza comprobaciones de valores NULL adicionales cuando se leen los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-320">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="7a23c-321">Esto puede dar lugar a excepciones cuya causa principal es difícil de rastrear cuando se encuentra un valor NULL inesperado.</span><span class="sxs-lookup"><span data-stu-id="7a23c-321">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="7a23c-322">El uso de `EnableDetailedErrors` agregará una comprobación de valores NULL adicional a las consultas de modo que, para una pequeña sobrecarga de rendimiento, estos errores sean más fáciles de rastrear para averiguar su causa principal.</span><span class="sxs-lookup"><span data-stu-id="7a23c-322">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="7a23c-323">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-323">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="7a23c-324">En el problema [n.º 955](https://github.com/dotnet/EntityFramework.Docs/issues/955) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-324">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="7a23c-325">Claves de partición de Cosmos</span><span class="sxs-lookup"><span data-stu-id="7a23c-325">Cosmos partition keys</span></span>

<span data-ttu-id="7a23c-326">Ahora, es posible especificar en las consultas la clave de partición que va a usarse para una consulta determinada.</span><span class="sxs-lookup"><span data-stu-id="7a23c-326">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="7a23c-327">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-327">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="7a23c-328">En el problema [n.º 2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-328">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="7a23c-329">Compatibilidad con la función DATALENGTH de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7a23c-329">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="7a23c-330">Se puede acceder con el nuevo método `EF.Functions.DataLength`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-330">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="7a23c-331">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-331">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="7a23c-332">Versión preliminar 2</span><span class="sxs-lookup"><span data-stu-id="7a23c-332">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="7a23c-333">Uso de un atributo de C# para especificar un campo de respaldo de propiedad</span><span class="sxs-lookup"><span data-stu-id="7a23c-333">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="7a23c-334">Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="7a23c-334">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="7a23c-335">Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente.</span><span class="sxs-lookup"><span data-stu-id="7a23c-335">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="7a23c-336">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-336">For example:</span></span>

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

<span data-ttu-id="7a23c-337">En el problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-337">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="7a23c-338">Asignación completa de discriminador</span><span class="sxs-lookup"><span data-stu-id="7a23c-338">Complete discriminator mapping</span></span>

<span data-ttu-id="7a23c-339">EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="7a23c-339">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="7a23c-340">Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador.</span><span class="sxs-lookup"><span data-stu-id="7a23c-340">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="7a23c-341">EF Core 5.0 ahora implementa estas mejoras.</span><span class="sxs-lookup"><span data-stu-id="7a23c-341">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="7a23c-342">Por ejemplo, las versiones anteriores de EF Core siempre generaban este SQL para una consulta que devuelve todos los tipos de una jerarquía:</span><span class="sxs-lookup"><span data-stu-id="7a23c-342">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="7a23c-343">EF Core 5.0 ahora genera lo siguiente cuando se configura una asignación completa de discriminador:</span><span class="sxs-lookup"><span data-stu-id="7a23c-343">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="7a23c-344">Este va a ser el comportamiento predeterminado a partir de la versión preliminar 3.</span><span class="sxs-lookup"><span data-stu-id="7a23c-344">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="7a23c-345">Mejoras de rendimiento de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7a23c-345">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="7a23c-346">Se han realizado dos mejoras de rendimiento para SQLIte:</span><span class="sxs-lookup"><span data-stu-id="7a23c-346">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="7a23c-347">La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-347">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="7a23c-348">La inicialización de SqliteConnection ahora es diferida.</span><span class="sxs-lookup"><span data-stu-id="7a23c-348">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="7a23c-349">Estas mejoras se encuentran en el proveedor Microsoft.Data.Sqlite de ADO.NET y, por lo tanto, también mejoran el rendimiento fuera de EF Core.</span><span class="sxs-lookup"><span data-stu-id="7a23c-349">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="7a23c-350">Versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="7a23c-350">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="7a23c-351">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="7a23c-351">Simple logging</span></span>

<span data-ttu-id="7a23c-352">Esta característica agrega funcionalidad similar a `Database.Log` en EF6.</span><span class="sxs-lookup"><span data-stu-id="7a23c-352">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="7a23c-353">Es decir, proporciona una manera sencilla de obtener registros de EF Core sin necesidad de configurar ningún tipo de plataforma de registro externa.</span><span class="sxs-lookup"><span data-stu-id="7a23c-353">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="7a23c-354">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="7a23c-354">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="7a23c-355">En el problema [n.º 2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7a23c-355">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="7a23c-356">Forma sencilla de generar contenido SQL</span><span class="sxs-lookup"><span data-stu-id="7a23c-356">Simple way to get generated SQL</span></span>

<span data-ttu-id="7a23c-357">EF Core 5.0 presenta el método de extensión `ToQueryString`, que devuelve el SQL que EF Core genera al ejecutar una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="7a23c-357">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="7a23c-358">La documentación preliminar se incluye en el [estado semanal de EF del 9 de enero de 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="7a23c-358">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="7a23c-359">En el problema [n.º 1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7a23c-359">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="7a23c-360">Uso de un atributo de C# para indicar que una entidad no tiene clave</span><span class="sxs-lookup"><span data-stu-id="7a23c-360">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="7a23c-361">Ahora se pueden configurar los tipos de entidad para indicar que no tienen clave mediante el nuevo valor `KeylessAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-361">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="7a23c-362">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-362">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="7a23c-363">En el problema [n.º 2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-363">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="7a23c-364">Posibilidad de cambiar la conexión o la cadena de conexión en una instancia inicializada de DbContext</span><span class="sxs-lookup"><span data-stu-id="7a23c-364">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="7a23c-365">Ahora es más fácil crear una instancia de DbContext sin ninguna conexión o cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="7a23c-365">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="7a23c-366">Además, la conexión o la cadena de conexión ahora también se pueden mutar en la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="7a23c-366">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="7a23c-367">Esta característica permite que la misma instancia de contexto se conecte de forma dinámica a diferentes bases de datos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-367">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="7a23c-368">En el problema [n.º 2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-368">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="7a23c-369">Proxies de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="7a23c-369">Change-tracking proxies</span></span>

<span data-ttu-id="7a23c-370">Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) y [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="7a23c-370">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="7a23c-371">A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios.</span><span class="sxs-lookup"><span data-stu-id="7a23c-371">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="7a23c-372">Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.</span><span class="sxs-lookup"><span data-stu-id="7a23c-372">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="7a23c-373">En el problema [n.º 2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-373">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="7a23c-374">Vistas de depuración mejoradas</span><span class="sxs-lookup"><span data-stu-id="7a23c-374">Enhanced debug views</span></span>

<span data-ttu-id="7a23c-375">Las vistas de depuración son una forma fácil de consultar los aspectos internos de EF Core al depurar problemas.</span><span class="sxs-lookup"><span data-stu-id="7a23c-375">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="7a23c-376">Hace algún tiempo ya se implementó una vista de depuración para el modelo.</span><span class="sxs-lookup"><span data-stu-id="7a23c-376">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="7a23c-377">En el caso de EF Core 5.0, hemos facilitado la lectura de la vista de modelo y hemos agregado una nueva vista de depuración para las entidades de las que se ha realizado un seguimiento en el administrador de estado.</span><span class="sxs-lookup"><span data-stu-id="7a23c-377">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="7a23c-378">La documentación preliminar se incluye en el [estado semanal de EF del 12 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="7a23c-378">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="7a23c-379">En el problema [n.º 2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7a23c-379">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="7a23c-380">Control mejorado de la semántica de valores NULL de base de datos</span><span class="sxs-lookup"><span data-stu-id="7a23c-380">Improved handling of database null semantics</span></span>

<span data-ttu-id="7a23c-381">Normalmente, las bases de datos relacionales tratan NULL como valores desconocidos y, por lo tanto, no son iguales a otros valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7a23c-381">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="7a23c-382">Mientras, C# trata NULL como un valor definido que se compara igual que cualquier otro valor NULL.</span><span class="sxs-lookup"><span data-stu-id="7a23c-382">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="7a23c-383">De forma predeterminada, EF Core traduce las consultas para que usen la semántica de valores NULL de C#.</span><span class="sxs-lookup"><span data-stu-id="7a23c-383">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="7a23c-384">EF Core 5.0 mejora en gran medida la eficacia de dichas traducciones.</span><span class="sxs-lookup"><span data-stu-id="7a23c-384">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="7a23c-385">En el problema [n.º 1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-385">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="7a23c-386">Propiedades del indizador</span><span class="sxs-lookup"><span data-stu-id="7a23c-386">Indexer properties</span></span>

<span data-ttu-id="7a23c-387">EF Core 5.0 admite la asignación de propiedades de indizador de C#.</span><span class="sxs-lookup"><span data-stu-id="7a23c-387">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="7a23c-388">Estas propiedades permiten a las entidades actuar como bolsas de propiedades en las que las columnas se asignan a propiedades con nombre de la bolsa.</span><span class="sxs-lookup"><span data-stu-id="7a23c-388">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="7a23c-389">En el problema [n.º 2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-389">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="7a23c-390">Generación de restricciones CHECK para las asignaciones de enumeración</span><span class="sxs-lookup"><span data-stu-id="7a23c-390">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="7a23c-391">Las migraciones de EF Core 5.0 ahora pueden generar restricciones CHECK para las asignaciones de propiedades de enumeración.</span><span class="sxs-lookup"><span data-stu-id="7a23c-391">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="7a23c-392">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-392">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="7a23c-393">En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-393">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="7a23c-394">IsRelational</span><span class="sxs-lookup"><span data-stu-id="7a23c-394">IsRelational</span></span>

<span data-ttu-id="7a23c-395">Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`.</span><span class="sxs-lookup"><span data-stu-id="7a23c-395">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="7a23c-396">Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="7a23c-396">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="7a23c-397">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-397">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="7a23c-398">En el problema [n.º 2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-398">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="7a23c-399">Simultaneidad optimista de Cosmos con mecanismos ETag</span><span class="sxs-lookup"><span data-stu-id="7a23c-399">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="7a23c-400">El proveedor de bases de datos de Azure Cosmos DB ya es compatible con la simultaneidad optimista mediante mecanismos ETag.</span><span class="sxs-lookup"><span data-stu-id="7a23c-400">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="7a23c-401">Utilice el generador de modelos de OnModelCreating para confirmar un mecanismo ETag:</span><span class="sxs-lookup"><span data-stu-id="7a23c-401">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="7a23c-402">Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](/ef/core/saving/concurrency), por ejemplo, para implementar reintentos.</span><span class="sxs-lookup"><span data-stu-id="7a23c-402">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="7a23c-403">En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-403">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="7a23c-404">Traducciones de consultas para más construcciones DateTime</span><span class="sxs-lookup"><span data-stu-id="7a23c-404">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="7a23c-405">Ahora las consultas que contienen la nueva construcción DateTime se traducen.</span><span class="sxs-lookup"><span data-stu-id="7a23c-405">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="7a23c-406">Además, ahora se asignan las funciones de SQL Server que hay a continuación:</span><span class="sxs-lookup"><span data-stu-id="7a23c-406">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="7a23c-407">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="7a23c-407">DateDiffWeek</span></span>
* <span data-ttu-id="7a23c-408">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="7a23c-408">DateFromParts</span></span>

<span data-ttu-id="7a23c-409">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-409">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="7a23c-410">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-410">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="7a23c-411">Traducciones de consultas para más construcciones de matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="7a23c-411">Query translations for more byte array constructs</span></span>

<span data-ttu-id="7a23c-412">Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.</span><span class="sxs-lookup"><span data-stu-id="7a23c-412">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="7a23c-413">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="7a23c-413">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="7a23c-414">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="7a23c-414">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="7a23c-415">Traducción de consultas para Reverse</span><span class="sxs-lookup"><span data-stu-id="7a23c-415">Query translation for Reverse</span></span>

<span data-ttu-id="7a23c-416">Las consultas que usan `Reverse` ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="7a23c-416">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="7a23c-417">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-417">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="7a23c-418">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-418">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="7a23c-419">Traducción de consultas para operadores bit a bit</span><span class="sxs-lookup"><span data-stu-id="7a23c-419">Query translation for bitwise operators</span></span>

<span data-ttu-id="7a23c-420">Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a23c-420">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="7a23c-421">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-421">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="7a23c-422">Traducción de consultas para cadenas en Cosmos</span><span class="sxs-lookup"><span data-stu-id="7a23c-422">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="7a23c-423">Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="7a23c-423">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="7a23c-424">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="7a23c-424">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
