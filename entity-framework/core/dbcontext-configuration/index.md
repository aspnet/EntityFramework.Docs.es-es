---
title: 'Configuración de un DbContext: EF Core'
description: Estrategias para configurar elementos DbContext con Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 179cb1e4ff7a433c13677ec3f1e457de96004489
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431149"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="b9c7d-103">Configuración de un DbContext</span><span class="sxs-lookup"><span data-stu-id="b9c7d-103">Configuring a DbContext</span></span>

<span data-ttu-id="b9c7d-104">En este artículo se muestran los patrones básicos para configurar un `DbContext` a través de un elemento `DbContextOptions` para conectarse a una base de datos mediante un proveedor de EF Core específico y comportamientos opcionales.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="b9c7d-105">Configuración de DbContext en tiempo de diseño</span><span class="sxs-lookup"><span data-stu-id="b9c7d-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="b9c7d-106">Es necesario que herramientas en tiempo de diseño de EF Core como las [migraciones](xref:core/managing-schemas/migrations/index) puedan detectar y crear una instancia de trabajo de un tipo `DbContext` para recopilar detalles sobre los tipos de entidad de la aplicación y cómo se asignan a un esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="b9c7d-107">Este proceso puede ser automático siempre y cuando la herramienta pueda crear fácilmente el `DbContext` de tal forma que se configure de manera similar a como se configuraría en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="b9c7d-108">Aunque cualquier patrón que proporcione la información de configuración necesaria al `DbContext` puede funcionar en tiempo de ejecución, las herramientas que requieren que se use un `DbContext` en tiempo de diseño solo pueden funcionar con un número de patrones limitado.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="b9c7d-109">Estos se tratan con más detalle en la sección [Creación de contexto en tiempo de diseño](xref:core/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="b9c7d-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="b9c7d-110">Configuring DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="b9c7d-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="b9c7d-111">`DbContext` debe tener una instancia de `DbContextOptions` para realizar cualquier trabajo.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="b9c7d-112">La instancia de `DbContextOptions` contiene información de configuración como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="b9c7d-113">El proveedor de base de datos que se va a usar, normalmente seleccionado invocando un método como `UseSqlServer` o `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="b9c7d-114">Estos métodos de extensión requieren el paquete de proveedor correspondiente, como `Microsoft.EntityFrameworkCore.SqlServer` o `Microsoft.EntityFrameworkCore.Sqlite`.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="b9c7d-115">Los métodos se definen en el espacio de nombres `Microsoft.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="b9c7d-116">Cualquier cadena de conexión o identificador de la instancia de base de datos necesarios, normalmente pasados como argumento al método de selección de proveedor mencionado anteriormente</span><span class="sxs-lookup"><span data-stu-id="b9c7d-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="b9c7d-117">Cualquier selector de comportamiento opcional de nivel de proveedor, normalmente también encadenado dentro de la llamada al método de selección de proveedor</span><span class="sxs-lookup"><span data-stu-id="b9c7d-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="b9c7d-118">Cualquier selector de comportamiento de EF Core general, normalmente encadenado después o antes del método de selector de proveedor</span><span class="sxs-lookup"><span data-stu-id="b9c7d-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="b9c7d-119">En el ejemplo siguiente se configura el elemento `DbContextOptions` para usar el proveedor de SQL Server, una conexión contenida en la variable `connectionString`, un tiempo de espera de comando de nivel de proveedor y un selector de comportamiento de EF Core que hace que todas las consultas ejecutadas en el `DbContext` sean de [no seguimiento](xref:core/querying/tracking#no-tracking-queries) de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> <span data-ttu-id="b9c7d-120">Los métodos de selector de proveedor y otros métodos de selector de comportamiento mencionados anteriormente son métodos de extensión en `DbContextOptions` o clases de opciones específicas del proveedor.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="b9c7d-121">Para tener acceso a estos métodos de extensión, es posible que necesite tener un espacio de nombres (normalmente `Microsoft.EntityFrameworkCore`) en el ámbito, así como incluir dependencias de paquetes adicionales en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="b9c7d-122">El elemento `DbContextOptions` se puede proporcionar al `DbContext` invalidando el método `OnConfiguring` o externamente a través de un argumento del constructor.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="b9c7d-123">Si se usan ambos, `OnConfiguring` se aplicará en último lugar y podrá sobrescribir opciones proporcionadas al argumento del constructor.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="b9c7d-124">Argumento del constructor</span><span class="sxs-lookup"><span data-stu-id="b9c7d-124">Constructor argument</span></span>

<span data-ttu-id="b9c7d-125">El constructor simplemente puede aceptar un elemento `DbContextOptions` como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]
> <span data-ttu-id="b9c7d-126">El constructor base de DbContext también acepta la versión no genérica de `DbContextOptions`, pero su uso no se recomienda para aplicaciones con varios tipos de contexto.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="b9c7d-127">Ahora la aplicación puede pasar el elemento `DbContextOptions` al crear una instancia de un contexto, como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="b9c7d-128">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="b9c7d-128">OnConfiguring</span></span>

