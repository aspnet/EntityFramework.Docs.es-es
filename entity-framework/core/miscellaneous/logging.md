---
title: EF Core de registro
description: Configuración del registro con Entity Framework Core
author: ajcvickers
ms.date: 10/06/2020
uid: core/miscellaneous/logging
ms.openlocfilehash: 389834b3822aeeaefb8c085538bc6359ccfa7094
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063015"
---
# <a name="logging"></a><span data-ttu-id="a2804-103">Registro</span><span class="sxs-lookup"><span data-stu-id="a2804-103">Logging</span></span>

> [!TIP]
> <span data-ttu-id="a2804-104">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="a2804-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="a2804-105">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="a2804-105">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="a2804-106">Esta característica se agregó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="a2804-106">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="a2804-107">Entity Framework Core (EF Core) genera mensajes de registro para operaciones como ejecutar una consulta o guardar cambios en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2804-107">Entity Framework Core (EF Core) generates log messages for operations such as executing a query or saving changes to the database.</span></span> <span data-ttu-id="a2804-108">Se puede tener acceso a ellos desde cualquier tipo de aplicación mediante el uso de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="a2804-108">These can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="a2804-109">al [configurar una instancia de DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="a2804-109">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="a2804-110">Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a2804-110">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a2804-111">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2804-111">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="a2804-112">Este concepto es similar a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6.</span><span class="sxs-lookup"><span data-stu-id="a2804-112">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="a2804-113">Consulte [registro sencillo](xref:core/miscellaneous/events/simple-logging) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="a2804-113">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="a2804-114">Aplicaciones de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2804-114">ASP.NET Core applications</span></span>

<span data-ttu-id="a2804-115">EF Core se integra automáticamente con los mecanismos de registro de ASP.NET Core siempre que `AddDbContext` `AddDbContextPool` se use o.</span><span class="sxs-lookup"><span data-stu-id="a2804-115">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="a2804-116">Por lo tanto, al usar ASP.NET Core, el registro debe configurarse tal y como se describe en la [documentación de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="a2804-116">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="a2804-117">Otras aplicaciones</span><span class="sxs-lookup"><span data-stu-id="a2804-117">Other applications</span></span>

<span data-ttu-id="a2804-118">El registro de EF Core requiere un ILoggerFactory que se configura a su vez con uno o más proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="a2804-118">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="a2804-119">Los proveedores comunes se incluyen en los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="a2804-119">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="a2804-120">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un registrador de consola simple.</span><span class="sxs-lookup"><span data-stu-id="a2804-120">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="a2804-121">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): admite las características ' registros de diagnóstico ' y ' flujo de registro ' de los servicios de App de Azure.</span><span class="sxs-lookup"><span data-stu-id="a2804-121">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="a2804-122">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): inicia sesión en un monitor de depurador con System. Diagnostics. Debug. WriteLine ().</span><span class="sxs-lookup"><span data-stu-id="a2804-122">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="a2804-123">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): registra en el registro de eventos de Windows.</span><span class="sxs-lookup"><span data-stu-id="a2804-123">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="a2804-124">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): admite EventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="a2804-124">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="a2804-125">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): registra en un agente de escucha de seguimiento mediante `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="a2804-125">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="a2804-126">Después de instalar los paquetes adecuados, la aplicación debe crear una instancia singleton/global de un LoggerFactory.</span><span class="sxs-lookup"><span data-stu-id="a2804-126">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="a2804-127">Por ejemplo, mediante el registrador de consola:</span><span class="sxs-lookup"><span data-stu-id="a2804-127">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="a2804-128">Versión 3.x</span><span class="sxs-lookup"><span data-stu-id="a2804-128">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="a2804-129">Versión 2.x</span><span class="sxs-lookup"><span data-stu-id="a2804-129">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="a2804-130">En el ejemplo de código siguiente se usa un `ConsoleLoggerProvider` constructor que ha quedado obsoleto en la versión 2,2 y se ha reemplazado en 3,0.</span><span class="sxs-lookup"><span data-stu-id="a2804-130">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="a2804-131">Es seguro omitir y suprimir las advertencias al usar 2,2.</span><span class="sxs-lookup"><span data-stu-id="a2804-131">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="a2804-132">Esta instancia singleton/global se debe registrar con EF Core en `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a2804-132">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="a2804-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2804-133">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="a2804-134">Es muy importante que las aplicaciones no creen una nueva instancia de ILoggerFactory para cada instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="a2804-134">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="a2804-135">Si lo hace, se producirá una fuga de memoria y un rendimiento deficiente.</span><span class="sxs-lookup"><span data-stu-id="a2804-135">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="a2804-136">Filtrar lo que se registra</span><span class="sxs-lookup"><span data-stu-id="a2804-136">Filtering what is logged</span></span>

<span data-ttu-id="a2804-137">La aplicación puede controlar lo que se registra mediante la configuración de un filtro en el ILoggerProvider.</span><span class="sxs-lookup"><span data-stu-id="a2804-137">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="a2804-138">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2804-138">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="a2804-139">Versión 3.x</span><span class="sxs-lookup"><span data-stu-id="a2804-139">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="a2804-140">Versión 2.x</span><span class="sxs-lookup"><span data-stu-id="a2804-140">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="a2804-141">En el ejemplo de código siguiente se usa un `ConsoleLoggerProvider` constructor que ha quedado obsoleto en la versión 2,2 y se ha reemplazado en 3,0.</span><span class="sxs-lookup"><span data-stu-id="a2804-141">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="a2804-142">Es seguro omitir y suprimir las advertencias al usar 2,2.</span><span class="sxs-lookup"><span data-stu-id="a2804-142">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

<span data-ttu-id="a2804-143">En este ejemplo, el registro se filtra para que solo se devuelvan mensajes:</span><span class="sxs-lookup"><span data-stu-id="a2804-143">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="a2804-144">en la categoría "Microsoft. EntityFrameworkCore. Database. Command"</span><span class="sxs-lookup"><span data-stu-id="a2804-144">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="a2804-145">en el nivel de "información"</span><span class="sxs-lookup"><span data-stu-id="a2804-145">at the 'Information' level</span></span>

<span data-ttu-id="a2804-146">Por EF Core, las categorías del registrador se definen en la `DbLoggerCategory` clase para facilitar la búsqueda de categorías, pero se resuelven en cadenas simples.</span><span class="sxs-lookup"><span data-stu-id="a2804-146">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="a2804-147">Puede encontrar más detalles sobre la infraestructura de registro subyacente en la [documentación de registro de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="a2804-147">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
