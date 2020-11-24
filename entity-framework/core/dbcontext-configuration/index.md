---
title: 'Duración, configuración e inicialización de DbContext: EF Core'
description: Patrones para crear y administrar instancias de DbContext con o sin inserción de dependencias
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: f4d51e10555844b5a14000a464f86d3440d5749e
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003294"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a><span data-ttu-id="db5d6-103">Duración, configuración e inicialización de DbContext</span><span class="sxs-lookup"><span data-stu-id="db5d6-103">DbContext Lifetime, Configuration, and Initialization</span></span>

<span data-ttu-id="db5d6-104">En este artículo se muestran los patrones básicos para la inicialización y configuración de una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext>.</span><span class="sxs-lookup"><span data-stu-id="db5d6-104">This article shows basic patterns for initialization and configuration of a <xref:Microsoft.EntityFrameworkCore.DbContext> instance.</span></span>

## <a name="the-dbcontext-lifetime"></a><span data-ttu-id="db5d6-105">La duración de DbContext</span><span class="sxs-lookup"><span data-stu-id="db5d6-105">The DbContext lifetime</span></span>

<span data-ttu-id="db5d6-106">La duración de `DbContext` comienza cuando se crea la instancia y finaliza cuando la instancia se [elimina](/dotnet/standard/garbage-collection/unmanaged).</span><span class="sxs-lookup"><span data-stu-id="db5d6-106">The lifetime of a `DbContext` begins when the instance is created and ends when the instance is [disposed](/dotnet/standard/garbage-collection/unmanaged).</span></span> <span data-ttu-id="db5d6-107">Una instancia de `DbContext` está diseñada para usarse para una _única_ [unidad de trabajo](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="db5d6-107">A `DbContext` instance is designed to be used for a _single_ [unit-of-work](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="db5d6-108">Esto significa que la duración de una instancia de `DbContext` suele ser muy breve.</span><span class="sxs-lookup"><span data-stu-id="db5d6-108">This means that the lifetime of a `DbContext` instance is usually very short.</span></span>

> [!TIP]
> <span data-ttu-id="db5d6-109">Por citar a Martin Fowler, del vínculo anterior, "una unidad de trabajo hace un seguimiento de todas las acciones que realiza durante una transacción comercial que pueden afectar a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-109">To quote Martin Fowler from the link above, "A Unit of Work keeps track of everything you do during a business transaction that can affect the database.</span></span> <span data-ttu-id="db5d6-110">Cuando ha terminado, determina todo lo que se debe hacer para modificar la base de datos como resultado de su trabajo".</span><span class="sxs-lookup"><span data-stu-id="db5d6-110">When you're done, it figures out everything that needs to be done to alter the database as a result of your work."</span></span>

<span data-ttu-id="db5d6-111">Una unidad de trabajo típica al utilizar Entity Framework Core (EF Core) implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="db5d6-111">A typical unit-of-work when using Entity Framework Core (EF Core) involves:</span></span>

- <span data-ttu-id="db5d6-112">Creación de una instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-112">Creation of a `DbContext` instance</span></span>
- <span data-ttu-id="db5d6-113">Seguimiento de las instancias de entidad por el contexto.</span><span class="sxs-lookup"><span data-stu-id="db5d6-113">Tracking of entity instances by the context.</span></span> <span data-ttu-id="db5d6-114">Seguimiento de las entidades mediante</span><span class="sxs-lookup"><span data-stu-id="db5d6-114">Entities become tracked by</span></span>
  - <span data-ttu-id="db5d6-115">[devolución desde una consulta](xref:core/querying/tracking)</span><span class="sxs-lookup"><span data-stu-id="db5d6-115">Being [returned from a query](xref:core/querying/tracking)</span></span>
  - <span data-ttu-id="db5d6-116">[adición o asociación al contexto](xref:core/saving/disconnected-entities)</span><span class="sxs-lookup"><span data-stu-id="db5d6-116">Being [added or attached to the context](xref:core/saving/disconnected-entities)</span></span>
- <span data-ttu-id="db5d6-117">Se realizan cambios en las entidades sometidas a seguimiento según sea necesario para implementar la regla empresarial.</span><span class="sxs-lookup"><span data-stu-id="db5d6-117">Changes are made to the tracked entities as needed to implement the business rule</span></span>
- <span data-ttu-id="db5d6-118">Se llama a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="db5d6-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> is called.</span></span> <span data-ttu-id="db5d6-119">EF Core detecta los cambios realizados y los escribe en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-119">EF Core detects the changes made and writes them to the database.</span></span>
- <span data-ttu-id="db5d6-120">Se elimina la instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-120">The `DbContext` instance is disposed</span></span>

> [!IMPORTANT]
>
> - <span data-ttu-id="db5d6-121">Es muy importante eliminar <xref:Microsoft.EntityFrameworkCore.DbContext> tras su uso.</span><span class="sxs-lookup"><span data-stu-id="db5d6-121">It is very important to dispose the <xref:Microsoft.EntityFrameworkCore.DbContext> after use.</span></span> <span data-ttu-id="db5d6-122">De este modo, se garantiza que se liberen los recursos no administrados y que se anule el registro de todos los eventos u otros enlaces para evitar pérdidas de memoria en caso de que se siga haciendo referencia a la instancia.</span><span class="sxs-lookup"><span data-stu-id="db5d6-122">This ensures both that any unmanaged resources are freed, and that any events or other hooks are unregistered so as to prevent memory leaks in case the instance remains referenced.</span></span>
> - <span data-ttu-id="db5d6-123">[DbContext **no es seguro para subprocesos**](#avoiding-dbcontext-threading-issues).</span><span class="sxs-lookup"><span data-stu-id="db5d6-123">[DbContext is **not thread-safe**](#avoiding-dbcontext-threading-issues).</span></span> <span data-ttu-id="db5d6-124">No comparta contextos entre subprocesos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-124">Do not share contexts between threads.</span></span> <span data-ttu-id="db5d6-125">Asegúrese de [esperar](/dotnet/csharp/language-reference/operators/await) todas las llamadas asincrónicas antes de continuar usando la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="db5d6-125">Make sure to [await](/dotnet/csharp/language-reference/operators/await) all async calls before continuing to use the context instance.</span></span>
> - <span data-ttu-id="db5d6-126">Un objeto <xref:System.InvalidOperationException> generado por código de EF Core puede poner el contexto en un estado irrecuperable.</span><span class="sxs-lookup"><span data-stu-id="db5d6-126">An <xref:System.InvalidOperationException> thrown by EF Core code can put the context into an unrecoverable state.</span></span> <span data-ttu-id="db5d6-127">Estas excepciones indican un error del programa y no están diseñadas para recuperarse.</span><span class="sxs-lookup"><span data-stu-id="db5d6-127">Such exceptions indicate a program error and are not designed to be recovered from.</span></span>

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a><span data-ttu-id="db5d6-128">DbContext en la inserción de dependencias para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db5d6-128">DbContext in dependency injection for ASP.NET Core</span></span>

<span data-ttu-id="db5d6-129">En muchas aplicaciones web, cada solicitud HTTP corresponde a una sola unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="db5d6-129">In many web applications, each HTTP request corresponds to a single unit-of-work.</span></span> <span data-ttu-id="db5d6-130">Esto hace que vincular la vida útil del contexto a la de la solicitud sea un buen valor predeterminado para las aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="db5d6-130">This makes tying the context lifetime to that of the request a good default for web applications.</span></span>

<span data-ttu-id="db5d6-131">Las aplicaciones de ASP.NET Core se [configuran mediante la inserción de dependencias](/aspnet/core/fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="db5d6-131">ASP.NET Core applications are [configured using dependency injection](/aspnet/core/fundamentals/startup).</span></span> <span data-ttu-id="db5d6-132">EF Core se puede agregar a esta configuración mediante <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> en el método [`ConfigurureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) de `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-132">EF Core can be added to this configuration using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in the [`ConfigurureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) method of `Startup.cs`.</span></span> <span data-ttu-id="db5d6-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-133">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
            
            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

<span data-ttu-id="db5d6-134">En este ejemplo se registra una subclase `DbContext` denominada `ApplicationDbContext` como un servicio con ámbito en el proveedor de servicios de aplicación de ASP.NET Core (también conocido como</span><span class="sxs-lookup"><span data-stu-id="db5d6-134">This example registers a `DbContext` subclass called `ApplicationDbContext` as a scoped service in the ASP.NET Core application service provider (a.k.a.</span></span> <span data-ttu-id="db5d6-135">contenedor de inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="db5d6-135">the dependency injection container).</span></span> <span data-ttu-id="db5d6-136">El contexto se configura para utilizar el proveedor de base de datos de SQL Server y leerá la cadena de conexión de la configuración de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db5d6-136">The context is configured to use the SQL Server database provider and will read the connection string from ASP.NET Core configuration.</span></span> <span data-ttu-id="db5d6-137">Por lo general, no importa _dónde_ en `ConfigureServices` se realiza la llamada a `AddDbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-137">It typically does not matter _where_ in `ConfigureServices` the call to `AddDbContext` is made.</span></span>

<span data-ttu-id="db5d6-138">La clase `ApplicationDbContext` debe exponer un constructor público con un parámetro `DbContextOptions<ApplicationDbContext>`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-138">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="db5d6-139">Así es cómo se pasa la configuración de contexto de `AddDbContext` a `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-139">This is how context configuration from `AddDbContext` is passed to the `DbContext`.</span></span> <span data-ttu-id="db5d6-140">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-140">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="db5d6-141">`ApplicationDbContext` se puede usar en controladores de ASP.NET Core u otros servicios a través de la inserción de constructores.</span><span class="sxs-lookup"><span data-stu-id="db5d6-141">`ApplicationDbContext` can then be used in ASP.NET Core controllers or other services through constructor injection.</span></span> <span data-ttu-id="db5d6-142">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-142">For example:</span></span>

<!--
    public class MyController
    {
        private readonly ApplicationDbContext _context;

        public MyController(ApplicationDbContext context)
        {
            _context = context;
        }
    }
-->
[!code-csharp[MyController](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Controllers/MyController.cs?name=MyController)]

<span data-ttu-id="db5d6-143">El resultado final es una instancia de `ApplicationDbContext` creada para cada solicitud y que se pasa al controlador para realizar una unidad de trabajo antes de que se elimine cuando finalice la solicitud.</span><span class="sxs-lookup"><span data-stu-id="db5d6-143">The final result is an `ApplicationDbContext` instance created for each request and passed to the controller to perform a unit-of-work before being disposed when the request ends.</span></span>

<span data-ttu-id="db5d6-144">Lea más adelante en este artículo para obtener más información sobre las opciones de configuración.</span><span class="sxs-lookup"><span data-stu-id="db5d6-144">Read further in this article to learn more about configuration options.</span></span> <span data-ttu-id="db5d6-145">Además, consulte [Inicio de la aplicación en ASP.NET Core](/aspnet/core/fundamentals/startup) e [Inserción de dependencias en ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) para obtener más información sobre la configuración y la inserción de dependencias en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db5d6-145">In addition, see [App startup in ASP.NET Core](/aspnet/core/fundamentals/startup) and [Dependency injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) for more information on configuration and dependency injection in ASP.NET Core.</span></span>

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a><span data-ttu-id="db5d6-146">Inicialización de DbContext simple con "New"</span><span class="sxs-lookup"><span data-stu-id="db5d6-146">Simple DbContext initialization with 'new'</span></span>

<span data-ttu-id="db5d6-147">Las instancias de `DbContext` se pueden construir de la manera normal de .NET, por ejemplo, con `new` en C#.</span><span class="sxs-lookup"><span data-stu-id="db5d6-147">`DbContext` instances can be constructed in the normal .NET way, for example with `new` in C#.</span></span> <span data-ttu-id="db5d6-148">La configuración se puede realizar invalidando el método `OnConfiguring` o pasando opciones al constructor.</span><span class="sxs-lookup"><span data-stu-id="db5d6-148">Configuration can be performed by overriding the `OnConfiguring` method, or by passing options to the constructor.</span></span> <span data-ttu-id="db5d6-149">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-149">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="db5d6-150">Este patrón también facilita el paso de la configuración, como la cadena de conexión, a través del constructor `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-150">This pattern also makes it easy to pass configuration like the connection string via the `DbContext` constructor.</span></span> <span data-ttu-id="db5d6-151">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-151">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        private readonly string _connectionString;

        public ApplicationDbContext(string connectionString)
        {
            _connectionString = connectionString;
        }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(_connectionString);
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNewAndArgs/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="db5d6-152">Como alternativa, `DbContextOptionsBuilder` se puede usar para crear un objeto `DbContextOptions` que se pasa a continuación al constructor `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-152">Alternately, `DbContextOptionsBuilder` can be used to create a `DbContextOptions` object that is then passed to the `DbContext` constructor.</span></span> <span data-ttu-id="db5d6-153">Esto permite que el elemento `DbContext` configurado para la inserción de dependencias también se construya explícitamente.</span><span class="sxs-lookup"><span data-stu-id="db5d6-153">This allows a `DbContext` configured for dependency injection to also be constructed explicitly.</span></span> <span data-ttu-id="db5d6-154">Por ejemplo, al usar el elemento `ApplicationDbContext` definido para aplicaciones web de ASP.NET Core que vimos anteriormente:</span><span class="sxs-lookup"><span data-stu-id="db5d6-154">For example, when using `ApplicationDbContext` defined for ASP.NET Core web apps above:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="db5d6-155">`DbContextOptions` se puede crear y se puede llamar al constructor explícitamente:</span><span class="sxs-lookup"><span data-stu-id="db5d6-155">The `DbContextOptions` can be created and the constructor can be called explicitly:</span></span>

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a><span data-ttu-id="db5d6-156">Uso de un generador de DbContext (por ejemplo, para Blazor)</span><span class="sxs-lookup"><span data-stu-id="db5d6-156">Using a DbContext factory (e.g. for Blazor)</span></span>

<span data-ttu-id="db5d6-157">Algunos tipos de aplicaciones (por ejemplo, [ASP.NET Core Blazor](/aspnet/core/blazor/)) utilizan la inserción de dependencias, pero no crean un ámbito de servicio que se alinee con la duración de `DbContext` deseada.</span><span class="sxs-lookup"><span data-stu-id="db5d6-157">Some application types (e.g. [ASP.NET Core Blazor](/aspnet/core/blazor/)) use dependency injection but do not create a service scope that aligns with the desired `DbContext` lifetime.</span></span> <span data-ttu-id="db5d6-158">Incluso en los casos en los que exista una alineación, es posible que la aplicación tenga que realizar varias unidades de trabajo en este ámbito.</span><span class="sxs-lookup"><span data-stu-id="db5d6-158">Even where such an alignment does exist, the application may need to perform multiple units-of-work within this scope.</span></span> <span data-ttu-id="db5d6-159">Por ejemplo, varias unidades de trabajo en una única solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="db5d6-159">For example, multiple units-of-work within a single HTTP request.</span></span>

<span data-ttu-id="db5d6-160">En estos casos, se puede usar <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> para registrar un generador para la creación de instancias de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-160">In these cases, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> can be used to register a factory for creation of `DbContext` instances.</span></span> <span data-ttu-id="db5d6-161">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-161">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

<span data-ttu-id="db5d6-162">La clase `ApplicationDbContext` debe exponer un constructor público con un parámetro `DbContextOptions<ApplicationDbContext>`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-162">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="db5d6-163">Este es el mismo patrón que se usa en la sección de ASP.NET Core tradicional anterior.</span><span class="sxs-lookup"><span data-stu-id="db5d6-163">This is the same pattern as used in the traditional ASP.NET Core section above.</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="db5d6-164">El generador de `DbContextFactory` se puede utilizar en otros servicios a través de la inserción de constructores.</span><span class="sxs-lookup"><span data-stu-id="db5d6-164">The `DbContextFactory` factory can then be used in other services through constructor injection.</span></span> <span data-ttu-id="db5d6-165">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-165">For example:</span></span>

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

<span data-ttu-id="db5d6-166">A continuación, el generador insertado se puede usar para construir instancias de DbContext en el código del servicio.</span><span class="sxs-lookup"><span data-stu-id="db5d6-166">The injected factory can then be used to construct DbContext instances in the service code.</span></span> <span data-ttu-id="db5d6-167">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-167">For example:</span></span>

<!--
        public void DoSomething()
        {
            using (var context = _contextFactory.CreateDbContext())
            {
                // ...
            }
        }
-->
[!code-csharp[DoSomething](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=DoSomething)]

<span data-ttu-id="db5d6-168">Tenga en cuenta que las instancias de `DbContext` creadas de este modo **no** están administradas por el proveedor de servicios de la aplicación y, por lo tanto, la aplicación debe eliminarlas.</span><span class="sxs-lookup"><span data-stu-id="db5d6-168">Notice that the `DbContext` instances created in this way are **not** managed by the application's service provider and therefore must be disposed by the application.</span></span>

<span data-ttu-id="db5d6-169">Consulte [Blazor Server de ASP.NET Core con Entity Framework Core (EF Core)](/aspnet/core/blazor/blazor-server-ef-core) para obtener más información sobre el uso de EF Core con Blazor.</span><span class="sxs-lookup"><span data-stu-id="db5d6-169">See [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) for more information on using EF Core with Blazor.</span></span>

## <a name="dbcontextoptions"></a><span data-ttu-id="db5d6-170">DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="db5d6-170">DbContextOptions</span></span>

<span data-ttu-id="db5d6-171">El punto inicial de toda la configuración de `DbContext` es <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db5d6-171">The starting point for all `DbContext` configuration is <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="db5d6-172">Hay tres maneras de obtener este generador:</span><span class="sxs-lookup"><span data-stu-id="db5d6-172">There are three ways to get this builder:</span></span>

- <span data-ttu-id="db5d6-173">En `AddDbContext` y métodos relacionados</span><span class="sxs-lookup"><span data-stu-id="db5d6-173">In `AddDbContext` and related methods</span></span>
- <span data-ttu-id="db5d6-174">En `OnConfiguring`</span><span class="sxs-lookup"><span data-stu-id="db5d6-174">In `OnConfiguring`</span></span>
- <span data-ttu-id="db5d6-175">Construido explícitamente con `new`</span><span class="sxs-lookup"><span data-stu-id="db5d6-175">Constructed explicitly with `new`</span></span>

<span data-ttu-id="db5d6-176">En las secciones anteriores se muestran ejemplos de cada una de ellas.</span><span class="sxs-lookup"><span data-stu-id="db5d6-176">Examples of each of these are shown in the preceding sections.</span></span> <span data-ttu-id="db5d6-177">Se puede aplicar la misma configuración independientemente de la procedencia del constructor.</span><span class="sxs-lookup"><span data-stu-id="db5d6-177">The same configuration can be applied regardless of where the builder comes from.</span></span> <span data-ttu-id="db5d6-178">Además, siempre se llama a `OnConfiguring` independientemente de cómo se construya el contexto.</span><span class="sxs-lookup"><span data-stu-id="db5d6-178">In addition, `OnConfiguring` is always called regardless of how the context is constructed.</span></span> <span data-ttu-id="db5d6-179">Esto significa que `OnConfiguring` se puede usar para realizar una configuración adicional incluso cuando se utiliza `AddDbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-179">This means `OnConfiguring` can be used to perform additional configuration even when `AddDbContext` is being used.</span></span>

### <a name="configuring-the-database-provider"></a><span data-ttu-id="db5d6-180">Configuración del proveedor de base de datos</span><span class="sxs-lookup"><span data-stu-id="db5d6-180">Configuring the database provider</span></span>

<span data-ttu-id="db5d6-181">Cada instancia de `DbContext` debe estar configurada para usar un único proveedor de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-181">Each `DbContext` instance must be configured to use one and only one database provider.</span></span> <span data-ttu-id="db5d6-182">(Se pueden usar diferentes instancias de un subtipo `DbContext` con diferentes proveedores de bases de datos, pero una sola instancia solo debe usar uno). Un proveedor de base de datos se configura mediante una llamada de `Use*`" específica.</span><span class="sxs-lookup"><span data-stu-id="db5d6-182">(Different instances of a `DbContext` subtype can be used with different database providers, but a single instance must only use one.) A database provider is configured using a specific `Use*`" call.</span></span> <span data-ttu-id="db5d6-183">Por ejemplo, para usar el proveedor de base de datos de SQL Server:</span><span class="sxs-lookup"><span data-stu-id="db5d6-183">For example, to use the SQL Server database provider:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="db5d6-184">Estos métodos de `Use*`" son métodos de extensión implementados por el proveedor de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-184">These `Use*`" methods are extension methods implemented by the database provider.</span></span> <span data-ttu-id="db5d6-185">Esto significa que el paquete NuGet del proveedor de bases de datos debe estar instalado para poder usar el método de extensión.</span><span class="sxs-lookup"><span data-stu-id="db5d6-185">This means that the database provider NuGet package must be installed before the extension method can be used.</span></span>

> [!TIP]
> <span data-ttu-id="db5d6-186">Los proveedores de bases de datos de EF Core hacen un uso extensivo de [métodos de extensión](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span><span class="sxs-lookup"><span data-stu-id="db5d6-186">EF Core database providers make extensive use of [extension methods](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span></span> <span data-ttu-id="db5d6-187">Si el compilador indica que no se puede encontrar un método, asegúrese de que el paquete NuGet del proveedor está instalado y de que tiene `using Microsoft.EntityFrameworkCore;` en el código.</span><span class="sxs-lookup"><span data-stu-id="db5d6-187">If the compiler indicates that a method cannot be found, then make sure that the provider's NuGet package is installed and that you have `using Microsoft.EntityFrameworkCore;` in your code.</span></span>

<span data-ttu-id="db5d6-188">En la tabla siguiente se incluyen ejemplos de proveedores de bases de datos comunes.</span><span class="sxs-lookup"><span data-stu-id="db5d6-188">The following table contains examples for common database providers.</span></span>

| <span data-ttu-id="db5d6-189">Sistema de base de datos</span><span class="sxs-lookup"><span data-stu-id="db5d6-189">Database system</span></span>              | <span data-ttu-id="db5d6-190">Ejemplo de configuración</span><span class="sxs-lookup"><span data-stu-id="db5d6-190">Example configuration</span></span>                         | <span data-ttu-id="db5d6-191">Detección de</span><span class="sxs-lookup"><span data-stu-id="db5d6-191">NuGet package</span></span>
|:-----------------------------|-----------------------------------------------|--------------
| <span data-ttu-id="db5d6-192">SQL Server o Azure SQL</span><span class="sxs-lookup"><span data-stu-id="db5d6-192">SQL Server or Azure SQL</span></span>      | `.UseSqlServer(connectionString)`             | [<span data-ttu-id="db5d6-193">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="db5d6-193">Microsoft.EntityFrameworkCore.SqlServer</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| <span data-ttu-id="db5d6-194">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="db5d6-194">Azure Cosmos DB</span></span>              | `.UseCosmos(connectionString, databaseName)`  | [<span data-ttu-id="db5d6-195">Microsoft.EntityFrameworkCore.Cosmos</span><span class="sxs-lookup"><span data-stu-id="db5d6-195">Microsoft.EntityFrameworkCore.Cosmos</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| <span data-ttu-id="db5d6-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="db5d6-196">SQLite</span></span>                       | `.UseSqlite(connectionString)`                | [<span data-ttu-id="db5d6-197">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="db5d6-197">Microsoft.EntityFrameworkCore.Sqlite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| <span data-ttu-id="db5d6-198">Base de datos en memoria de EF Core</span><span class="sxs-lookup"><span data-stu-id="db5d6-198">EF Core in-memory database</span></span>   | `.UseInMemoryDatabase(databaseName)`          | [<span data-ttu-id="db5d6-199">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="db5d6-199">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| <span data-ttu-id="db5d6-200">PostgreSQL\*</span><span class="sxs-lookup"><span data-stu-id="db5d6-200">PostgreSQL\*</span></span>                  | `.UseNpgsql(connectionString)`                | [<span data-ttu-id="db5d6-201">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="db5d6-201">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| <span data-ttu-id="db5d6-202">MySQL/MariaDB\*</span><span class="sxs-lookup"><span data-stu-id="db5d6-202">MySQL/MariaDB\*</span></span>               | `.UseMySql((connectionString)`                | [<span data-ttu-id="db5d6-203">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="db5d6-203">Pomelo.EntityFrameworkCore.MySql</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| <span data-ttu-id="db5d6-204">Oracle\*</span><span class="sxs-lookup"><span data-stu-id="db5d6-204">Oracle\*</span></span>                      | `.UseOracle(connectionString)`                | [<span data-ttu-id="db5d6-205">Oracle.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="db5d6-205">Oracle.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

<span data-ttu-id="db5d6-206">\*Microsoft no entrega estos proveedores de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-206">\*These database providers are not shipped by Microsoft.</span></span> <span data-ttu-id="db5d6-207">Vea [Proveedores de bases de datos](xref:core/providers/index) para obtener más información acerca de los proveedores de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-207">See [Database Providers](xref:core/providers/index) for more information about database providers.</span></span>

> [!WARNING]
> <span data-ttu-id="db5d6-208">La base de datos en memoria de EF Core no está diseñada para uso en producción.</span><span class="sxs-lookup"><span data-stu-id="db5d6-208">The EF Core in-memory database is not designed for production use.</span></span> <span data-ttu-id="db5d6-209">Además, es posible que no sea la mejor opción incluso para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="db5d6-209">In addition, it may not be the best choice even for testing.</span></span> <span data-ttu-id="db5d6-210">Consulte [Pruebas de código que usa EF Core](xref:core/testing/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="db5d6-210">See [Testing Code That Uses EF Core](xref:core/testing/index) for more information.</span></span>

<span data-ttu-id="db5d6-211">Consulte [Cadenas de conexión](xref:core/miscellaneous/connection-strings) para obtener más información sobre el uso de cadenas de conexión con EF Core.</span><span class="sxs-lookup"><span data-stu-id="db5d6-211">See [Connection Strings](xref:core/miscellaneous/connection-strings) for more information on using connection strings with EF Core.</span></span>

<span data-ttu-id="db5d6-212">La configuración opcional específica del proveedor de bases de datos se realiza en un generador adicional específico del proveedor.</span><span class="sxs-lookup"><span data-stu-id="db5d6-212">Optional configuration specific to the database provider is performed in an additional provider-specific builder.</span></span> <span data-ttu-id="db5d6-213">Por ejemplo, el uso de <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> para configurar reintentos para la resistencia de la conexión al conectarse a Azure SQL:</span><span class="sxs-lookup"><span data-stu-id="db5d6-213">For example, using <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> to configure retries for connection resiliency when connecting to Azure SQL:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .UseSqlServer(
                    @"Server=(localdb)\mssqllocaldb;Database=Test",
                    providerOptions =>
                        {
                            providerOptions.EnableRetryOnFailure();
                        });
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalProviderConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

> [!TIP]
> <span data-ttu-id="db5d6-214">Se usa el mismo proveedor de bases de datos para SQL Server y Azure SQL.</span><span class="sxs-lookup"><span data-stu-id="db5d6-214">The same database provider is used for SQL Server and Azure SQL.</span></span> <span data-ttu-id="db5d6-215">Sin embargo, se recomienda usar la [resistencia de la conexión](xref:core/miscellaneous/connection-resiliency) para la conexión a SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="db5d6-215">However, it is recommended that [connection resiliency](xref:core/miscellaneous/connection-resiliency) be used when connecting to SQL Azure.</span></span>

<span data-ttu-id="db5d6-216">Vea [Proveedores de bases de datos](xref:core/providers/index) para obtener más información sobre la configuración específica del proveedor.</span><span class="sxs-lookup"><span data-stu-id="db5d6-216">See [Database Providers](xref:core/providers/index) for more information on provider-specific configuration.</span></span>

### <a name="other-dbcontext-configuration"></a><span data-ttu-id="db5d6-217">Otra configuración de DbContext</span><span class="sxs-lookup"><span data-stu-id="db5d6-217">Other DbContext configuration</span></span>

<span data-ttu-id="db5d6-218">Otra configuración de `DbContext` se puede encadenar antes o después (no hay diferencia) de la llamada de `Use*`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-218">Other `DbContext` configuration can be chained either before or after (it makes no difference which) the `Use*` call.</span></span> <span data-ttu-id="db5d6-219">Por ejemplo, para activar el registro de datos confidenciales:</span><span class="sxs-lookup"><span data-stu-id="db5d6-219">For example, to turn on sensitive-data logging:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .EnableSensitiveDataLogging()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="db5d6-220">En la tabla siguiente se incluyen ejemplos de métodos comunes a los que se llama en `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-220">The following table contains examples of common methods called on `DbContextOptionsBuilder`.</span></span>

| <span data-ttu-id="db5d6-221">Método DbContextOptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="db5d6-221">DbContextOptionsBuilder method</span></span>                                                             | <span data-ttu-id="db5d6-222">Qué hace</span><span class="sxs-lookup"><span data-stu-id="db5d6-222">What it does</span></span>                                                | <span data-ttu-id="db5d6-223">Más información</span><span class="sxs-lookup"><span data-stu-id="db5d6-223">Learn more</span></span>
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | <span data-ttu-id="db5d6-224">Establece el comportamiento de seguimiento predeterminado para las consultas.</span><span class="sxs-lookup"><span data-stu-id="db5d6-224">Sets the default tracking behavior for queries</span></span>              | [<span data-ttu-id="db5d6-225">Comportamiento del seguimiento de las consultas</span><span class="sxs-lookup"><span data-stu-id="db5d6-225">Query Tracking Behavior</span></span>](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | <span data-ttu-id="db5d6-226">Una manera sencilla de obtener registros de EF Core (EF Core 5.0 y versiones posteriores)</span><span class="sxs-lookup"><span data-stu-id="db5d6-226">A simple way to get EF Core logs (EF Core 5.0 and later)</span></span>    | [<span data-ttu-id="db5d6-227">Registro, eventos y diagnósticos</span><span class="sxs-lookup"><span data-stu-id="db5d6-227">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | <span data-ttu-id="db5d6-228">Registra un generador de `Micrsofot.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-228">Registers an `Micrsofot.Extensions.Logging` factory</span></span>         | [<span data-ttu-id="db5d6-229">Registro, eventos y diagnósticos</span><span class="sxs-lookup"><span data-stu-id="db5d6-229">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | <span data-ttu-id="db5d6-230">Incluye datos de aplicación en excepciones y registro.</span><span class="sxs-lookup"><span data-stu-id="db5d6-230">Includes application data in exceptions and logging</span></span>         | [<span data-ttu-id="db5d6-231">Registro, eventos y diagnósticos</span><span class="sxs-lookup"><span data-stu-id="db5d6-231">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | <span data-ttu-id="db5d6-232">Errores de consulta más detallados (a costa del rendimiento).</span><span class="sxs-lookup"><span data-stu-id="db5d6-232">More detailed query errors (at the expense of performance)</span></span>  | [<span data-ttu-id="db5d6-233">Registro, eventos y diagnósticos</span><span class="sxs-lookup"><span data-stu-id="db5d6-233">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | <span data-ttu-id="db5d6-234">Omite o inicia advertencias y otros eventos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-234">Ignore or throw for warnings and other events</span></span>               | [<span data-ttu-id="db5d6-235">Registro, eventos y diagnósticos</span><span class="sxs-lookup"><span data-stu-id="db5d6-235">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | <span data-ttu-id="db5d6-236">Registra los interceptores de EF Core.</span><span class="sxs-lookup"><span data-stu-id="db5d6-236">Registers EF Core interceptors</span></span>                              | [<span data-ttu-id="db5d6-237">Registro, eventos y diagnósticos</span><span class="sxs-lookup"><span data-stu-id="db5d6-237">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | <span data-ttu-id="db5d6-238">Usa servidores proxy dinámicos para la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="db5d6-238">Use dynamic proxies for lazy-loading</span></span>                        | [<span data-ttu-id="db5d6-239">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="db5d6-239">Lazy Loading</span></span>](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | <span data-ttu-id="db5d6-240">Usa servidores proxy dinámicos para el seguimiento de cambios.</span><span class="sxs-lookup"><span data-stu-id="db5d6-240">Use dynamic proxies for change-tracking</span></span>                     | <span data-ttu-id="db5d6-241">Próximamente...</span><span class="sxs-lookup"><span data-stu-id="db5d6-241">Coming soon...</span></span>

> [!NOTE]
> <span data-ttu-id="db5d6-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> y <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> son métodos de extensión de los paquetes NuGet [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/).</span><span class="sxs-lookup"><span data-stu-id="db5d6-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> and <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> are extension methods from the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package.</span></span> <span data-ttu-id="db5d6-243">Este tipo de llamada de ".UseSomething()" es la forma recomendada de configurar o usar las extensiones de EF Core incluidas en otros paquetes.</span><span class="sxs-lookup"><span data-stu-id="db5d6-243">This kind of ".UseSomething()" call is the recommended way to configure and/or use EF Core extensions contained in other packages.</span></span>

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a><span data-ttu-id="db5d6-244">`DbContextOptions` frente a `DbContextOptions<TContext>`</span><span class="sxs-lookup"><span data-stu-id="db5d6-244">`DbContextOptions` verses `DbContextOptions<TContext>`</span></span>

<span data-ttu-id="db5d6-245">La mayoría de las subclases `DbContext` que aceptan `DbContextOptions` deben usar la variación `DbContextOptions<TContext>` [genérica](/dotnet/csharp/programming-guide/generics/).</span><span class="sxs-lookup"><span data-stu-id="db5d6-245">Most `DbContext` subclasses that accept a `DbContextOptions` should use the [generic](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` variation.</span></span> <span data-ttu-id="db5d6-246">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-246">For example:</span></span>

<!--
    public sealed class SealedApplicationDbContext : DbContext
    {
        public SealedApplicationDbContext(DbContextOptions<SealedApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[SealedApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=SealedApplicationDbContext)]

<span data-ttu-id="db5d6-247">Esto garantiza que las opciones correctas para el subtipo `DbContext` específico se resuelvan a partir de la inserción de dependencias, incluso cuando se registran varios subtipos `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-247">This ensures that the correct options for the specific `DbContext` subtype are resolved from dependency injection, even when multiple `DbContext` subtypes are registered.</span></span>

> [!TIP]
> <span data-ttu-id="db5d6-248">No es necesario que el DbContext esté sellado, pero el sellado es el procedimiento recomendado para las clases que no están diseñadas para heredarse.</span><span class="sxs-lookup"><span data-stu-id="db5d6-248">Your DbContext does not need to be sealed, but sealing is best practice to do so for classes not designed to be inherited from.</span></span>

<span data-ttu-id="db5d6-249">Sin embargo, si el subtipo `DbContext` va a heredarse, debe exponer un constructor protegido que tome un elemento `DbContextOptions` no genérico.</span><span class="sxs-lookup"><span data-stu-id="db5d6-249">However, if the `DbContext` subtype is itself intended to be inherited from, then it should expose a protected constructor taking a non-generic `DbContextOptions`.</span></span> <span data-ttu-id="db5d6-250">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-250">For example:</span></span>

<!--
    public abstract class ApplicationDbContextBase : DbContext
    {
        protected ApplicationDbContextBase(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContextBase](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContextBase)]

<span data-ttu-id="db5d6-251">Esto permite que varias subclases concretas llamen a este constructor base mediante sus diferentes instancias de `DbContextOptions<TContext>` genéricas.</span><span class="sxs-lookup"><span data-stu-id="db5d6-251">This allows multiple concrete subclasses to call this base constructor using their different generic `DbContextOptions<TContext>` instances.</span></span> <span data-ttu-id="db5d6-252">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-252">For example:</span></span>

<!--
    public sealed class ApplicationDbContext1 : ApplicationDbContextBase
    {
        public ApplicationDbContext1(DbContextOptions<ApplicationDbContext1> contextOptions)
            : base(contextOptions)
        {
        }
    }

    public sealed class ApplicationDbContext2 : ApplicationDbContextBase
    {
        public ApplicationDbContext2(DbContextOptions<ApplicationDbContext2> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext12](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext12)]

<span data-ttu-id="db5d6-253">Observe que este es exactamente el mismo patrón que al heredar de `DbContext` directamente.</span><span class="sxs-lookup"><span data-stu-id="db5d6-253">Notice that this is exactly the same pattern as when inheriting from `DbContext` directly.</span></span> <span data-ttu-id="db5d6-254">Es decir, el propio constructor `DbContext` acepta un elemento `DbContextOptions` no genérico por esta razón.</span><span class="sxs-lookup"><span data-stu-id="db5d6-254">That is, the `DbContext` constructor itself accepts a non-generic `DbContextOptions` for this reason.</span></span>

<span data-ttu-id="db5d6-255">Una subclase `DbContext` de la que se va a crear una instancia y se puede heredar debe exponer ambas formas de constructor.</span><span class="sxs-lookup"><span data-stu-id="db5d6-255">A `DbContext` subclass intended to be both instantiated and inherited from should expose both forms of constructor.</span></span> <span data-ttu-id="db5d6-256">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db5d6-256">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }

        protected ApplicationDbContext(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext)]

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="db5d6-257">Configuración de DbContext en tiempo de diseño</span><span class="sxs-lookup"><span data-stu-id="db5d6-257">Design-time DbContext configuration</span></span>

<span data-ttu-id="db5d6-258">Es necesario que herramientas en tiempo de diseño de EF Core, como aquellas para [migraciones de EF Core](xref:core/managing-schemas/migrations/index), puedan detectar y crear una instancia de trabajo de un tipo `DbContext` para recopilar detalles sobre los tipos de entidad de la aplicación y cómo se asignan a un esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-258">EF Core design-time tools such as those for [EF Core migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="db5d6-259">Este proceso puede ser automático siempre y cuando la herramienta pueda crear fácilmente el `DbContext` de tal forma que se configure de manera similar a como se configuraría en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="db5d6-259">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="db5d6-260">Aunque cualquier patrón que proporcione la información de configuración necesaria al `DbContext` puede funcionar en tiempo de ejecución, las herramientas que requieren que se use un `DbContext` en tiempo de diseño solo pueden funcionar con un número de patrones limitado.</span><span class="sxs-lookup"><span data-stu-id="db5d6-260">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="db5d6-261">Estos se tratan con más detalle en [Creación de contexto en tiempo de diseño](xref:core/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="db5d6-261">These are covered in more detail in [Design-Time Context Creation](xref:core/cli/dbcontext-creation).</span></span>

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="db5d6-262">Evitar problemas con el subprocesamiento de DbContext</span><span class="sxs-lookup"><span data-stu-id="db5d6-262">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="db5d6-263">Entity Framework Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db5d6-263">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="db5d6-264">Esto incluye la ejecución en paralelo de consultas asincrónicas y cualquier uso simultáneo explícito desde varios subprocesos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-264">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="db5d6-265">Por tanto, espere siempre llamadas asincrónicas de inmediato mediante el operador `await` o use instancias de `DbContext` independientes para operaciones que se ejecuten en paralelo.</span><span class="sxs-lookup"><span data-stu-id="db5d6-265">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="db5d6-266">Cuando EF Core detecte un intento de usar una instancia de `DbContext` simultáneamente, verá un elemento `InvalidOperationException` con un mensaje como este:</span><span class="sxs-lookup"><span data-stu-id="db5d6-266">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="db5d6-267">Se inició una segunda operación en este contexto antes de que se completara una operación anterior.</span><span class="sxs-lookup"><span data-stu-id="db5d6-267">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="db5d6-268">Esto se debe normalmente a distintos subprocesos que usan la misma instancia de DbContext. Sin embargo, no se garantiza que los miembros de instancia sean seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-268">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="db5d6-269">Cuando el acceso simultáneo no se detecta, puede dar lugar a un comportamiento indefinido, a bloqueos de la aplicación y a daño en los datos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-269">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="db5d6-270">Hay errores comunes que pueden dar lugar accidentalmente a un acceso simultáneo en la misma instancia de `DbContext`:</span><span class="sxs-lookup"><span data-stu-id="db5d6-270">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="asynchronous-operation-pitfalls"></a><span data-ttu-id="db5d6-271">Errores de operaciones asincrónicas</span><span class="sxs-lookup"><span data-stu-id="db5d6-271">Asynchronous operation pitfalls</span></span>

<span data-ttu-id="db5d6-272">Los métodos asincrónicos habilitan EF Core para iniciar operaciones con acceso a la base de datos sin bloqueos.</span><span class="sxs-lookup"><span data-stu-id="db5d6-272">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="db5d6-273">Pero si un llamador no espera a que finalice uno de estos métodos y sigue realizando otras operaciones en el `DbContext`, el estado del `DbContext` puede estar (y muy probablemente lo estará) dañado.</span><span class="sxs-lookup"><span data-stu-id="db5d6-273">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="db5d6-274">Espere siempre métodos asincrónicos de EF Core de inmediato.</span><span class="sxs-lookup"><span data-stu-id="db5d6-274">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a><span data-ttu-id="db5d6-275">Uso compartido implícito de instancias de DbContext mediante la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="db5d6-275">Implicitly sharing DbContext instances via dependency injection</span></span>

<span data-ttu-id="db5d6-276">El método de extensión [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) registra tipos de `DbContext` con una [duración de ámbito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="db5d6-276">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="db5d6-277">Está protegido frente a problemas de acceso simultáneo en la mayoría de las aplicaciones ASP.NET Core, ya que solo hay un subproceso ejecutando cada solicitud de cliente en un momento dado, y cada solicitud obtiene un ámbito de inserción de dependencias independiente (y, por tanto, una instancia de `DbContext` independiente).</span><span class="sxs-lookup"><span data-stu-id="db5d6-277">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="db5d6-278">Para el modelo de hospedaje de Blazor Server, se usa una solicitud lógica para mantener el circuito de usuario de Blazor y, por tanto, solo hay disponible una instancia de DbContext con ámbito por circuito de usuario si se usa el ámbito de inserción predeterminado.</span><span class="sxs-lookup"><span data-stu-id="db5d6-278">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="db5d6-279">Cualquier código que ejecute explícitamente varios subprocesos en paralelo debe garantizar que no se tenga nunca acceso a las instancias de `DbContext` simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="db5d6-279">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="db5d6-280">Con la inserción de dependencias, esto se puede lograr registrando el contexto como con ámbito y creando ámbitos (mediante `IServiceScopeFactory`) para cada subproceso, o bien registrando el `DbContext` como transitorio (mediante la sobrecarga de `AddDbContext`, que toma un parámetro `ServiceLifetime`).</span><span class="sxs-lookup"><span data-stu-id="db5d6-280">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="db5d6-281">Más lectura</span><span class="sxs-lookup"><span data-stu-id="db5d6-281">More reading</span></span>

- <span data-ttu-id="db5d6-282">Lea [Inserción de dependencias](/aspnet/core/fundamentals/dependency-injection) para obtener más información sobre el uso de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db5d6-282">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="db5d6-283">Lea [Pruebas](xref:core/testing/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="db5d6-283">Read [Testing](xref:core/testing/index) for more information.</span></span>
