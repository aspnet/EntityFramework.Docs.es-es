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
# <a name="using-microsoftextensionslogging-in-ef-core"></a>Usar Microsoft. Extensions. Logging en EF Core

[Microsoft. Extensions. Logging](/dotnet/core/extensions/logging) es un mecanismo de registro extensible con proveedores de complementos para muchos sistemas de registro comunes. Tanto los complementos suministrados por Microsoft (por ejemplo, [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) como los complementos de terceros (por ejemplo, [Serilog. Extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) están disponibles como paquetes NuGet.

Entity Framework Core (EF Core) se integra totalmente con `Microsoft.Extensions.Logging` . Sin embargo, considere la posibilidad de usar un [registro simple](xref:core/logging-events-diagnostics/simple-logging) para una forma más sencilla de registrar, especialmente en el caso de las aplicaciones que no usan la inserción de dependencias.

## <a name="aspnet-core-applications"></a>Aplicaciones de ASP.NET Core

`Microsoft.Extensions.Logging` se [utiliza de forma predeterminada en las aplicaciones ASP.net Core](/aspnet/core/fundamentals/logging). Llamar a <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .

## <a name="other-application-types"></a>Otros tipos de aplicaciones

Otros tipos de aplicaciones pueden usar [GenericHost](/dotnet/core/extensions/generic-host) para obtener los mismos patrones de inserción de dependencias que se usan en ASP.net Core. AddDbContext o AddDbContextPool se pueden usar de la misma manera que en las aplicaciones ASP.NET Core.

`Microsoft.Extensions.Logging` también se puede usar para aplicaciones que no usan la inserción de dependencias, aunque el [registro sencillo](xref:core/logging-events-diagnostics/simple-logging) puede ser más fácil de configurar.

`Microsoft.Extensions.Logging` requiere la creación de un <xref:Microsoft.Extensions.Logging.LoggerFactory> . Este generador debe almacenarse como una instancia estática o global en algún lugar y usarse cada vez que se crea un DbContext. Por ejemplo, es habitual almacenar el generador del registrador como una propiedad estática en DbContext.

### <a name="ef-core-30-and-above"></a>[EF Core 3,0 y versiones posteriores](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[EF Core 2.1](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> En EF Core 2,1, es muy importante que las aplicaciones no creen una nueva instancia de LoggerFactory para cada instancia de DbContext. Si lo hace, se producirá una fuga de memoria y un rendimiento deficiente. Esto se ha corregido en EF Core 3,0 y versiones posteriores.

***

Esta instancia singleton/global se debe registrar con EF Core en <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> . Por ejemplo:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a>Obtención de mensajes detallados

> [!TIP]
> La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext. Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.

### <a name="sensitive-data"></a>Información confidencial

De forma predeterminada, EF Core no incluirá los valores de los datos en los mensajes de excepción. Esto se debe a que estos datos pueden ser confidenciales y se pueden revelar en el uso de producción si no se controla una excepción.

Sin embargo, conocer los valores de datos, especialmente para las claves, puede ser muy útil al depurar. Esto se puede habilitar en EF Core mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Por ejemplo:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Excepciones de consulta detalladas

Por motivos de rendimiento, EF Core no encapsula cada llamada para leer un valor del proveedor de base de datos en un bloque try-catch. Sin embargo, en ocasiones se producen excepciones que son difíciles de diagnosticar, especialmente cuando la base de datos devuelve un valor NULL cuando el modelo no lo permite.

La activación de hará que <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduzca estos bloques try-catch y, por tanto, proporcione errores más detallados. Por ejemplo:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a>Configuración de mensajes específicos

La API de EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> permite a las aplicaciones cambiar lo que ocurre cuando se encuentra un evento específico. Se puede usar para:

* Cambiar el nivel de registro en el que se registra el evento
* Omitir el registro del evento por completo
* Producir una excepción cuando se produce el evento

### <a name="changing-the-log-level-for-an-event"></a>Cambiar el nivel de registro de un evento

A veces puede resultar útil cambiar el nivel de registro predefinido para un evento. Por ejemplo, se puede usar para promover dos eventos adicionales de `LogLevel.Debug` a `LogLevel.Information` :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Suprimir el registro de un evento

De forma similar, se puede suprimir un evento individual del registro. Esto es especialmente útil para omitir una advertencia que se ha revisado y comprendido. Por ejemplo:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Throw para un evento

Por último, EF Core se pueden configurar para que se inicien para un evento determinado. Esto es especialmente útil para cambiar una advertencia a un error. (De hecho, este era el propósito original del `ConfigureWarnings` método, es decir, el nombre). Por ejemplo:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a>Filtrado y otra configuración

Consulte [Inicio de sesión en .net](/dotnet/core/extensions/logging) para obtener instrucciones sobre el filtrado de registros y otras configuraciones.

EF Core eventos de registro se definen en una de las siguientes:

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> para eventos comunes a todos los proveedores de bases de datos de EF Core
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> para eventos comunes a todos los proveedores de bases de datos relacionales
* Clase similar para los eventos específicos del proveedor de base de datos actual. Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.

Estas definiciones contienen los identificadores de evento, el nivel de registro y la categoría de cada evento, tal y como usa `Microsoft.Extensions.Logging` .
