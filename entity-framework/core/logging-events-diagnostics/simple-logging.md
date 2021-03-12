---
title: EF Core de registro simples
description: Registro desde un EF Core DbContext mediante LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 24a3de668a0e587dcc0ab2ae5f061b0eb4d4edcf
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024217"
---
# <a name="simple-logging"></a>Registro sencillo

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 5.0.

> [!TIP]
> Puede [descargar el ejemplo de este artículo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Logging/SimpleLogging) en github.

Se puede usar el registro simple de Entity Framework Core (EF Core) para obtener fácilmente los registros durante el desarrollo y la depuración de aplicaciones. Esta forma de registro requiere una configuración mínima y ningún paquete de NuGet adicional.

> [!TIP]
> EF Core también se integra con [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging), que requiere más configuración, pero suele ser más adecuado para el registro en aplicaciones de producción.

## <a name="configuration"></a>Configuración

Se puede acceder a los registros de EF Core desde cualquier tipo de aplicación mediante el uso de <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> al [configurar una instancia de DbContext](xref:core/dbcontext-configuration/index). Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Como alternativa, `LogTo` se puede llamar a como parte de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o al crear una instancia de que se va a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> pasar al `DbContext` constructor.

> [!TIP]
> La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext. Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.

## <a name="directing-the-logs"></a>Dirigir los registros

### <a name="logging-to-the-console"></a>Registro en la consola

`LogTo` requiere un <xref:System.Action%601> delegado que acepte una cadena. EF Core llamará a este delegado con una cadena para cada mensaje de registro generado. Después, el delegado debe hacer algo con el mensaje especificado.

El <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> método se utiliza a menudo para este delegado, como se muestra anteriormente. Esto hace que cada mensaje de registro se escriba en la consola.

### <a name="logging-to-the-debug-window"></a>Registro en la ventana de depuración

<xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> se puede usar para enviar la salida a la ventana de depuración en Visual Studio o en otros IDE. En este caso, se debe usar la [Sintaxis lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) porque la `Debug` clase se compila fuera de las compilaciones de versión. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a>Registro en un archivo

Para escribir en un archivo, es necesario crear un <xref:System.IO.StreamWriter> o similar para el archivo. El <xref:System.IO.StreamWriter.WriteLine%2A> método se puede usar como en los otros ejemplos anteriores. Recuerde asegurarse de que el archivo se cierra limpiamente eliminando el escritor cuando se desecha el contexto. Por ejemplo:

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true);

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }

    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para registrar archivos en aplicaciones de producción.

## <a name="getting-detailed-messages"></a>Obtención de mensajes detallados

### <a name="sensitive-data"></a>Información confidencial

De forma predeterminada, EF Core no incluirá los valores de los datos en los mensajes de excepción. Esto se debe a que estos datos pueden ser confidenciales y se pueden revelar en el uso de producción si no se controla una excepción.

Sin embargo, conocer los valores de datos, especialmente para las claves, puede ser muy útil al depurar. Esto se puede habilitar en EF Core mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Excepciones de consulta detalladas

Por motivos de rendimiento, EF Core no encapsula cada llamada para leer un valor del proveedor de base de datos en un bloque try-catch. Sin embargo, en ocasiones se producen excepciones que son difíciles de diagnosticar, especialmente cuando la base de datos devuelve un valor NULL cuando el modelo no lo permite.

La activación de hará que <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduzca estos bloques try-catch y, por tanto, proporcione errores más detallados. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a>Filtrado

### <a name="log-levels"></a>Niveles de registro

Cada mensaje de registro de EF Core se asigna a un nivel definido por la <xref:Microsoft.Extensions.Logging.LogLevel> enumeración. De forma predeterminada, EF Core registro simple incluye todos los mensajes en el `Debug` nivel o superior. `LogTo` se puede pasar un nivel mínimo más alto para filtrar algunos mensajes. Por ejemplo, si `Information` se pasa el resultado en un conjunto mínimo de registros limitado al acceso a la base de datos y a algunos mensajes de mantenimiento.

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a>Mensajes específicos

A todos los mensajes de registro se les asigna un <xref:Microsoft.Extensions.Logging.EventId> . Se puede tener acceso a estos identificadores desde la <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> clase o la <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> clase para mensajes específicos relacionales. Un proveedor de bases de datos también puede tener identificadores específicos del proveedor en una clase similar. Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.

`LogTo` se puede configurar para registrar solo los mensajes asociados a uno o varios identificadores de eventos. Por ejemplo, para registrar solo los mensajes para el contexto que se está inicializando o eliminando:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a>Categorías de mensajes

Cada mensaje de registro se asigna a una categoría de registrador jerárquico con nombre. Las categorías son:

