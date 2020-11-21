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
# <a name="interceptors"></a><span data-ttu-id="b1ae3-103">Interceptores</span><span class="sxs-lookup"><span data-stu-id="b1ae3-103">Interceptors</span></span>

<span data-ttu-id="b1ae3-104">Los interceptores de Entity Framework Core (EF Core) permiten la interceptación, modificación y/o supresión de operaciones de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-104">Entity Framework Core (EF Core) interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="b1ae3-105">Esto incluye operaciones de base de datos de bajo nivel tales como ejecutar un comando, así como operaciones de nivel superior tales como llamadas a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-105">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="b1ae3-106">Los interceptores son distintos del registro y el diagnóstico en que permiten la modificación o supresión de la operación que se intercepta.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-106">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="b1ae3-107">El [registro sencillo](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) son mejores opciones de registro.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-107">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="b1ae3-108">Los interceptores se registran por instancia de DbContext al configurarse el contexto.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-108">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="b1ae3-109">Use una [escucha de diagnóstico](xref:core/logging-events-diagnostics/diagnostic-listeners) para obtener la misma información, pero para todas las instancias de DbContext del proceso.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="registering-interceptors"></a><span data-ttu-id="b1ae3-110">Registro de interceptores</span><span class="sxs-lookup"><span data-stu-id="b1ae3-110">Registering interceptors</span></span>

