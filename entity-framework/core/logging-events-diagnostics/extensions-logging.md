---
title: Usar Microsoft. Extensions. Logging-EF Core
description: Registro desde EF Core mediante Microsoft. Extensions. Logging en ASP.NET Core y otros tipos de aplicaciones
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431345"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a><span data-ttu-id="23b35-103">Usar Microsoft. Extensions. Logging en EF Core</span><span class="sxs-lookup"><span data-stu-id="23b35-103">Using Microsoft.Extensions.Logging in EF Core</span></span>

<span data-ttu-id="23b35-104">[Microsoft. Extensions. Logging](/dotnet/core/extensions/logging) es un mecanismo de registro extensible con proveedores de complementos para muchos sistemas de registro comunes.</span><span class="sxs-lookup"><span data-stu-id="23b35-104">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="23b35-105">Tanto los complementos suministrados por Microsoft (por ejemplo, [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) como los complementos de terceros (por ejemplo, [Serilog. Extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) están disponibles como paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="23b35-105">Both Microsoft-supplied plug-ins (e.g [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) and third-party plug-ins (e.g. [Serilog.Extensions.Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) are available as NuGet packages.</span></span>

<span data-ttu-id="23b35-106">Entity Framework Core (EF Core) se integra totalmente con `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="23b35-106">Entity Framework Core (EF Core) fully integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="23b35-107">Sin embargo, considere la posibilidad de usar un [registro simple](xref:core/logging-events-diagnostics/simple-logging) para una forma más sencilla de registrar, especialmente en el caso de las aplicaciones que no usan la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="23b35-107">However, consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) for a simpler way to log, especially for applications that don't use dependency injection.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="23b35-108">Aplicaciones de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23b35-108">ASP.NET Core applications</span></span>

<span data-ttu-id="23b35-109">`Microsoft.Extensions.Logging` se [utiliza de forma predeterminada en las aplicaciones ASP.net Core](/aspnet/core/fundamentals/logging).</span><span class="sxs-lookup"><span data-stu-id="23b35-109">`Microsoft.Extensions.Logging` is [used by default in ASP.NET Core applications](/aspnet/core/fundamentals/logging).</span></span> <span data-ttu-id="23b35-110">Llamar a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .</span><span class="sxs-lookup"><span data-stu-id="23b35-110">Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A>.</span></span>

## <a name="other-application-types"></a><span data-ttu-id="23b35-111">Otros tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="23b35-111">Other application types</span></span>

<span data-ttu-id="23b35-112">Otros tipos de aplicaciones pueden usar [GenericHost](/dotnet/core/extensions/generic-host) para obtener los mismos patrones de inserción de dependencias que se usan en ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="23b35-112">Other application types can use the [GenericHost](/dotnet/core/extensions/generic-host) to get the same dependency injection patterns as are used in ASP.NET Core.</span></span> <span data-ttu-id="23b35-113">AddDbContext o AddDbContextPool se pueden usar de la misma manera que en las aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23b35-113">AddDbContext or AddDbContextPool can then be used just like in ASP.NET Core applications.</span></span>

<span data-ttu-id="23b35-114">`Microsoft.Extensions.Logging` también se puede usar para aplicaciones que no usan la inserción de dependencias, aunque el [registro sencillo](xref:core/logging-events-diagnostics/simple-logging) puede ser más fácil de configurar.</span><span class="sxs-lookup"><span data-stu-id="23b35-114">`Microsoft.Extensions.Logging` can also be used for applications that don't use dependency injection, although [simple logging](xref:core/logging-events-diagnostics/simple-logging) can be easier to set up.</span></span>

<span data-ttu-id="23b35-115">`Microsoft.Extensions.Logging` requiere la creación de un <xref:Microsoft.Extensions.Logging.LoggerFactory> .</span><span class="sxs-lookup"><span data-stu-id="23b35-115">`Microsoft.Extensions.Logging` requires creation of a <xref:Microsoft.Extensions.Logging.LoggerFactory>.</span></span> <span data-ttu-id="23b35-116">Este generador debe almacenarse como una instancia estática o global en algún lugar y usarse cada vez que se crea un DbContext.</span><span class="sxs-lookup"><span data-stu-id="23b35-116">This factory should be stored as a static/global instance somewhere and used each time a DbContext is created.</span></span> <span data-ttu-id="23b35-117">Por ejemplo, es habitual almacenar el generador del registrador como una propiedad estática en DbContext.</span><span class="sxs-lookup"><span data-stu-id="23b35-117">For example, it is common to store the logger factory as a static property on the DbContext.</span></span>

### <a name="ef-core-30-and-above"></a>[<span data-ttu-id="23b35-118">EF Core 3,0 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="23b35-118">EF Core 3.0 and above</span></span>](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[<span data-ttu-id="23b35-119">EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="23b35-119">EF Core 2.1</span></span>](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> <span data-ttu-id="23b35-120">En EF Core 2,1, es muy importante que las aplicaciones no creen una nueva instancia de LoggerFactory para cada instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="23b35-120">In EF Core 2.1, It is very important that applications do not create a new LoggerFactory instance for each DbContext instance.</span></span> <span data-ttu-id="23b35-121">Si lo hace, se producirá una fuga de memoria y un rendimiento deficiente.</span><span class="sxs-lookup"><span data-stu-id="23b35-121">Doing so will result in a memory leak and poor performance.</span></span> <span data-ttu-id="23b35-122">Esto se ha corregido en EF Core 3,0 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="23b35-122">This has been fixed in EF Core 3.0 and above.</span></span>

***

<span data-ttu-id="23b35-123">Esta instancia singleton/global se debe registrar con EF Core en <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> .</span><span class="sxs-lookup"><span data-stu-id="23b35-123">This singleton/global instance should then be registered with EF Core on the <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="23b35-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="23b35-124">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a><span data-ttu-id="23b35-125">Obtención de mensajes detallados</span><span class="sxs-lookup"><span data-stu-id="23b35-125">Getting detailed messages</span></span>

> [!TIP]
> <span data-ttu-id="23b35-126">La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="23b35-126">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="23b35-127">Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.</span><span class="sxs-lookup"><span data-stu-id="23b35-127">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="23b35-128">Información confidencial</span><span class="sxs-lookup"><span data-stu-id="23b35-128">Sensitive data</span></span>

<span data-ttu-id="23b35-129">De forma predeterminada, EF Core no incluirá los valores de los datos en los mensajes de excepción.</span><span class="sxs-lookup"><span data-stu-id="23b35-129">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="23b35-130">Esto se debe a que estos datos pueden ser confidenciales y se pueden revelar en el uso de producción si no se controla una excepción.</span><span class="sxs-lookup"><span data-stu-id="23b35-130">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="23b35-131">Sin embargo, conocer los valores de datos, especialmente para las claves, puede ser muy útil al depurar.</span><span class="sxs-lookup"><span data-stu-id="23b35-131">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="23b35-132">Esto se puede habilitar en EF Core mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="23b35-132">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="23b35-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="23b35-133">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="23b35-134">Excepciones de consulta detalladas</span><span class="sxs-lookup"><span data-stu-id="23b35-134">Detailed query exceptions</span></span>

<span data-ttu-id="23b35-135">Por motivos de rendimiento, EF Core no encapsula cada llamada para leer un valor del proveedor de base de datos en un bloque try-catch.</span><span class="sxs-lookup"><span data-stu-id="23b35-135">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="23b35-136">Sin embargo, en ocasiones se producen excepciones que son difíciles de diagnosticar, especialmente cuando la base de datos devuelve un valor NULL cuando el modelo no lo permite.</span><span class="sxs-lookup"><span data-stu-id="23b35-136">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="23b35-137">La activación de hará que <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduzca estos bloques try-catch y, por tanto, proporcione errores más detallados.</span><span class="sxs-lookup"><span data-stu-id="23b35-137">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="23b35-138">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="23b35-138">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="23b35-139">Configuración de mensajes específicos</span><span class="sxs-lookup"><span data-stu-id="23b35-139">Configuration for specific messages</span></span>

<span data-ttu-id="23b35-140">La API de EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> permite a las aplicaciones cambiar lo que ocurre cuando se encuentra un evento específico.</span><span class="sxs-lookup"><span data-stu-id="23b35-140">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="23b35-141">Se puede usar para:</span><span class="sxs-lookup"><span data-stu-id="23b35-141">This can be used to:</span></span>

* <span data-ttu-id="23b35-142">Cambiar el nivel de registro en el que se registra el evento</span><span class="sxs-lookup"><span data-stu-id="23b35-142">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="23b35-143">Omitir el registro del evento por completo</span><span class="sxs-lookup"><span data-stu-id="23b35-143">Skip logging the event altogether</span></span>
* <span data-ttu-id="23b35-144">Producir una excepción cuando se produce el evento</span><span class="sxs-lookup"><span data-stu-id="23b35-144">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="23b35-145">Cambiar el nivel de registro de un evento</span><span class="sxs-lookup"><span data-stu-id="23b35-145">Changing the log level for an event</span></span>

<span data-ttu-id="23b35-146">A veces puede resultar útil cambiar el nivel de registro predefinido para un evento.</span><span class="sxs-lookup"><span data-stu-id="23b35-146">Sometimes it can be useful to change the pre-defined log level for an event.</span></span> <span data-ttu-id="23b35-147">Por ejemplo, se puede usar para promover dos eventos adicionales de `LogLevel.Debug` a `LogLevel.Information` :</span><span class="sxs-lookup"><span data-stu-id="23b35-147">For example, this can be used to promote two additional events from `LogLevel.Debug` to `LogLevel.Information`:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="23b35-148">Suprimir el registro de un evento</span><span class="sxs-lookup"><span data-stu-id="23b35-148">Suppress logging an event</span></span>

<span data-ttu-id="23b35-149">De forma similar, se puede suprimir un evento individual del registro.</span><span class="sxs-lookup"><span data-stu-id="23b35-149">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="23b35-150">Esto es especialmente útil para omitir una advertencia que se ha revisado y comprendido.</span><span class="sxs-lookup"><span data-stu-id="23b35-150">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="23b35-151">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="23b35-151">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="23b35-152">Throw para un evento</span><span class="sxs-lookup"><span data-stu-id="23b35-152">Throw for an event</span></span>

<span data-ttu-id="23b35-153">Por último, EF Core se pueden configurar para que se inicien para un evento determinado.</span><span class="sxs-lookup"><span data-stu-id="23b35-153">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="23b35-154">Esto es especialmente útil para cambiar una advertencia a un error.</span><span class="sxs-lookup"><span data-stu-id="23b35-154">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="23b35-155">(De hecho, este era el propósito original del `ConfigureWarnings` método, es decir, el nombre). Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="23b35-155">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a><span data-ttu-id="23b35-156">Filtrado y otra configuración</span><span class="sxs-lookup"><span data-stu-id="23b35-156">Filtering and other configuration</span></span>

<span data-ttu-id="23b35-157">Consulte [Inicio de sesión en .net](/dotnet/core/extensions/logging) para obtener instrucciones sobre el filtrado de registros y otras configuraciones.</span><span class="sxs-lookup"><span data-stu-id="23b35-157">See [Logging in .NET](/dotnet/core/extensions/logging) for guidance on log filtering and other configuration.</span></span>

<span data-ttu-id="23b35-158">EF Core eventos de registro se definen en una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="23b35-158">EF Core logging events are defined in one of:</span></span>

* <span data-ttu-id="23b35-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> para eventos comunes a todos los proveedores de bases de datos de EF Core</span><span class="sxs-lookup"><span data-stu-id="23b35-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="23b35-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> para eventos comunes a todos los proveedores de bases de datos relacionales</span><span class="sxs-lookup"><span data-stu-id="23b35-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="23b35-161">Clase similar para los eventos específicos del proveedor de base de datos actual.</span><span class="sxs-lookup"><span data-stu-id="23b35-161">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="23b35-162">Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="23b35-162">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="23b35-163">Estas definiciones contienen los identificadores de evento, el nivel de registro y la categoría de cada evento, tal y como usa `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="23b35-163">These definitions contain the event IDs, log level, and category for each event, as used by `Microsoft.Extensions.Logging`.</span></span>