| Category                                             | error de Hadoop
|:-----------------------------------------------------|-------------------------------------------------
| Microsoft.EntityFrameworkCore                        | Todos los mensajes de EF Core
| Microsoft. EntityFrameworkCore. Database               | Todas las interacciones de base de datos
| Microsoft. EntityFrameworkCore. Database. Connection    | Usos de una conexión de base de datos
| Microsoft. EntityFrameworkCore. Database. Command       | Usos de un comando de base de datos
| Microsoft. EntityFrameworkCore. Database. Transaction   | Usos de una transacción de base de datos
| Microsoft. EntityFrameworkCore. Update                 | Guardar entidades, excluidas las interacciones de base de datos
| Microsoft. EntityFrameworkCore. Model                  | Todas las interacciones entre modelos y metadatos
| Microsoft. EntityFrameworkCore. Model. Validation       | Validación de modelos
| Microsoft. EntityFrameworkCore. Query                  | Consultas, excluidas las interacciones de base de datos
| Microsoft. EntityFrameworkCore. Infrastructure         | Eventos generales, como la creación de contexto
| Microsoft. EntityFrameworkCore. scaffolding            | Ingeniería inversa de base de datos
| Microsoft. EntityFrameworkCore. Migrations             | Migraciones
| Microsoft. EntityFrameworkCore. ChangeTracking         | Interacciones de seguimiento de cambios

`LogTo` se puede configurar para registrar solo los mensajes de una o varias categorías. Por ejemplo, para registrar solo interacciones de base de datos:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

Tenga en cuenta que la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> clase proporciona una API jerárquica para buscar una categoría y evita la necesidad de codificar cadenas de forma rígida.

Puesto que las categorías son jerárquicas, este ejemplo con la `Database` categoría incluirá todos los mensajes para las subcategorías `Database.Connection` , `Database.Command` y `Database.Transaction` .

### <a name="custom-filters"></a>Filtros personalizados

`LogTo` permite usar un filtro personalizado para los casos en los que ninguna de las opciones de filtrado anteriores sean suficientes. Por ejemplo, para registrar cualquier mensaje en `Information` el nivel o superior, así como mensajes para abrir y cerrar una conexión:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> El filtrado mediante filtros personalizados o el uso de cualquiera de las otras opciones que se muestran aquí es más eficaz que el filtrado del `LogTo` delegado. Esto se debe a que, si el filtro determina que el mensaje no se debe registrar, no se crea ni siquiera el mensaje de registro.

## <a name="configuration-for-specific-messages"></a>Configuración de mensajes específicos

La API de EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> permite a las aplicaciones cambiar lo que ocurre cuando se encuentra un evento específico. Se puede usar para:

* Cambiar el nivel de registro en el que se registra el evento
* Omitir el registro del evento por completo
* Producir una excepción cuando se produce el evento

### <a name="changing-the-log-level-for-an-event"></a>Cambiar el nivel de registro de un evento

En el ejemplo anterior se usaba un filtro personalizado para registrar todos `LogLevel.Information` los mensajes en, así como dos eventos definidos para `LogLevel.Debug` . Lo mismo se puede lograr cambiando el nivel de registro de los dos `Debug` eventos a `Information` :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Suprimir el registro de un evento

De forma similar, se puede suprimir un evento individual del registro. Esto es especialmente útil para omitir una advertencia que se ha revisado y comprendido. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Throw para un evento

Por último, EF Core se pueden configurar para que se inicien para un evento determinado. Esto es especialmente útil para cambiar una advertencia a un error. (De hecho, este era el propósito original del `ConfigureWarnings` método, es decir, el nombre). Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a>Contenido y formato del mensaje

El contenido predeterminado de `LogTo` se formatea en varias líneas. La primera línea contiene metadatos de mensaje:

* <xref:Microsoft.Extensions.Logging.LogLevel>Como prefijo de cuatro caracteres
* Marca de tiempo local, con formato para la referencia cultural actual
* <xref:Microsoft.Extensions.Logging.EventId>En el formulario que se puede copiar y pegar para obtener el miembro de <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> o una de las otras `EventId` clases, más el valor de identificador sin formato
* La categoría de eventos, tal y como se ha descrito anteriormente.

Por ejemplo:

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

Este contenido se puede personalizar pasando valores de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> , como se muestra en las secciones siguientes.

> [!TIP]
> Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para obtener más control sobre el formato del registro.

### <a name="using-utc-time"></a>Usar la hora UTC

De forma predeterminada, las marcas de tiempo están diseñadas para el consumo local durante la depuración. Use <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> para usar marcas de tiempo UTC independientes de la referencia cultural en su lugar, pero mantenga todo lo demás. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

En este ejemplo se da como resultado el siguiente formato de registro:

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

### <a name="single-line-logging"></a>Registro de una sola línea

A veces resulta útil obtener exactamente una línea por cada mensaje de registro. Esto puede habilitarse mediante <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> . Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

En este ejemplo se da como resultado el siguiente formato de registro:

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a>Otras opciones de contenido

Otras marcas de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> se pueden usar para reducir la cantidad de metadatos que se incluyen en el registro. Esto puede ser útil junto con el registro de una sola línea. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

En este ejemplo se da como resultado el siguiente formato de registro:

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a>Pasar de EF6

EF Core registro simple difiere de <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6 en dos aspectos importantes:

* Los mensajes de registro no se limitan únicamente a interacciones de base de datos
* El registro se debe configurar en el momento de inicialización del contexto.

En la primera diferencia, el filtrado descrito anteriormente se puede usar para limitar los mensajes que se registran.

La segunda diferencia es un cambio intencionado para mejorar el rendimiento, ya que no genera mensajes de registro cuando no es necesario. Sin embargo, todavía es posible obtener un comportamiento similar a EF6 mediante la creación de una `Log` propiedad en `DbContext` y, a continuación, usarla solo cuando se haya establecido. Por ejemplo:

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