<span data-ttu-id="b1ae3-111">Los interceptores se registran mediante <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> al [configurar una instancia de DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b1ae3-111">Interceptors are registered using <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="b1ae3-112">Esto se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b1ae3-112">This is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b1ae3-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-113">For example:</span></span>

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

<span data-ttu-id="b1ae3-114">Como alternativa, `AddInterceptors` se puede llamar a como parte de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o al crear una instancia de que se va a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> pasar al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-114">Alternately, `AddInterceptors` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the DbContext constructor.</span></span>

> [!TIP]
> <span data-ttu-id="b1ae3-115">La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="b1ae3-116">Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

<span data-ttu-id="b1ae3-117">A menudo, los interceptores no tienen estado, lo que significa que se puede usar una única instancia de interceptor para todas las instancias de DbContext.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-117">Interceptors are often stateless, which means that a single interceptor instance can be used for all DbContext instances.</span></span> <span data-ttu-id="b1ae3-118">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-118">For example:</span></span>

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

<span data-ttu-id="b1ae3-119">Cada instancia de interceptor debe implementar una o más interfaces derivadas de <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> .</span><span class="sxs-lookup"><span data-stu-id="b1ae3-119">Every interceptor instance must implement one or more interface derived from <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor>.</span></span> <span data-ttu-id="b1ae3-120">Cada instancia solo debe registrarse una vez incluso si implementa varias interfaces de interceptación; EF Core enrutará los eventos de cada interfaz según corresponda.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-120">Each instance should only be registered once even if it implements multiple interception interfaces; EF Core will route events for each interface as appropriate.</span></span>

## <a name="database-interception"></a><span data-ttu-id="b1ae3-121">Interceptación de base de datos</span><span class="sxs-lookup"><span data-stu-id="b1ae3-121">Database interception</span></span>

> [!NOTE]
> <span data-ttu-id="b1ae3-122">La intercepción de bases de datos se presentó en EF Core 3,0 y solo está disponible para los proveedores de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-122">Database interception was introduced in EF Core 3.0 and is only available for relational database providers.</span></span>
> <span data-ttu-id="b1ae3-123">La compatibilidad con el punto de retorno se presentó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-123">Savepoint support was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="b1ae3-124">La interceptación de base de datos de bajo nivel se divide en las tres interfaces que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-124">Low-level database interception is split into the three interfaces shown in the following table.</span></span>

| <span data-ttu-id="b1ae3-125">Interceptor</span><span class="sxs-lookup"><span data-stu-id="b1ae3-125">Interceptor</span></span>                                                            | <span data-ttu-id="b1ae3-126">Operaciones de base de datos interceptadas</span><span class="sxs-lookup"><span data-stu-id="b1ae3-126">Database operations intercepted</span></span>
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | <span data-ttu-id="b1ae3-127">Crear comandos</span><span class="sxs-lookup"><span data-stu-id="b1ae3-127">Creating commands</span></span></br><span data-ttu-id="b1ae3-128">Ejecutar comandos</span><span class="sxs-lookup"><span data-stu-id="b1ae3-128">Executing commands</span></span></br><span data-ttu-id="b1ae3-129">Errores de comando</span><span class="sxs-lookup"><span data-stu-id="b1ae3-129">Command failures</span></span></br><span data-ttu-id="b1ae3-130">Desechar el DbDataReader del comando</span><span class="sxs-lookup"><span data-stu-id="b1ae3-130">Disposing the command's DbDataReader</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | <span data-ttu-id="b1ae3-131">Abrir y cerrar conexiones</span><span class="sxs-lookup"><span data-stu-id="b1ae3-131">Opening and closing connections</span></span></br><span data-ttu-id="b1ae3-132">Errores de conexión</span><span class="sxs-lookup"><span data-stu-id="b1ae3-132">Connection failures</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | <span data-ttu-id="b1ae3-133">Crear transacciones</span><span class="sxs-lookup"><span data-stu-id="b1ae3-133">Creating transactions</span></span></br><span data-ttu-id="b1ae3-134">Uso de transacciones existentes</span><span class="sxs-lookup"><span data-stu-id="b1ae3-134">Using existing transactions</span></span></br><span data-ttu-id="b1ae3-135">Confirmar transacciones</span><span class="sxs-lookup"><span data-stu-id="b1ae3-135">Committing transactions</span></span></br><span data-ttu-id="b1ae3-136">Revertir transacciones</span><span class="sxs-lookup"><span data-stu-id="b1ae3-136">Rolling back transactions</span></span></br><span data-ttu-id="b1ae3-137">Crear y usar puntos de retorno</span><span class="sxs-lookup"><span data-stu-id="b1ae3-137">Creating and using savepoints</span></span></br><span data-ttu-id="b1ae3-138">Errores de transacción</span><span class="sxs-lookup"><span data-stu-id="b1ae3-138">Transaction failures</span></span>

<span data-ttu-id="b1ae3-139">Las clases base <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> y <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contienen implementaciones no operativas para cada método de la interfaz correspondiente.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-139">The base classes <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor>, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor>, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contain no-op implementations for each method in the corresponding interface.</span></span> <span data-ttu-id="b1ae3-140">Utilice las clases base para evitar la necesidad de implementar métodos de intercepción no utilizados.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-140">Use the base classes to avoid the need to implement unused interception methods.</span></span>

<span data-ttu-id="b1ae3-141">Los métodos de cada tipo de interceptor se encuentran en pares, con el primero que se llama antes de que se inicie la operación de base de datos, y el segundo después de que se haya completado la operación.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-141">The methods on each interceptor type come in pairs, with the first being called before the database operation is started, and the second after the operation has completed.</span></span> <span data-ttu-id="b1ae3-142">Por ejemplo.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-142">For example.</span></span> <span data-ttu-id="b1ae3-143">Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> se llama a antes de que se ejecute una consulta y <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> se llama después de que se haya enviado la consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-143">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> is called before a query is executed, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> is called after query has been sent to the database.</span></span>

<span data-ttu-id="b1ae3-144">Cada par de métodos tiene dos variaciones sincrónicas y asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-144">Each pair of methods have both sync and async variations.</span></span> <span data-ttu-id="b1ae3-145">Esto permite que se produzca la e/s asincrónica, como la solicitud de un token de acceso, como parte de la interceptación de una operación de base de datos asincrónica.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-145">This allows for asynchronous I/O, such as requesting an access token, to happen as part of intercepting an async database operation.</span></span>

### <a name="example-command-interception-to-add-query-hints"></a><span data-ttu-id="b1ae3-146">Ejemplo: intercepción de comandos para agregar sugerencias de consulta</span><span class="sxs-lookup"><span data-stu-id="b1ae3-146">Example: Command interception to add query hints</span></span>

> [!TIP]  
> <span data-ttu-id="b1ae3-147">Puede [descargar el ejemplo de interceptor de comandos](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) de github.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-147">You can [download the command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) from GitHub.</span></span>

<span data-ttu-id="b1ae3-148"><xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor>Se puede utilizar para modificar SQL antes de enviarlo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-148">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> can be used to modify SQL before it is sent to the database.</span></span> <span data-ttu-id="b1ae3-149">En este ejemplo se muestra cómo modificar el SQL para incluir una sugerencia de consulta.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-149">This example shows how to modify the SQL to include a query hint.</span></span>

<span data-ttu-id="b1ae3-150">A menudo, la parte más complicada de la interceptación es determinar si el comando corresponde a la consulta que debe modificarse.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-150">Often, the trickiest part of the interception is determining when the command corresponds to the query that needs to be modified.</span></span> <span data-ttu-id="b1ae3-151">Analizar el SQL es una opción, pero tiende a ser frágil.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-151">Parsing the SQL is one option, but tends to be fragile.</span></span> <span data-ttu-id="b1ae3-152">Otra opción consiste en usar [EF Core etiquetas de consulta](xref:core/querying/tags) para etiquetar cada consulta que se debe modificar.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-152">Another option is to use [EF Core query tags](xref:core/querying/tags) to tag each query that should be modified.</span></span> <span data-ttu-id="b1ae3-153">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-153">For example:</span></span>

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

<span data-ttu-id="b1ae3-154">Esta etiqueta se puede detectar en el interceptor ya que siempre se incluirá como comentario en la primera línea del texto del comando.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-154">This tag can then be detected in the interceptor as it will always be included as a comment in the first line of the command text.</span></span> <span data-ttu-id="b1ae3-155">Al detectar la etiqueta, se modifica el SQL de la consulta para agregar la sugerencia adecuada:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-155">On detecting the tag, the query SQL is modified to add the appropriate hint:</span></span>

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

<span data-ttu-id="b1ae3-156">Aviso:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-156">Notice:</span></span>

* <span data-ttu-id="b1ae3-157">El interceptor hereda de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> para evitar tener que implementar todos los métodos en la interfaz del interceptor.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-157">The interceptor inherits from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> to avoid having to implement every method in the interceptor interface.</span></span>
* <span data-ttu-id="b1ae3-158">El interceptor implementa métodos de sincronización y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-158">The interceptor implements both sync and async methods.</span></span> <span data-ttu-id="b1ae3-159">Esto garantiza que se aplica la misma sugerencia de consulta a las consultas asincrónicas y de sincronización.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-159">This ensures that the same query hint is applied to sync and async queries.</span></span>
* <span data-ttu-id="b1ae3-160">El interceptor implementa los `Executing` métodos a los que llama EF Core con el SQL generado _antes_ de que se envíe a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-160">The interceptor implements the `Executing` methods which are called by EF Core with the generated SQL _before_ it is sent to the database.</span></span> <span data-ttu-id="b1ae3-161">Compare esto con los `Executed` métodos, a los que se llama después de que se devuelva la llamada a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-161">Contrast this with the `Executed` methods, which are called after the database call has returned.</span></span>

<span data-ttu-id="b1ae3-162">Al ejecutar el código de este ejemplo, se genera lo siguiente cuando se etiqueta una consulta:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-162">Running the code in this example generates the following when a query is tagged:</span></span>

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

<span data-ttu-id="b1ae3-163">Por otro lado, cuando una consulta no se etiqueta, se envía a la base de datos sin modificar:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-163">On the other hand, when a query is not tagged, then it is sent to the database unmodified:</span></span>

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a><span data-ttu-id="b1ae3-164">Ejemplo: intercepción de conexión para la autenticación de SQL Azure mediante ADD</span><span class="sxs-lookup"><span data-stu-id="b1ae3-164">Example: Connection interception for SQL Azure authentication using ADD</span></span>

> [!TIP]  
> <span data-ttu-id="b1ae3-165">Puede [descargar el ejemplo de interceptor de conexión](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) desde github.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-165">You can [download the connection interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) from GitHub.</span></span>

<span data-ttu-id="b1ae3-166"><xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor>Se puede utilizar para manipular el antes de que <xref:System.Data.Common.DbConnection> se utilice para conectarse a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-166">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> can be used to manipulate the <xref:System.Data.Common.DbConnection> before it is used to connect to the database.</span></span> <span data-ttu-id="b1ae3-167">Se puede usar para obtener un token de acceso Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="b1ae3-167">This can be used to obtain an Azure Active Directory (AAD) access token.</span></span> <span data-ttu-id="b1ae3-168">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-168">For example:</span></span>

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
> <span data-ttu-id="b1ae3-169">[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) ahora admite la autenticación de AAD a través de la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-169">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) now supports AAD authentication via connection string.</span></span> <span data-ttu-id="b1ae3-170">Consulte <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-170">See <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> for more information.</span></span>

> [!WARNING]
> <span data-ttu-id="b1ae3-171">Observe que el interceptor produce si se realiza una llamada de sincronización para abrir la conexión.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-171">Notice that the interceptor throws if a sync call is made to open the connection.</span></span> <span data-ttu-id="b1ae3-172">Esto se debe a que no hay ningún método no asincrónico para obtener el token de acceso y no hay [ninguna manera universal y sencilla de llamar a un método asincrónico desde el contexto no asincrónico sin arriesgarse al interbloqueo](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span><span class="sxs-lookup"><span data-stu-id="b1ae3-172">This is because there is no non-async method to obtain the access token and there is [no universal and simple way to call an async method from non-async context without risking deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span></span>

> [!WARNING]
> <span data-ttu-id="b1ae3-173">en algunas situaciones, es posible que el token de acceso no se almacene en caché automáticamente en el proveedor de tokens de Azure.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-173">in some situations the access token may not be cached automatically the Azure Token Provider.</span></span> <span data-ttu-id="b1ae3-174">Según el tipo de token solicitado, puede que tenga que implementar su propio almacenamiento en caché aquí.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-174">Depending on the kind of token requested, you may need to implement your own caching here.</span></span>

### <a name="example-advanced-command-interception-for-caching"></a><span data-ttu-id="b1ae3-175">Ejemplo: intercepción de comandos avanzada para el almacenamiento en caché</span><span class="sxs-lookup"><span data-stu-id="b1ae3-175">Example: Advanced command interception for caching</span></span>

> [!TIP]  
> <span data-ttu-id="b1ae3-176">Puede [descargar el ejemplo de interceptor de comandos avanzado](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) de github.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-176">You can [download the advanced command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) from GitHub.</span></span>

<span data-ttu-id="b1ae3-177">Los interceptores de EF Core pueden:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-177">EF Core interceptors can:</span></span>

* <span data-ttu-id="b1ae3-178">Indicar EF Core para suprimir la ejecución de la operación que se va a interceptar</span><span class="sxs-lookup"><span data-stu-id="b1ae3-178">Tell EF Core to suppress executing the operation being intercepted</span></span>
* <span data-ttu-id="b1ae3-179">Cambiar el resultado de la operación devuelto a EF Core</span><span class="sxs-lookup"><span data-stu-id="b1ae3-179">Change the result of the operation reported back to EF Core</span></span>

<span data-ttu-id="b1ae3-180">En este ejemplo se muestra un interceptor que usa estas características para comportarse como una memoria caché de segundo nivel primitiva.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-180">This example shows an interceptor that uses these features to behave like a primitive second-level cache.</span></span> <span data-ttu-id="b1ae3-181">Se devuelven resultados de consulta en caché para una consulta específica, evitando un ida y vuelta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-181">Cached query results are returned for a specific query, avoiding a database roundtrip.</span></span>

> [!WARNING]
> <span data-ttu-id="b1ae3-182">Tenga cuidado al cambiar el comportamiento predeterminado de EF Core de esta manera.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-182">Take care when changing the EF Core default behavior in this way.</span></span> <span data-ttu-id="b1ae3-183">EF Core pueden comportarse de maneras inesperadas si obtiene un resultado anómalo que no puede procesar correctamente.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-183">EF Core may behave in unexpected ways if it gets an abnormal result that it cannot process correctly.</span></span> <span data-ttu-id="b1ae3-184">Además, este ejemplo muestra los conceptos del interceptor; no se ha diseñado como plantilla para una implementación de caché de segundo nivel sólida.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-184">Also, this example demonstrates interceptor concepts; it is not intended as a template for a robust second-level cache implementation.</span></span>

<span data-ttu-id="b1ae3-185">En este ejemplo, la aplicación ejecuta con frecuencia una consulta para obtener el "mensaje diario" más reciente:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-185">In this example, the application frequently executes a query to obtain the most recent "daily message":</span></span>

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

<span data-ttu-id="b1ae3-186">Esta consulta se [etiqueta](xref:core/querying/tags) para que se pueda detectar fácilmente en el interceptor.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-186">This query is [tagged](xref:core/querying/tags) so that it can be easily detected in the interceptor.</span></span> <span data-ttu-id="b1ae3-187">La idea es consultar solo la base de datos de un mensaje nuevo cada día.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-187">The idea is to only query the database for a new message once every day.</span></span> <span data-ttu-id="b1ae3-188">En otras ocasiones, la aplicación usará un resultado almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-188">At other times the application will use a cached result.</span></span> <span data-ttu-id="b1ae3-189">(El ejemplo utiliza el retraso de 10 segundos en el ejemplo para simular un nuevo día).</span><span class="sxs-lookup"><span data-stu-id="b1ae3-189">(The sample uses delay of 10 seconds in the sample to simulate a new day.)</span></span>

#### <a name="interceptor-state"></a><span data-ttu-id="b1ae3-190">Estado del interceptor</span><span class="sxs-lookup"><span data-stu-id="b1ae3-190">Interceptor state</span></span>

<span data-ttu-id="b1ae3-191">Este interceptor es con estado: almacena el identificador y el texto del mensaje del mensaje diario más reciente consultado, más la hora a la que se ejecutó la consulta.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-191">This interceptor is stateful: it stores the ID and message text of the most recent daily message queried, plus the time when that query was executed.</span></span> <span data-ttu-id="b1ae3-192">Debido a este estado, también se necesita un [bloqueo](/dotnet/csharp/language-reference/keywords/lock-statement) , ya que el almacenamiento en caché requiere que varias instancias de contexto utilicen el mismo interceptor.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-192">Because of this state we also need a [lock](/dotnet/csharp/language-reference/keywords/lock-statement) since the caching requires that same interceptor must be used by multiple context instances.</span></span>

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a><span data-ttu-id="b1ae3-193">Antes de la ejecución</span><span class="sxs-lookup"><span data-stu-id="b1ae3-193">Before execution</span></span>

<span data-ttu-id="b1ae3-194">En el `Executing` método (es decir, antes de realizar una llamada a la base de datos), el interceptor detecta la consulta etiquetada y, a continuación, comprueba si hay un resultado almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-194">In the `Executing` method (i.e. before making a database call), the interceptor detects the tagged query and then checks if there is a cached result.</span></span> <span data-ttu-id="b1ae3-195">Si se encuentra un resultado de este tipo, se suprime la consulta y se usan en su lugar los resultados almacenados en caché.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-195">If such a result is found, then the query is suppressed and cached results are used instead.</span></span>

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

<span data-ttu-id="b1ae3-196">Observe cómo el código llama a <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> y pasa un reemplazo <xref:System.Data.Common.DbDataReader> que contiene los datos almacenados en caché.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-196">Notice how the code calls <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> and passes a replacement <xref:System.Data.Common.DbDataReader> containing the cached data.</span></span> <span data-ttu-id="b1ae3-197">A continuación, se devuelve este InterceptionResult, lo que provoca la supresión de la ejecución de la consulta.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-197">This InterceptionResult is then returned, causing suppression of query execution.</span></span> <span data-ttu-id="b1ae3-198">En su lugar, EF Core usa el lector de reemplazo como los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-198">The replacement reader is instead used by EF Core as the results of the query.</span></span>

<span data-ttu-id="b1ae3-199">Este interceptor también manipula el texto del comando.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-199">This interceptor also manipulates the command text.</span></span> <span data-ttu-id="b1ae3-200">Esta manipulación no es necesaria, pero mejora la claridad en los mensajes de registro.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-200">This manipulation is not required, but improves clarity in log messages.</span></span> <span data-ttu-id="b1ae3-201">No es necesario que el texto del comando sea un SQL válido, ya que la consulta no se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-201">The command text does not need to be valid SQL since the query is now not going to be executed.</span></span>

#### <a name="after-execution"></a><span data-ttu-id="b1ae3-202">Después de la ejecución</span><span class="sxs-lookup"><span data-stu-id="b1ae3-202">After execution</span></span>

<span data-ttu-id="b1ae3-203">Si no hay ningún mensaje en caché disponible, o si ha expirado, el código anterior no suprime el resultado.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-203">If no cached message is available, or if it has expired, then the code above does not suppress the result.</span></span> <span data-ttu-id="b1ae3-204">Por tanto, EF Core ejecutará la consulta de la manera habitual.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-204">EF Core will therefore execute the query as normal.</span></span> <span data-ttu-id="b1ae3-205">Después se devolverá al método del interceptor `Executed` después de la ejecución.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-205">It will then return to the interceptor's `Executed` method after execution.</span></span> <span data-ttu-id="b1ae3-206">En este punto, si el resultado no es ya un lector almacenado en memoria caché, el nuevo identificador de mensaje y la cadena se exfieren del lector real y se almacenan en caché listos para el siguiente uso de esta consulta.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-206">At this point if the result is not already a cached reader, then the new message ID and string is exacted from the real reader and cached ready for the next use of this query.</span></span>

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

#### <a name="demonstration"></a><span data-ttu-id="b1ae3-207">Demostración</span><span class="sxs-lookup"><span data-stu-id="b1ae3-207">Demonstration</span></span>

<span data-ttu-id="b1ae3-208">El [ejemplo de interceptor de almacenamiento en caché](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contiene una aplicación de consola simple que consulta los mensajes diarios para probar el almacenamiento en caché:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-208">The [caching interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contains a simple console application that queries for daily messages to test the caching:</span></span>

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

<span data-ttu-id="b1ae3-209">Esta acción devuelve la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-209">This results in the following output:</span></span>

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

<span data-ttu-id="b1ae3-210">Tenga en cuenta la salida del registro de que la aplicación sigue utilizando el mensaje almacenado en caché hasta que expire el tiempo de espera, momento en el que se vuelve a consultar la base de datos para cualquier mensaje nuevo.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-210">Notice from the log output that the application continues to use the cached message until the timeout expires, at which point the database is queried again for any new message.</span></span>

## <a name="savechanges-interception"></a><span data-ttu-id="b1ae3-211">Interceptación de SaveChanges</span><span class="sxs-lookup"><span data-stu-id="b1ae3-211">SaveChanges interception</span></span>

> [!NOTE]
> <span data-ttu-id="b1ae3-212">La intercepción de SaveChanges se presentó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-212">SaveChanges interception was introduced in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="b1ae3-213">Puede [descargar el ejemplo de interceptor de SaveChanges](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) desde github.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-213">You can [download the SaveChanges interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) from GitHub.</span></span>

<span data-ttu-id="b1ae3-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>los puntos de interceptación y se definen mediante el`ISaveChangesInterceptor`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> interception points are defined by the `ISaveChangesInterceptor`</span></span> <!-- Issue #2748 --> <span data-ttu-id="b1ae3-215">interfaz.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-215">interface.</span></span> <span data-ttu-id="b1ae3-216">Como para otros interceptores, el `SaveChangesInterceptor`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-216">As for other interceptors, the `SaveChangesInterceptor`</span></span> <!-- Issue #2748 --> <span data-ttu-id="b1ae3-217">la clase base con métodos no-OP se proporciona por comodidad.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-217">base class with no-op methods is provided as a convenience.</span></span>

> [!TIP]
> <span data-ttu-id="b1ae3-218">Los interceptores son eficaces.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-218">Interceptors are powerful.</span></span> <span data-ttu-id="b1ae3-219">Sin embargo, en muchos casos puede ser más fácil invalidar el método SaveChanges o usar los [eventos .net para SaveChanges](xref:core/logging-events-diagnostics/events) expuesto en DbContext.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-219">However, in many cases it may be easier to override the SaveChanges method or use the [.NET events for SaveChanges](xref:core/logging-events-diagnostics/events) exposed on DbContext.</span></span>

### <a name="example-savechanges-interception-for-auditing"></a><span data-ttu-id="b1ae3-220">Ejemplo: intercepción de SaveChanges para la auditoría</span><span class="sxs-lookup"><span data-stu-id="b1ae3-220">Example: SaveChanges interception for auditing</span></span>

<span data-ttu-id="b1ae3-221">SaveChanges se puede interceptar para crear un registro de auditoría independiente de los cambios realizados.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-221">SaveChanges can be intercepted to create an independent audit record of the changes made.</span></span>

> [!NOTE]
> <span data-ttu-id="b1ae3-222">No pretende ser una solución de auditoría sólida.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-222">This is not intended to be a robust auditing solution.</span></span> <span data-ttu-id="b1ae3-223">En su lugar, es un ejemplo sencillo que se usa para mostrar las características de la interceptación.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-223">Rather it is a simplistic example used to demonstrate the features of interception.</span></span>

#### <a name="the-application-context"></a><span data-ttu-id="b1ae3-224">El contexto de la aplicación</span><span class="sxs-lookup"><span data-stu-id="b1ae3-224">The application context</span></span>

<span data-ttu-id="b1ae3-225">El [ejemplo de auditoría](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) usa un DbContext sencillo con blogs y publicaciones.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-225">The [sample for auditing](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) uses a simple DbContext with blogs and posts.</span></span>

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

<span data-ttu-id="b1ae3-226">Observe que se registra una nueva instancia del interceptor para cada instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-226">Notice that a new instance of the interceptor is registered for each DbContext instance.</span></span> <span data-ttu-id="b1ae3-227">Esto se debe a que el interceptor de auditoría contiene el estado vinculado a la instancia de contexto actual.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-227">This is because the auditing interceptor contains state linked to the current context instance.</span></span>

#### <a name="the-audit-context"></a><span data-ttu-id="b1ae3-228">Contexto de auditoría</span><span class="sxs-lookup"><span data-stu-id="b1ae3-228">The audit context</span></span>

<span data-ttu-id="b1ae3-229">El ejemplo también contiene un segundo DbContext y el modelo que se usa para la base de datos de auditoría.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-229">The sample also contains a second DbContext and model used for the auditing database.</span></span>

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

#### <a name="the-interceptor"></a><span data-ttu-id="b1ae3-230">El interceptor</span><span class="sxs-lookup"><span data-stu-id="b1ae3-230">The interceptor</span></span>

<span data-ttu-id="b1ae3-231">La idea general para la auditoría con el interceptor es:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-231">The general idea for auditing with the interceptor is:</span></span>

* <span data-ttu-id="b1ae3-232">Se crea un mensaje de auditoría al principio de SaveChanges y se escribe en la base de datos de auditoría.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-232">An audit message is created at the beginning of SaveChanges and is written to the auditing database</span></span>
* <span data-ttu-id="b1ae3-233">Se permite que SaveChanges continúe</span><span class="sxs-lookup"><span data-stu-id="b1ae3-233">SaveChanges is allowed to continue</span></span>
* <span data-ttu-id="b1ae3-234">Si SaveChanges se realiza correctamente, se actualiza el mensaje de auditoría para indicar que la operación se ha realizado correctamente.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-234">If SaveChanges succeeds, then the audit message is updated to indicate success</span></span>
* <span data-ttu-id="b1ae3-235">Si se produce un error en SaveChanges, el mensaje de auditoría se actualiza para indicar el error.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-235">If SaveChanges fails, then the audit message is updated to indicate the failure</span></span>

<span data-ttu-id="b1ae3-236">La primera fase se controla antes de que los cambios se envíen a la base de datos mediante invalidaciones de `ISaveChangesInterceptor.SavingChanges`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-236">The first stage is handled before any changes are sent to the database using overrides of `ISaveChangesInterceptor.SavingChanges`</span></span> <!-- Issue #2748 --> <span data-ttu-id="b1ae3-237"> y `ISaveChangesInterceptor.SavingChangesAsync`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-237">and `ISaveChangesInterceptor.SavingChangesAsync`</span></span><!-- Issue #2748 --><span data-ttu-id="b1ae3-238">.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-238">.</span></span>

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

<span data-ttu-id="b1ae3-239">La invalidación de los métodos de sincronización y Async garantiza que la auditoría se realizará independientemente de si se llama a SaveChanges o SaveChangesAsync.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-239">Overriding both sync and async methods ensures that auditing will happen regardless of whether SaveChanges or SaveChangesAsync are called.</span></span> <span data-ttu-id="b1ae3-240">Observe también que la sobrecarga asincrónica es capaz de realizar la e/s asincrónica sin bloqueo en la base de datos de auditoría.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-240">Notice also that the async overload is itself able to perform non-blocking async I/O to the auditing database.</span></span> <span data-ttu-id="b1ae3-241">Puede que desee iniciar el método Sync SavingChanges para asegurarse de que toda la e/s de la base de datos sea asincrónica.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-241">You may wish to throw from the sync SavingChanges method to ensure that all database I/O is async.</span></span> <span data-ttu-id="b1ae3-242">Esto requiere que la aplicación siempre llame a SaveChangesAsync y nunca a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-242">This then requires that the application always calls SaveChangesAsync and never SaveChanges.</span></span>

#### <a name="the-audit-message"></a><span data-ttu-id="b1ae3-243">Mensaje de auditoría</span><span class="sxs-lookup"><span data-stu-id="b1ae3-243">The audit message</span></span>

<span data-ttu-id="b1ae3-244">Cada método de interceptor tiene un `eventData` parámetro que proporciona información contextual sobre el evento que se va a interceptar.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-244">Every interceptor method has an `eventData` parameter providing contextual information about the event being intercepted.</span></span> <span data-ttu-id="b1ae3-245">En este caso, el DbContext de la aplicación actual se incluye en los datos de evento, que se utiliza a continuación para crear un mensaje de auditoría.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-245">In this case the current application DbContext is included in the event data, which is then used to create an audit message.</span></span>

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

<span data-ttu-id="b1ae3-246">El resultado es una `SaveChangesAudit` entidad con una colección de `EntityAudit` entidades, una para cada inserción, actualización o eliminación.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-246">The result is a `SaveChangesAudit` entity with a collection of `EntityAudit` entities, one for each insert, update, or delete.</span></span> <span data-ttu-id="b1ae3-247">A continuación, el interceptor inserta estas entidades en la base de datos de auditoría.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-247">The interceptor then inserts these entities into the audit database.</span></span>

> [!TIP]
> <span data-ttu-id="b1ae3-248">ToString se invalida en cada EF Core clase de datos de evento para generar el mensaje de registro equivalente para el evento.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-248">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="b1ae3-249">Por ejemplo, al llamar a se `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0 inicializado ' BlogsContext ' con el proveedor ' Microsoft. EntityFrameworkCore. SQLite ' con Options: None".</span><span class="sxs-lookup"><span data-stu-id="b1ae3-249">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

#### <a name="detecting-success"></a><span data-ttu-id="b1ae3-250">Detección correcta</span><span class="sxs-lookup"><span data-stu-id="b1ae3-250">Detecting success</span></span>

<span data-ttu-id="b1ae3-251">La entidad Audit se almacena en el interceptor para que se pueda tener acceso a ella de nuevo una vez que SaveChanges se realiza correctamente o se produce un error.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-251">The audit entity is stored on the interceptor so that it can be accessed again once SaveChanges either succeeds or fails.</span></span> <span data-ttu-id="b1ae3-252">Para que se realice correctamente, `ISaveChangesInterceptor.SavedChanges`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-252">For success, `ISaveChangesInterceptor.SavedChanges`</span></span> <!-- Issue #2748 --> <span data-ttu-id="b1ae3-253">o bien `ISaveChangesInterceptor.SavedChangesAsync`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-253">or `ISaveChangesInterceptor.SavedChangesAsync`</span></span> <!-- Issue #2748 --> <span data-ttu-id="b1ae3-254">Se llama a .</span><span class="sxs-lookup"><span data-stu-id="b1ae3-254">is called.</span></span>

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

<span data-ttu-id="b1ae3-255">La entidad de auditoría está asociada al contexto de auditoría, ya que ya existe en la base de datos y debe actualizarse.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-255">The audit entity is attached to the audit context, since it already exists in the database and needs to be updated.</span></span> <span data-ttu-id="b1ae3-256">A continuación, establecemos `Succeeded` y `EndTime` , que marca estas propiedades como modificadas, por lo que SaveChanges enviará una actualización a la base de datos de auditoría.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-256">We then set `Succeeded` and `EndTime`, which marks these properties as modified so SaveChanges will send an update to the audit database.</span></span>

#### <a name="detecting-failure"></a><span data-ttu-id="b1ae3-257">Detección de errores</span><span class="sxs-lookup"><span data-stu-id="b1ae3-257">Detecting failure</span></span>

<span data-ttu-id="b1ae3-258">El error se administra de forma muy similar a como se realiza correctamente, pero en el `ISaveChangesInterceptor.SaveChangesFailed`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-258">Failure is handled in much the same way as success, but in the `ISaveChangesInterceptor.SaveChangesFailed`</span></span> <!-- Issue #2748 --> <span data-ttu-id="b1ae3-259">o bien `ISaveChangesInterceptor.SaveChangesFailedAsync`</span><span class="sxs-lookup"><span data-stu-id="b1ae3-259">or `ISaveChangesInterceptor.SaveChangesFailedAsync`</span></span> <!-- Issue #2748 --> <span data-ttu-id="b1ae3-260">.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-260">method.</span></span> <span data-ttu-id="b1ae3-261">Los datos del evento contienen la excepción que se produjo.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-261">The event data contains the exception that was thrown.</span></span>

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

#### <a name="demonstration"></a><span data-ttu-id="b1ae3-262">Demostración</span><span class="sxs-lookup"><span data-stu-id="b1ae3-262">Demonstration</span></span>

<span data-ttu-id="b1ae3-263">El [ejemplo de auditoría](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contiene una aplicación de consola simple que realiza cambios en la base de datos de blogs y, a continuación, muestra la auditoría que se ha creado.</span><span class="sxs-lookup"><span data-stu-id="b1ae3-263">The [auditing sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contains a simple console application that makes changes to the blogging database and then shows the auditing that was created.</span></span>

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

<span data-ttu-id="b1ae3-264">El resultado muestra el contenido de la base de datos de auditoría:</span><span class="sxs-lookup"><span data-stu-id="b1ae3-264">The result shows the contents of the auditing database:</span></span>

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
