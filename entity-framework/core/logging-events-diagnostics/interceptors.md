---
title: 'Interceptores: EF Core'
description: Interceptación de operaciones de base de datos y otros eventos
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: 22d860a083c5ece9be109be630c3ce01dd742bf2
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003423"
---
# <a name="interceptors"></a>Interceptores

Los interceptores de Entity Framework Core (EF Core) permiten la interceptación, modificación y/o supresión de operaciones de EF Core. Esto incluye operaciones de base de datos de bajo nivel tales como ejecutar un comando, así como operaciones de nivel superior tales como llamadas a SaveChanges.

Los interceptores son distintos del registro y el diagnóstico en que permiten la modificación o supresión de la operación que se intercepta. El [registro sencillo](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) son mejores opciones de registro.

Los interceptores se registran por instancia de DbContext al configurarse el contexto. Use una [escucha de diagnóstico](xref:core/logging-events-diagnostics/diagnostic-listeners) para obtener la misma información, pero para todas las instancias de DbContext del proceso.

## <a name="registering-interceptors"></a>Registro de interceptores

Los interceptores se registran mediante <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> al [configurar una instancia de DbContext](xref:core/dbcontext-configuration/index). Esto se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Por ejemplo:

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

Como alternativa, `AddInterceptors` se puede llamar a como parte de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o al crear una instancia de que se va a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> pasar al constructor DbContext.

> [!TIP]
> La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext. Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.

A menudo, los interceptores no tienen estado, lo que significa que se puede usar una única instancia de interceptor para todas las instancias de DbContext. Por ejemplo:

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor 
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

Cada instancia de interceptor debe implementar una o más interfaces derivadas de <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> . Cada instancia solo debe registrarse una vez incluso si implementa varias interfaces de interceptación; EF Core enrutará los eventos de cada interfaz según corresponda.

## <a name="database-interception"></a>Interceptación de base de datos

> [!NOTE]
> La intercepción de bases de datos se presentó en EF Core 3,0 y solo está disponible para los proveedores de bases de datos relacionales.
> La compatibilidad con el punto de retorno se presentó en EF Core 5,0.

La interceptación de base de datos de bajo nivel se divide en las tres interfaces que se muestran en la tabla siguiente.

| Interceptor                                                            | Operaciones de base de datos interceptadas
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | Crear comandos</br>Ejecutar comandos</br>Errores de comando</br>Desechar el DbDataReader del comando
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | Abrir y cerrar conexiones</br>Errores de conexión
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | Crear transacciones</br>Uso de transacciones existentes</br>Confirmar transacciones</br>Revertir transacciones</br>Crear y usar puntos de retorno</br>Errores de transacción

Las clases base <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> y <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contienen implementaciones no operativas para cada método de la interfaz correspondiente. Utilice las clases base para evitar la necesidad de implementar métodos de intercepción no utilizados.

Los métodos de cada tipo de interceptor se encuentran en pares, con el primero que se llama antes de que se inicie la operación de base de datos, y el segundo después de que se haya completado la operación. Por ejemplo. Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> se llama a antes de que se ejecute una consulta y <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> se llama después de que se haya enviado la consulta a la base de datos.

Cada par de métodos tiene dos variaciones sincrónicas y asincrónicas. Esto permite que se produzca la e/s asincrónica, como la solicitud de un token de acceso, como parte de la interceptación de una operación de base de datos asincrónica.

### <a name="example-command-interception-to-add-query-hints"></a>Ejemplo: intercepción de comandos para agregar sugerencias de consulta

> [!TIP]  
> Puede [descargar el ejemplo de interceptor de comandos](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) de github.

<xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor>Se puede utilizar para modificar SQL antes de enviarlo a la base de datos. En este ejemplo se muestra cómo modificar el SQL para incluir una sugerencia de consulta.

A menudo, la parte más complicada de la interceptación es determinar si el comando corresponde a la consulta que debe modificarse. Analizar el SQL es una opción, pero tiende a ser frágil. Otra opción consiste en usar [EF Core etiquetas de consulta](xref:core/querying/tags) para etiquetar cada consulta que se debe modificar. Por ejemplo:

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

Esta etiqueta se puede detectar en el interceptor ya que siempre se incluirá como comentario en la primera línea del texto del comando. Al detectar la etiqueta, se modifica el SQL de la consulta para agregar la sugerencia adecuada:

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

Aviso:

* El interceptor hereda de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> para evitar tener que implementar todos los métodos en la interfaz del interceptor.
* El interceptor implementa métodos de sincronización y asincrónicos. Esto garantiza que se aplica la misma sugerencia de consulta a las consultas asincrónicas y de sincronización.
* El interceptor implementa los `Executing` métodos a los que llama EF Core con el SQL generado _antes_ de que se envíe a la base de datos. Compare esto con los `Executed` métodos, a los que se llama después de que se devuelva la llamada a la base de datos.

Al ejecutar el código de este ejemplo, se genera lo siguiente cuando se etiqueta una consulta:

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

