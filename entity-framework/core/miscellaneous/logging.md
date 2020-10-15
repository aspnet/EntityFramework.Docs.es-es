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
# <a name="logging"></a>Registro

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) de este artículo en GitHub.

## <a name="simple-logging"></a>Registro sencillo

> [!NOTE]
> Esta característica se agregó en EF Core 5,0.

Entity Framework Core (EF Core) genera mensajes de registro para operaciones como ejecutar una consulta o guardar cambios en la base de datos. Se puede tener acceso a ellos desde cualquier tipo de aplicación mediante el uso de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> al [configurar una instancia de DbContext](xref:core/miscellaneous/configuring-dbcontext). Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Este concepto es similar a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6.

Consulte [registro sencillo](xref:core/miscellaneous/events/simple-logging) para obtener más información.

## <a name="aspnet-core-applications"></a>Aplicaciones de ASP.NET Core

EF Core se integra automáticamente con los mecanismos de registro de ASP.NET Core siempre que `AddDbContext` `AddDbContextPool` se use o. Por lo tanto, al usar ASP.NET Core, el registro debe configurarse tal y como se describe en la [documentación de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).

## <a name="other-applications"></a>Otras aplicaciones

El registro de EF Core requiere un ILoggerFactory que se configura a su vez con uno o más proveedores de registro. Los proveedores comunes se incluyen en los siguientes paquetes:

* [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un registrador de consola simple.
* [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): admite las características ' registros de diagnóstico ' y ' flujo de registro ' de los servicios de App de Azure.
* [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): inicia sesión en un monitor de depurador con System. Diagnostics. Debug. WriteLine ().
* [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): registra en el registro de eventos de Windows.
* [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): admite EventSource/EventListener.
* [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): registra en un agente de escucha de seguimiento mediante `System.Diagnostics.TraceSource.TraceEvent()` .

Después de instalar los paquetes adecuados, la aplicación debe crear una instancia singleton/global de un LoggerFactory. Por ejemplo, mediante el registrador de consola:

### <a name="version-3x"></a>[Versión 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Versión 2.x](#tab/v2)

> [!NOTE]
> En el ejemplo de código siguiente se usa un `ConsoleLoggerProvider` constructor que ha quedado obsoleto en la versión 2,2 y se ha reemplazado en 3,0. Es seguro omitir y suprimir las advertencias al usar 2,2.

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

Esta instancia singleton/global se debe registrar con EF Core en `DbContextOptionsBuilder` . Por ejemplo:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> Es muy importante que las aplicaciones no creen una nueva instancia de ILoggerFactory para cada instancia de contexto. Si lo hace, se producirá una fuga de memoria y un rendimiento deficiente.

## <a name="filtering-what-is-logged"></a>Filtrar lo que se registra

La aplicación puede controlar lo que se registra mediante la configuración de un filtro en el ILoggerProvider. Por ejemplo:

### <a name="version-3x"></a>[Versión 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Versión 2.x](#tab/v2)

> [!NOTE]
> En el ejemplo de código siguiente se usa un `ConsoleLoggerProvider` constructor que ha quedado obsoleto en la versión 2,2 y se ha reemplazado en 3,0. Es seguro omitir y suprimir las advertencias al usar 2,2.

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

En este ejemplo, el registro se filtra para que solo se devuelvan mensajes:

* en la categoría "Microsoft. EntityFrameworkCore. Database. Command"
* en el nivel de "información"

Por EF Core, las categorías del registrador se definen en la `DbLoggerCategory` clase para facilitar la búsqueda de categorías, pero se resuelven en cadenas simples.

Puede encontrar más detalles sobre la infraestructura de registro subyacente en la [documentación de registro de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).