<span data-ttu-id="b9c7d-129">También puede inicializar el elemento `DbContextOptions` dentro del propio contexto.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="b9c7d-130">Aunque puede usar esta técnica para la configuración básica, normalmente seguirá siendo necesario que obtenga determinados detalles de configuración del exterior como, por ejemplo, una cadena de conexión de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="b9c7d-131">Esto puede hacerse con una API de configuración o con cualquier otro medio.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="b9c7d-132">Para inicializar `DbContextOptions` dentro del contexto, invalide el método `OnConfiguring` y llame a los métodos en el elemento `DbContextOptionsBuilder` proporcionado:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="b9c7d-133">Una aplicación puede simplemente crear una instancia de este contexto sin pasar nada a su constructor:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="b9c7d-134">Este enfoque no se presta a pruebas, a menos que las pruebas tengan como destino la base de datos completa.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="b9c7d-135">Uso de DbContext con inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="b9c7d-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="b9c7d-136">EF Core admite el uso de `DbContext` con un contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="b9c7d-137">El tipo DbContext se puede agregar al contenedor de servicios mediante el método `AddDbContext<TContext>`.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="b9c7d-138">`AddDbContext<TContext>` hará que el tipo DbContext, `TContext`, y el elemento `DbContextOptions<TContext>` correspondiente estén disponibles para la inserción desde el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="b9c7d-139">Consulte [más lectura](#more-reading) a continuación para obtener información adicional sobre la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="b9c7d-140">Incorporación del elemento `DbContext` a la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-140">Adding the `DbContext` to dependency injection:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="b9c7d-141">Para ello, es necesario agregar un [argumento del constructor](#constructor-argument) al tipo DbContext que acepte `DbContextOptions<TContext>`.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="b9c7d-142">Código de contexto:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-142">Context code:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="b9c7d-143">Código de aplicación (en ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="b9c7d-143">Application code (in ASP.NET Core):</span></span>

```csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="b9c7d-144">Código de aplicación (mediante ServiceProvider directamente, menos común):</span><span class="sxs-lookup"><span data-stu-id="b9c7d-144">Application code (using ServiceProvider directly, less common):</span></span>

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="b9c7d-145">Evitar problemas con el subprocesamiento de DbContext</span><span class="sxs-lookup"><span data-stu-id="b9c7d-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="b9c7d-146">Entity Framework Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="b9c7d-147">Esto incluye la ejecución en paralelo de consultas asincrónicas y cualquier uso simultáneo explícito desde varios subprocesos.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="b9c7d-148">Por tanto, espere siempre llamadas asincrónicas de inmediato mediante el operador `await` o use instancias de `DbContext` independientes para operaciones que se ejecuten en paralelo.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="b9c7d-149">Cuando EF Core detecte un intento de usar una instancia de `DbContext` simultáneamente, verá un elemento `InvalidOperationException` con un mensaje como este:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="b9c7d-150">Se inició una segunda operación en este contexto antes de que se completara una operación anterior.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="b9c7d-151">Esto se debe normalmente a distintos subprocesos que usan la misma instancia de DbContext. Sin embargo, no se garantiza que los miembros de instancia sean seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="b9c7d-152">Cuando el acceso simultáneo no se detecta, puede dar lugar a un comportamiento indefinido, a bloqueos de la aplicación y a daño en los datos.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="b9c7d-153">Hay errores comunes que pueden dar lugar accidentalmente a un acceso simultáneo en la misma instancia de `DbContext`:</span><span class="sxs-lookup"><span data-stu-id="b9c7d-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="b9c7d-154">Olvido de la espera de la finalización de una operación asincrónica antes de iniciar cualquier otra operación en el mismo DbContext</span><span class="sxs-lookup"><span data-stu-id="b9c7d-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="b9c7d-155">Los métodos asincrónicos habilitan EF Core para iniciar operaciones con acceso a la base de datos sin bloqueos.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="b9c7d-156">Pero si un llamador no espera a que finalice uno de estos métodos y sigue realizando otras operaciones en el `DbContext`, el estado del `DbContext` puede estar (y muy probablemente lo estará) dañado.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="b9c7d-157">Espere siempre métodos asincrónicos de EF Core de inmediato.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-157">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="b9c7d-158">Uso compartido implícito de instancias de DbContext entre varios subprocesos a través de la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="b9c7d-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="b9c7d-159">El método de extensión [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) registra tipos de `DbContext` con una [duración de ámbito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="b9c7d-160">Está protegido frente a problemas de acceso simultáneo en la mayoría de las aplicaciones ASP.NET Core, ya que solo hay un subproceso ejecutando cada solicitud de cliente en un momento dado, y cada solicitud obtiene un ámbito de inserción de dependencias independiente (y, por tanto, una instancia de `DbContext` independiente).</span><span class="sxs-lookup"><span data-stu-id="b9c7d-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="b9c7d-161">Para el modelo de hospedaje de Blazor Server, se usa una solicitud lógica para mantener el circuito de usuario de Blazor y, por tanto, solo hay disponible una instancia de DbContext con ámbito por circuito de usuario si se usa el ámbito de inserción predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="b9c7d-162">Cualquier código que ejecute explícitamente varios subprocesos en paralelo debe garantizar que no se tenga nunca acceso a las instancias de `DbContext` simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="b9c7d-163">Con la inserción de dependencias, esto se puede lograr registrando el contexto como con ámbito y creando ámbitos (mediante `IServiceScopeFactory`) para cada subproceso, o bien registrando el `DbContext` como transitorio (mediante la sobrecarga de `AddDbContext`, que toma un parámetro `ServiceLifetime`).</span><span class="sxs-lookup"><span data-stu-id="b9c7d-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="b9c7d-164">Más lectura</span><span class="sxs-lookup"><span data-stu-id="b9c7d-164">More reading</span></span>

- <span data-ttu-id="b9c7d-165">Lea [Inserción de dependencias](/aspnet/core/fundamentals/dependency-injection) para obtener más información sobre el uso de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="b9c7d-166">Lea [Pruebas](xref:core/testing/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b9c7d-166">Read [Testing](xref:core/testing/index) for more information.</span></span>