Por otro lado, cuando una consulta no se etiqueta, se envía a la base de datos sin modificar:

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a>Ejemplo: intercepción de conexión para la autenticación de SQL Azure mediante ADD

> [!TIP]  
> Puede [descargar el ejemplo de interceptor de conexión](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) desde github.

<xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor>Se puede utilizar para manipular el antes de que <xref:System.Data.Common.DbConnection> se utilice para conectarse a la base de datos. Se puede usar para obtener un token de acceso Azure Active Directory (AAD). Por ejemplo:

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) ahora admite la autenticación de AAD a través de la cadena de conexión. Consulte <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> para obtener más información.

> [!WARNING]
> Observe que el interceptor produce si se realiza una llamada de sincronización para abrir la conexión. Esto se debe a que no hay ningún método no asincrónico para obtener el token de acceso y no hay [ninguna manera universal y sencilla de llamar a un método asincrónico desde el contexto no asincrónico sin arriesgarse al interbloqueo](https://devblogs.microsoft.com/dotnet/configureawait-faq/).

> [!WARNING]
> en algunas situaciones, es posible que el token de acceso no se almacene en caché automáticamente en el proveedor de tokens de Azure. Según el tipo de token solicitado, puede que tenga que implementar su propio almacenamiento en caché aquí.

### <a name="example-advanced-command-interception-for-caching"></a>Ejemplo: intercepción de comandos avanzada para el almacenamiento en caché

> [!TIP]  
> Puede [descargar el ejemplo de interceptor de comandos avanzado](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) de github.

Los interceptores de EF Core pueden:

* Indicar EF Core para suprimir la ejecución de la operación que se va a interceptar
* Cambiar el resultado de la operación devuelto a EF Core

En este ejemplo se muestra un interceptor que usa estas características para comportarse como una memoria caché de segundo nivel primitiva. Se devuelven resultados de consulta en caché para una consulta específica, evitando un ida y vuelta de base de datos.

> [!WARNING]
> Tenga cuidado al cambiar el comportamiento predeterminado de EF Core de esta manera. EF Core pueden comportarse de maneras inesperadas si obtiene un resultado anómalo que no puede procesar correctamente. Además, este ejemplo muestra los conceptos del interceptor; no se ha diseñado como plantilla para una implementación de caché de segundo nivel sólida.

En este ejemplo, la aplicación ejecuta con frecuencia una consulta para obtener el "mensaje diario" más reciente:

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

Esta consulta se [etiqueta](xref:core/querying/tags) para que se pueda detectar fácilmente en el interceptor. La idea es consultar solo la base de datos de un mensaje nuevo cada día. En otras ocasiones, la aplicación usará un resultado almacenado en caché. (El ejemplo utiliza el retraso de 10 segundos en el ejemplo para simular un nuevo día).

#### <a name="interceptor-state"></a>Estado del interceptor

Este interceptor es con estado: almacena el identificador y el texto del mensaje del mensaje diario más reciente consultado, más la hora a la que se ejecutó la consulta. Debido a este estado, también se necesita un [bloqueo](/dotnet/csharp/language-reference/keywords/lock-statement) , ya que el almacenamiento en caché requiere que varias instancias de contexto utilicen el mismo interceptor.

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a>Antes de la ejecución

En el `Executing` método (es decir, antes de realizar una llamada a la base de datos), el interceptor detecta la consulta etiquetada y, a continuación, comprueba si hay un resultado almacenado en caché. Si se encuentra un resultado de este tipo, se suprime la consulta y se usan en su lugar los resultados almacenados en caché.

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

Observe cómo el código llama a <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> y pasa un reemplazo <xref:System.Data.Common.DbDataReader> que contiene los datos almacenados en caché. A continuación, se devuelve este InterceptionResult, lo que provoca la supresión de la ejecución de la consulta. En su lugar, EF Core usa el lector de reemplazo como los resultados de la consulta.

Este interceptor también manipula el texto del comando. Esta manipulación no es necesaria, pero mejora la claridad en los mensajes de registro. No es necesario que el texto del comando sea un SQL válido, ya que la consulta no se va a ejecutar.

#### <a name="after-execution"></a>Después de la ejecución

Si no hay ningún mensaje en caché disponible, o si ha expirado, el código anterior no suprime el resultado. Por tanto, EF Core ejecutará la consulta de la manera habitual. Después se devolverá al método del interceptor `Executed` después de la ejecución. En este punto, si el resultado no es ya un lector almacenado en memoria caché, el nuevo identificador de mensaje y la cadena se exfieren del lector real y se almacenan en caché listos para el siguiente uso de esta consulta.

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a>Demostración

El [ejemplo de interceptor de almacenamiento en caché](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contiene una aplicación de consola simple que consulta los mensajes diarios para probar el almacenamiento en caché:

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }
        
        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

Esta acción devuelve la siguiente salida:

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

Tenga en cuenta la salida del registro de que la aplicación sigue utilizando el mensaje almacenado en caché hasta que expire el tiempo de espera, momento en el que se vuelve a consultar la base de datos para cualquier mensaje nuevo.

## <a name="savechanges-interception"></a>Interceptación de SaveChanges

> [!NOTE]
> La intercepción de SaveChanges se presentó en EF Core 5,0.

> [!TIP]  
> Puede [descargar el ejemplo de interceptor de SaveChanges](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) desde github.

<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>los puntos de interceptación y se definen mediante el`ISaveChangesInterceptor` <!-- Issue #2748 --> interfaz. Como para otros interceptores, el `SaveChangesInterceptor` <!-- Issue #2748 --> la clase base con métodos no-OP se proporciona por comodidad.

> [!TIP]
> Los interceptores son eficaces. Sin embargo, en muchos casos puede ser más fácil invalidar el método SaveChanges o usar los [eventos .net para SaveChanges](xref:core/logging-events-diagnostics/events) expuesto en DbContext.

### <a name="example-savechanges-interception-for-auditing"></a>Ejemplo: intercepción de SaveChanges para la auditoría

SaveChanges se puede interceptar para crear un registro de auditoría independiente de los cambios realizados.

> [!NOTE]
> No pretende ser una solución de auditoría sólida. En su lugar, es un ejemplo sencillo que se usa para mostrar las características de la interceptación.

#### <a name="the-application-context"></a>El contexto de la aplicación

El [ejemplo de auditoría](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) usa un DbContext sencillo con blogs y publicaciones.

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

Observe que se registra una nueva instancia del interceptor para cada instancia de DbContext. Esto se debe a que el interceptor de auditoría contiene el estado vinculado a la instancia de contexto actual.

#### <a name="the-audit-context"></a>Contexto de auditoría

El ejemplo también contiene un segundo DbContext y el modelo que se usa para la base de datos de auditoría.

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a>El interceptor

La idea general para la auditoría con el interceptor es:

* Se crea un mensaje de auditoría al principio de SaveChanges y se escribe en la base de datos de auditoría.
* Se permite que SaveChanges continúe
* Si SaveChanges se realiza correctamente, se actualiza el mensaje de auditoría para indicar que la operación se ha realizado correctamente.
* Si se produce un error en SaveChanges, el mensaje de auditoría se actualiza para indicar el error.

La primera fase se controla antes de que los cambios se envíen a la base de datos mediante invalidaciones de `ISaveChangesInterceptor.SavingChanges` <!-- Issue #2748 -->  y `ISaveChangesInterceptor.SavingChangesAsync`<!-- Issue #2748 -->.

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

La invalidación de los métodos de sincronización y Async garantiza que la auditoría se realizará independientemente de si se llama a SaveChanges o SaveChangesAsync. Observe también que la sobrecarga asincrónica es capaz de realizar la e/s asincrónica sin bloqueo en la base de datos de auditoría. Puede que desee iniciar el método Sync SavingChanges para asegurarse de que toda la e/s de la base de datos sea asincrónica. Esto requiere que la aplicación siempre llame a SaveChangesAsync y nunca a SaveChanges.

#### <a name="the-audit-message"></a>Mensaje de auditoría

Cada método de interceptor tiene un `eventData` parámetro que proporciona información contextual sobre el evento que se va a interceptar. En este caso, el DbContext de la aplicación actual se incluye en los datos de evento, que se utiliza a continuación para crear un mensaje de auditoría.

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

El resultado es una `SaveChangesAudit` entidad con una colección de `EntityAudit` entidades, una para cada inserción, actualización o eliminación. A continuación, el interceptor inserta estas entidades en la base de datos de auditoría.

> [!TIP]
> ToString se invalida en cada EF Core clase de datos de evento para generar el mensaje de registro equivalente para el evento. Por ejemplo, al llamar a se `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0 inicializado ' BlogsContext ' con el proveedor ' Microsoft. EntityFrameworkCore. SQLite ' con Options: None".

#### <a name="detecting-success"></a>Detección correcta

La entidad Audit se almacena en el interceptor para que se pueda tener acceso a ella de nuevo una vez que SaveChanges se realiza correctamente o se produce un error. Para que se realice correctamente, `ISaveChangesInterceptor.SavedChanges` <!-- Issue #2748 --> o bien `ISaveChangesInterceptor.SavedChangesAsync` <!-- Issue #2748 --> Se llama a .

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

La entidad de auditoría está asociada al contexto de auditoría, ya que ya existe en la base de datos y debe actualizarse. A continuación, establecemos `Succeeded` y `EndTime` , que marca estas propiedades como modificadas, por lo que SaveChanges enviará una actualización a la base de datos de auditoría.

#### <a name="detecting-failure"></a>Detección de errores

El error se administra de forma muy similar a como se realiza correctamente, pero en el `ISaveChangesInterceptor.SaveChangesFailed` <!-- Issue #2748 --> o bien `ISaveChangesInterceptor.SaveChangesFailedAsync` <!-- Issue #2748 --> . Los datos del evento contienen la excepción que se produjo.

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a>Demostración

El [ejemplo de auditoría](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contiene una aplicación de consola simple que realiza cambios en la base de datos de blogs y, a continuación, muestra la auditoría que se ha creado.

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

El resultado muestra el contenido de la base de datos de auditoría:

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
