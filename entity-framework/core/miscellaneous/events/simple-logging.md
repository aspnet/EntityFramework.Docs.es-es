---
title: EF Core de registro simples
description: Registro desde un DbContext de EFCore con LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/miscellaneous/events/simple-logging
ms.openlocfilehash: 2d671dd9daad16a6431b91419736ec67733e41b3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066567"
---
# <a name="simple-logging"></a><span data-ttu-id="344e2-103">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="344e2-103">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="344e2-104">Esta característica se agregó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="344e2-104">This feature was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="344e2-105">Puede [ver y descargar el ejemplo de este artículo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) en github.</span><span class="sxs-lookup"><span data-stu-id="344e2-105">You can [view and download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) on GitHub.</span></span>

<span data-ttu-id="344e2-106">Se puede usar el registro simple de Entity Framework Core (EF Core) para obtener fácilmente los registros durante el desarrollo y la depuración de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="344e2-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="344e2-107">Esta forma de registro requiere una configuración mínima y ningún paquete de NuGet adicional.</span><span class="sxs-lookup"><span data-stu-id="344e2-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="344e2-108">EF Core también se integra con [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging), que requiere más configuración, pero suele ser más adecuado para el registro en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="344e2-108">EF Core also integrates with [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="344e2-109">Configuración</span><span class="sxs-lookup"><span data-stu-id="344e2-109">Configuration</span></span>

<span data-ttu-id="344e2-110">Se puede tener acceso a EF Core registros desde cualquier tipo de aplicación mediante el uso de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="344e2-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="344e2-111">al [configurar una instancia de DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="344e2-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="344e2-112">Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="344e2-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="344e2-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="344e2-114">Como alternativa, `LogTo` se puede llamar a como parte de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o al crear una instancia de que se va a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> pasar al `DbContext` constructor.</span><span class="sxs-lookup"><span data-stu-id="344e2-114">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="344e2-115">La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="344e2-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="344e2-116">Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.</span><span class="sxs-lookup"><span data-stu-id="344e2-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="344e2-117">Dirigir los registros</span><span class="sxs-lookup"><span data-stu-id="344e2-117">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="344e2-118">Registro en la consola</span><span class="sxs-lookup"><span data-stu-id="344e2-118">Logging to the console</span></span>

<span data-ttu-id="344e2-119">`LogTo` requiere un <xref:System.Action%601> delegado que acepte una cadena.</span><span class="sxs-lookup"><span data-stu-id="344e2-119">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="344e2-120">EF Core llamará a este delegado con una cadena para cada mensaje de registro generado.</span><span class="sxs-lookup"><span data-stu-id="344e2-120">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="344e2-121">Después, el delegado debe hacer algo con el mensaje especificado.</span><span class="sxs-lookup"><span data-stu-id="344e2-121">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="344e2-122">El <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> método se utiliza a menudo para este delegado, como se muestra anteriormente.</span><span class="sxs-lookup"><span data-stu-id="344e2-122">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="344e2-123">Esto hace que cada mensaje de registro se escriba en la consola.</span><span class="sxs-lookup"><span data-stu-id="344e2-123">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="344e2-124">Registro en la ventana de depuración</span><span class="sxs-lookup"><span data-stu-id="344e2-124">Logging to the debug window</span></span>

<span data-ttu-id="344e2-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> se puede usar para enviar la salida a la ventana de depuración en Visual Studio o en otros IDE.</span><span class="sxs-lookup"><span data-stu-id="344e2-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="344e2-126">En este caso, se debe usar la [Sintaxis lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) porque la `Debug` clase se compila fuera de las compilaciones de versión.</span><span class="sxs-lookup"><span data-stu-id="344e2-126">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="344e2-127">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-127">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="344e2-128">Registro en un archivo</span><span class="sxs-lookup"><span data-stu-id="344e2-128">Logging to a file</span></span>

<span data-ttu-id="344e2-129">Para escribir en un archivo, es necesario crear un <xref:System.IO.StreamWriter> o similar para el archivo.</span><span class="sxs-lookup"><span data-stu-id="344e2-129">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="344e2-130">El <xref:System.IO.StreamWriter.WriteLine%2A> método se puede usar como en los otros ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="344e2-130">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="344e2-131">Recuerde asegurarse de que el archivo se cierra limpiamente eliminando el escritor cuando se desecha el contexto.</span><span class="sxs-lookup"><span data-stu-id="344e2-131">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="344e2-132">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-132">For example:</span></span>

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
[!code-csharp[LogToFile](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="344e2-133">Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para registrar archivos en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="344e2-133">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="344e2-134">Obtención de mensajes detallados</span><span class="sxs-lookup"><span data-stu-id="344e2-134">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="344e2-135">Información confidencial</span><span class="sxs-lookup"><span data-stu-id="344e2-135">Sensitive data</span></span>

<span data-ttu-id="344e2-136">De forma predeterminada, EF Core no incluirá los valores de los datos en los mensajes de excepción.</span><span class="sxs-lookup"><span data-stu-id="344e2-136">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="344e2-137">Esto se debe a que estos datos pueden ser confidenciales y se pueden revelar en el uso de producción si no se controla una excepción.</span><span class="sxs-lookup"><span data-stu-id="344e2-137">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="344e2-138">Sin embargo, conocer los valores de datos, especialmente para las claves, puede ser muy útil al depurar.</span><span class="sxs-lookup"><span data-stu-id="344e2-138">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="344e2-139">Esto se puede habilitar en EF Core mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="344e2-139">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="344e2-140">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-140">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="344e2-141">Excepciones de consulta detalladas</span><span class="sxs-lookup"><span data-stu-id="344e2-141">Detailed query exceptions</span></span>

<span data-ttu-id="344e2-142">Por motivos de rendimiento, EF Core no encapsula cada llamada para leer un valor del proveedor de base de datos en un bloque try-catch.</span><span class="sxs-lookup"><span data-stu-id="344e2-142">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="344e2-143">Sin embargo, en ocasiones se producen excepciones que son difíciles de diagnosticar, especialmente cuando la base de datos devuelve un valor NULL cuando el modelo no lo permite.</span><span class="sxs-lookup"><span data-stu-id="344e2-143">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="344e2-144">La activación de hará que <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduzca estos bloques try-catch y, por tanto, proporcione errores más detallados.</span><span class="sxs-lookup"><span data-stu-id="344e2-144">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="344e2-145">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-145">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="344e2-146">Filtros</span><span class="sxs-lookup"><span data-stu-id="344e2-146">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="344e2-147">Niveles de registro</span><span class="sxs-lookup"><span data-stu-id="344e2-147">Log levels</span></span>

<span data-ttu-id="344e2-148">Cada mensaje de registro de EF Core se asigna a un nivel definido por la <xref:Microsoft.Extensions.Logging.LogLevel> enumeración.</span><span class="sxs-lookup"><span data-stu-id="344e2-148">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="344e2-149">De forma predeterminada, EF Core registro simple incluye todos los mensajes en el `Debug` nivel o superior.</span><span class="sxs-lookup"><span data-stu-id="344e2-149">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="344e2-150">`LogTo` se puede pasar un nivel mínimo más alto para filtrar algunos mensajes.</span><span class="sxs-lookup"><span data-stu-id="344e2-150">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="344e2-151">Por ejemplo, si `Information` se pasa el resultado en un conjunto mínimo de registros limitado al acceso a la base de datos y a algunos mensajes de mantenimiento.</span><span class="sxs-lookup"><span data-stu-id="344e2-151">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="344e2-152">Mensajes específicos</span><span class="sxs-lookup"><span data-stu-id="344e2-152">Specific messages</span></span>

<span data-ttu-id="344e2-153">A todos los mensajes de registro se les asigna un <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="344e2-153">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="344e2-154">Se puede tener acceso a estos identificadores desde la <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> clase o la <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> clase para mensajes específicos relacionales.</span><span class="sxs-lookup"><span data-stu-id="344e2-154">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="344e2-155">Un proveedor de bases de datos también puede tener identificadores específicos del proveedor en una clase similar.</span><span class="sxs-lookup"><span data-stu-id="344e2-155">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="344e2-156">Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="344e2-156">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="344e2-157">`LogTo` se puede configurar para registrar solo los mensajes asociados a uno o varios identificadores de eventos.</span><span class="sxs-lookup"><span data-stu-id="344e2-157">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="344e2-158">Por ejemplo, para registrar solo los mensajes para el contexto que se está inicializando o eliminando:</span><span class="sxs-lookup"><span data-stu-id="344e2-158">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="344e2-159">Categorías de mensajes</span><span class="sxs-lookup"><span data-stu-id="344e2-159">Message categories</span></span>

<span data-ttu-id="344e2-160">Cada mensaje de registro se asigna a una categoría de registrador jerárquico con nombre.</span><span class="sxs-lookup"><span data-stu-id="344e2-160">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="344e2-161">Las categorías son:</span><span class="sxs-lookup"><span data-stu-id="344e2-161">The categories are:</span></span>

| <span data-ttu-id="344e2-162">Category</span><span class="sxs-lookup"><span data-stu-id="344e2-162">Category</span></span>                                             | <span data-ttu-id="344e2-163">Mensajes</span><span class="sxs-lookup"><span data-stu-id="344e2-163">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="344e2-164">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="344e2-164">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="344e2-165">Todos los mensajes de EF Core</span><span class="sxs-lookup"><span data-stu-id="344e2-165">All EF Core messages</span></span>
| <span data-ttu-id="344e2-166">Microsoft. EntityFrameworkCore. Database</span><span class="sxs-lookup"><span data-stu-id="344e2-166">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="344e2-167">Todas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-167">All database interactions</span></span>
| <span data-ttu-id="344e2-168">Microsoft. EntityFrameworkCore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="344e2-168">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="344e2-169">Usos de una conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-169">Uses of a database connection</span></span>
| <span data-ttu-id="344e2-170">Microsoft. EntityFrameworkCore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="344e2-170">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="344e2-171">Usos de un comando de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-171">Uses of a database command</span></span>
| <span data-ttu-id="344e2-172">Microsoft. EntityFrameworkCore. Database. Transaction</span><span class="sxs-lookup"><span data-stu-id="344e2-172">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="344e2-173">Usos de una transacción de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-173">Uses of a database transaction</span></span>
| <span data-ttu-id="344e2-174">Microsoft. EntityFrameworkCore. Update</span><span class="sxs-lookup"><span data-stu-id="344e2-174">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="344e2-175">Guardar entidades, excluidas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-175">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="344e2-176">Microsoft. EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="344e2-176">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="344e2-177">Todas las interacciones entre modelos y metadatos</span><span class="sxs-lookup"><span data-stu-id="344e2-177">All model and metadata interactions</span></span>
| <span data-ttu-id="344e2-178">Microsoft. EntityFrameworkCore. Model. Validation</span><span class="sxs-lookup"><span data-stu-id="344e2-178">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="344e2-179">Validación de modelos</span><span class="sxs-lookup"><span data-stu-id="344e2-179">Model validation</span></span>
| <span data-ttu-id="344e2-180">Microsoft. EntityFrameworkCore. Query</span><span class="sxs-lookup"><span data-stu-id="344e2-180">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="344e2-181">Consultas, excluidas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-181">Queries, excluding database interactions</span></span>
| <span data-ttu-id="344e2-182">Microsoft. EntityFrameworkCore. Infrastructure</span><span class="sxs-lookup"><span data-stu-id="344e2-182">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="344e2-183">Eventos generales, como la creación de contexto</span><span class="sxs-lookup"><span data-stu-id="344e2-183">General events, such as context creation</span></span>
| <span data-ttu-id="344e2-184">Microsoft. EntityFrameworkCore. scaffolding</span><span class="sxs-lookup"><span data-stu-id="344e2-184">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="344e2-185">Ingeniería inversa de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-185">Database reverse engineering</span></span>
| <span data-ttu-id="344e2-186">Microsoft. EntityFrameworkCore. Migrations</span><span class="sxs-lookup"><span data-stu-id="344e2-186">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="344e2-187">Migraciones</span><span class="sxs-lookup"><span data-stu-id="344e2-187">Migrations</span></span>
| <span data-ttu-id="344e2-188">Microsoft. EntityFrameworkCore. ChangeTracking</span><span class="sxs-lookup"><span data-stu-id="344e2-188">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="344e2-189">Interacciones de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="344e2-189">Change tracking interactions</span></span>

<span data-ttu-id="344e2-190">`LogTo` se puede configurar para registrar solo los mensajes de una o varias categorías.</span><span class="sxs-lookup"><span data-stu-id="344e2-190">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="344e2-191">Por ejemplo, para registrar solo interacciones de base de datos:</span><span class="sxs-lookup"><span data-stu-id="344e2-191">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="344e2-192">Tenga en cuenta que la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> clase proporciona una API jerárquica para buscar una categoría y evita la necesidad de codificar cadenas de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="344e2-192">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="344e2-193">Puesto que las categorías son jerárquicas, este ejemplo con la `Database` categoría incluirá todos los mensajes para las subcategorías `Database.Connection` , `Database.Command` y `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="344e2-193">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="344e2-194">Filtros personalizados</span><span class="sxs-lookup"><span data-stu-id="344e2-194">Custom filters</span></span>

<span data-ttu-id="344e2-195">`LogTo` permite usar un filtro personalizado para los casos en los que ninguna de las opciones de filtrado anteriores sean suficientes.</span><span class="sxs-lookup"><span data-stu-id="344e2-195">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="344e2-196">Por ejemplo, para registrar cualquier mensaje en `Information` el nivel o superior, así como mensajes para abrir y cerrar una conexión:</span><span class="sxs-lookup"><span data-stu-id="344e2-196">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="344e2-197">El filtrado mediante filtros personalizados o el uso de cualquiera de las otras opciones que se muestran aquí es más eficaz que el filtrado en el delegado de LogTo.</span><span class="sxs-lookup"><span data-stu-id="344e2-197">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the LogTo delegate.</span></span> <span data-ttu-id="344e2-198">Esto se debe a que, si el filtro determina que el mensaje no se debe registrar, no se crea ni siquiera el mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="344e2-198">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="message-contents-and-formatting"></a><span data-ttu-id="344e2-199">Contenido y formato del mensaje</span><span class="sxs-lookup"><span data-stu-id="344e2-199">Message contents and formatting</span></span>

<span data-ttu-id="344e2-200">El contenido predeterminado de `LogTo` se formatea en varias líneas.</span><span class="sxs-lookup"><span data-stu-id="344e2-200">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="344e2-201">La primera línea contiene metadatos de mensaje:</span><span class="sxs-lookup"><span data-stu-id="344e2-201">The first line contains message metadata:</span></span>

* <span data-ttu-id="344e2-202"><xref:Microsoft.Extensions.Logging.LogLevel>Como prefijo de cuatro caracteres</span><span class="sxs-lookup"><span data-stu-id="344e2-202">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="344e2-203">Marca de tiempo local, con formato para la referencia cultural actual</span><span class="sxs-lookup"><span data-stu-id="344e2-203">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="344e2-204"><xref:Microsoft.Extensions.Logging.EventId>En el formulario que se puede copiar y pegar para obtener el miembro de <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> o una de las otras `EventId` clases, más el valor de identificador sin formato</span><span class="sxs-lookup"><span data-stu-id="344e2-204">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="344e2-205">La categoría de eventos, tal y como se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="344e2-205">The event category, as described above.</span></span>

<span data-ttu-id="344e2-206">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-206">For example:</span></span>

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

<span data-ttu-id="344e2-207">Este contenido se puede personalizar pasando valores de [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="344e2-207">This content can be customized by passing values from [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --><span data-ttu-id="344e2-208">, como se muestra en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="344e2-208">, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="344e2-209">Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para obtener más control sobre el formato del registro.</span><span class="sxs-lookup"><span data-stu-id="344e2-209">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="344e2-210">Usar la hora UTC</span><span class="sxs-lookup"><span data-stu-id="344e2-210">Using UTC time</span></span>

<span data-ttu-id="344e2-211">De forma predeterminada, los timestamnps están diseñados para el consumo local durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="344e2-211">By default, timestamnps are designed for local consumption while debugging.</span></span> <span data-ttu-id="344e2-212">Use `DbContextLoggerOptions.DefaultWithUtcTime` para usar marcas de tiempo UTC independientes de la referencia cultural en su lugar, pero mantenga todo lo demás.</span><span class="sxs-lookup"><span data-stu-id="344e2-212">Use `DbContextLoggerOptions.DefaultWithUtcTime` to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="344e2-213">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-213">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="344e2-214">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="344e2-214">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="344e2-215">Registro de una sola línea</span><span class="sxs-lookup"><span data-stu-id="344e2-215">Single line logging</span></span>

<span data-ttu-id="344e2-216">A veces resulta útil obtener exactamente una línea por cada mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="344e2-216">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="344e2-217">Esto puede habilitarse mediante `DbContextLoggerOptions.SingleLine` .</span><span class="sxs-lookup"><span data-stu-id="344e2-217">This can be enabled by `DbContextLoggerOptions.SingleLine`.</span></span> <span data-ttu-id="344e2-218">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-218">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="344e2-219">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="344e2-219">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="344e2-220">Otras opciones de contenido</span><span class="sxs-lookup"><span data-stu-id="344e2-220">Other content options</span></span>

<span data-ttu-id="344e2-221">Otras marcas en [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="344e2-221">Other flags in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> <span data-ttu-id="344e2-222">se puede usar para reducir la cantidad de metadatos que se incluyen en el registro.</span><span class="sxs-lookup"><span data-stu-id="344e2-222">can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="344e2-223">Esto puede ser útil junto con el registro de una sola línea.</span><span class="sxs-lookup"><span data-stu-id="344e2-223">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="344e2-224">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-224">For example:</span></span>

<span data-ttu-id="344e2-225">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="344e2-225">This example results in the following log formatting:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="344e2-226">Pasar de EF6</span><span class="sxs-lookup"><span data-stu-id="344e2-226">Moving from EF6</span></span>

<span data-ttu-id="344e2-227">EF Core registro simple difiere de <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6 en dos aspectos importantes:</span><span class="sxs-lookup"><span data-stu-id="344e2-227">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="344e2-228">Los mensajes de registro no se limitan únicamente a interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="344e2-228">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="344e2-229">El registro se debe configurar en el momento de inicialización del contexto.</span><span class="sxs-lookup"><span data-stu-id="344e2-229">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="344e2-230">En la primera diferencia, el filtrado descrito anteriormente se puede usar para limitar los mensajes que se registran.</span><span class="sxs-lookup"><span data-stu-id="344e2-230">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="344e2-231">La segunda diferencia es un cambio intencionado para mejorar el rendimiento, ya que no genera mensajes de registro cuando no es necesario.</span><span class="sxs-lookup"><span data-stu-id="344e2-231">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="344e2-232">Sin embargo, todavía es posible obtener un comportamiento similar a EF6 mediante la creación de una `Log` propiedad en `DbContext` y, a continuación, usarla solo cuando se haya establecido.</span><span class="sxs-lookup"><span data-stu-id="344e2-232">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="344e2-233">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="344e2-233">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
