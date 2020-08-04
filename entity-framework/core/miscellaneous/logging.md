---
title: EF Core de registro
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 4f39e0ad1f061970aae7f7eb7abdc72e4bb0d691
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526464"
---
# <a name="logging"></a><span data-ttu-id="2b33e-102">Registro</span><span class="sxs-lookup"><span data-stu-id="2b33e-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="2b33e-103">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="2b33e-103">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="2b33e-104">Aplicaciones de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b33e-104">ASP.NET Core applications</span></span>

<span data-ttu-id="2b33e-105">EF Core se integra automáticamente con los mecanismos de registro de ASP.NET Core siempre que `AddDbContext` `AddDbContextPool` se use o.</span><span class="sxs-lookup"><span data-stu-id="2b33e-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="2b33e-106">Por lo tanto, al usar ASP.NET Core, el registro debe configurarse tal y como se describe en la [documentación de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="2b33e-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="2b33e-107">Otras aplicaciones</span><span class="sxs-lookup"><span data-stu-id="2b33e-107">Other applications</span></span>

<span data-ttu-id="2b33e-108">El registro de EF Core requiere un ILoggerFactory que se configura a su vez con uno o más proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="2b33e-108">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="2b33e-109">Los proveedores comunes se incluyen en los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="2b33e-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="2b33e-110">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un registrador de consola simple.</span><span class="sxs-lookup"><span data-stu-id="2b33e-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="2b33e-111">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): admite las características ' registros de diagnóstico ' y ' flujo de registro ' de los servicios de App de Azure.</span><span class="sxs-lookup"><span data-stu-id="2b33e-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="2b33e-112">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): inicia sesión en un monitor de depurador con System. Diagnostics. Debug. WriteLine ().</span><span class="sxs-lookup"><span data-stu-id="2b33e-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="2b33e-113">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): registra en el registro de eventos de Windows.</span><span class="sxs-lookup"><span data-stu-id="2b33e-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="2b33e-114">[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): admite EventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="2b33e-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="2b33e-115">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): registra en un agente de escucha de seguimiento mediante `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="2b33e-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="2b33e-116">Después de instalar los paquetes adecuados, la aplicación debe crear una instancia singleton/global de un LoggerFactory.</span><span class="sxs-lookup"><span data-stu-id="2b33e-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="2b33e-117">Por ejemplo, mediante el registrador de consola:</span><span class="sxs-lookup"><span data-stu-id="2b33e-117">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="2b33e-118">Versión 3. x</span><span class="sxs-lookup"><span data-stu-id="2b33e-118">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="2b33e-119">Versión 2.x</span><span class="sxs-lookup"><span data-stu-id="2b33e-119">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="2b33e-120">En el ejemplo de código siguiente se usa un `ConsoleLoggerProvider` constructor que ha quedado obsoleto en la versión 2,2 y se ha reemplazado en 3,0.</span><span class="sxs-lookup"><span data-stu-id="2b33e-120">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="2b33e-121">Es seguro omitir y suprimir las advertencias al usar 2,2.</span><span class="sxs-lookup"><span data-stu-id="2b33e-121">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="2b33e-122">Esta instancia singleton/global se debe registrar con EF Core en `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2b33e-122">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="2b33e-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b33e-123">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="2b33e-124">Es muy importante que las aplicaciones no creen una nueva instancia de ILoggerFactory para cada instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="2b33e-124">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="2b33e-125">Si lo hace, se producirá una fuga de memoria y un rendimiento deficiente.</span><span class="sxs-lookup"><span data-stu-id="2b33e-125">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="2b33e-126">Filtrar lo que se registra</span><span class="sxs-lookup"><span data-stu-id="2b33e-126">Filtering what is logged</span></span>

<span data-ttu-id="2b33e-127">La aplicación puede controlar lo que se registra mediante la configuración de un filtro en el ILoggerProvider.</span><span class="sxs-lookup"><span data-stu-id="2b33e-127">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="2b33e-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b33e-128">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="2b33e-129">Versión 3. x</span><span class="sxs-lookup"><span data-stu-id="2b33e-129">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="2b33e-130">Versión 2.x</span><span class="sxs-lookup"><span data-stu-id="2b33e-130">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="2b33e-131">En el ejemplo de código siguiente se usa un `ConsoleLoggerProvider` constructor que ha quedado obsoleto en la versión 2,2 y se ha reemplazado en 3,0.</span><span class="sxs-lookup"><span data-stu-id="2b33e-131">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="2b33e-132">Es seguro omitir y suprimir las advertencias al usar 2,2.</span><span class="sxs-lookup"><span data-stu-id="2b33e-132">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

<span data-ttu-id="2b33e-133">En este ejemplo, el registro se filtra para que solo se devuelvan mensajes:</span><span class="sxs-lookup"><span data-stu-id="2b33e-133">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="2b33e-134">en la categoría "Microsoft. EntityFrameworkCore. Database. Command"</span><span class="sxs-lookup"><span data-stu-id="2b33e-134">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="2b33e-135">en el nivel de "información"</span><span class="sxs-lookup"><span data-stu-id="2b33e-135">at the 'Information' level</span></span>

<span data-ttu-id="2b33e-136">Por EF Core, las categorías del registrador se definen en la `DbLoggerCategory` clase para facilitar la búsqueda de categorías, pero se resuelven en cadenas simples.</span><span class="sxs-lookup"><span data-stu-id="2b33e-136">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="2b33e-137">Puede encontrar más detalles sobre la infraestructura de registro subyacente en la [documentación de registro de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="2b33e-137">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
