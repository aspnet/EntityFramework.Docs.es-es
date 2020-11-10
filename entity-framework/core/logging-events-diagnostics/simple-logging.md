---
title: EF Core de registro simples
description: Registro desde un EF Core DbContext mediante LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 49619cc10ea098e39e71dde347e00bbc3c39b13a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431342"
---
# <a name="simple-logging"></a><span data-ttu-id="efed4-103">Registro simple</span><span class="sxs-lookup"><span data-stu-id="efed4-103">Simple Logging</span></span>

> [!NOTE]
> <span data-ttu-id="efed4-104">Esta característica se agregó en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="efed4-104">This feature was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="efed4-105">Puede [descargar el ejemplo de este artículo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) en github.</span><span class="sxs-lookup"><span data-stu-id="efed4-105">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) from GitHub.</span></span>

<span data-ttu-id="efed4-106">Se puede usar el registro simple de Entity Framework Core (EF Core) para obtener fácilmente los registros durante el desarrollo y la depuración de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="efed4-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="efed4-107">Esta forma de registro requiere una configuración mínima y ningún paquete de NuGet adicional.</span><span class="sxs-lookup"><span data-stu-id="efed4-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="efed4-108">EF Core también se integra con [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging), que requiere más configuración, pero suele ser más adecuado para el registro en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="efed4-108">EF Core also integrates with [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="efed4-109">Configuración</span><span class="sxs-lookup"><span data-stu-id="efed4-109">Configuration</span></span>

<span data-ttu-id="efed4-110">Se puede tener acceso a EF Core registros desde cualquier tipo de aplicación mediante el uso de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="efed4-110">EF Core logs can be accessed from any type of application through the use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="efed4-111">al [configurar una instancia de DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="efed4-111">when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="efed4-112">Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="efed4-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="efed4-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="efed4-114">Como alternativa, `LogTo` se puede llamar a como parte de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o al crear una instancia de que se va a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> pasar al `DbContext` constructor.</span><span class="sxs-lookup"><span data-stu-id="efed4-114">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="efed4-115">La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="efed4-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="efed4-116">Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.</span><span class="sxs-lookup"><span data-stu-id="efed4-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="efed4-117">Dirigir los registros</span><span class="sxs-lookup"><span data-stu-id="efed4-117">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="efed4-118">Registro en la consola</span><span class="sxs-lookup"><span data-stu-id="efed4-118">Logging to the console</span></span>

<span data-ttu-id="efed4-119">`LogTo` requiere un <xref:System.Action%601> delegado que acepte una cadena.</span><span class="sxs-lookup"><span data-stu-id="efed4-119">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="efed4-120">EF Core llamará a este delegado con una cadena para cada mensaje de registro generado.</span><span class="sxs-lookup"><span data-stu-id="efed4-120">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="efed4-121">Después, el delegado debe hacer algo con el mensaje especificado.</span><span class="sxs-lookup"><span data-stu-id="efed4-121">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="efed4-122">El <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> método se utiliza a menudo para este delegado, como se muestra anteriormente.</span><span class="sxs-lookup"><span data-stu-id="efed4-122">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="efed4-123">Esto hace que cada mensaje de registro se escriba en la consola.</span><span class="sxs-lookup"><span data-stu-id="efed4-123">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="efed4-124">Registro en la ventana de depuración</span><span class="sxs-lookup"><span data-stu-id="efed4-124">Logging to the debug window</span></span>

<span data-ttu-id="efed4-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> se puede usar para enviar la salida a la ventana de depuración en Visual Studio o en otros IDE.</span><span class="sxs-lookup"><span data-stu-id="efed4-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="efed4-126">En este caso, se debe usar la [Sintaxis lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) porque la `Debug` clase se compila fuera de las compilaciones de versión.</span><span class="sxs-lookup"><span data-stu-id="efed4-126">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="efed4-127">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-127">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="efed4-128">Registro en un archivo</span><span class="sxs-lookup"><span data-stu-id="efed4-128">Logging to a file</span></span>

<span data-ttu-id="efed4-129">Para escribir en un archivo, es necesario crear un <xref:System.IO.StreamWriter> o similar para el archivo.</span><span class="sxs-lookup"><span data-stu-id="efed4-129">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="efed4-130">El <xref:System.IO.StreamWriter.WriteLine%2A> método se puede usar como en los otros ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="efed4-130">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="efed4-131">Recuerde asegurarse de que el archivo se cierra limpiamente eliminando el escritor cuando se desecha el contexto.</span><span class="sxs-lookup"><span data-stu-id="efed4-131">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="efed4-132">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-132">For example:</span></span>

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
> <span data-ttu-id="efed4-133">Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para registrar archivos en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="efed4-133">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="efed4-134">Obtención de mensajes detallados</span><span class="sxs-lookup"><span data-stu-id="efed4-134">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="efed4-135">Información confidencial</span><span class="sxs-lookup"><span data-stu-id="efed4-135">Sensitive data</span></span>

<span data-ttu-id="efed4-136">De forma predeterminada, EF Core no incluirá los valores de los datos en los mensajes de excepción.</span><span class="sxs-lookup"><span data-stu-id="efed4-136">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="efed4-137">Esto se debe a que estos datos pueden ser confidenciales y se pueden revelar en el uso de producción si no se controla una excepción.</span><span class="sxs-lookup"><span data-stu-id="efed4-137">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="efed4-138">Sin embargo, conocer los valores de datos, especialmente para las claves, puede ser muy útil al depurar.</span><span class="sxs-lookup"><span data-stu-id="efed4-138">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="efed4-139">Esto se puede habilitar en EF Core mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="efed4-139">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="efed4-140">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-140">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="efed4-141">Excepciones de consulta detalladas</span><span class="sxs-lookup"><span data-stu-id="efed4-141">Detailed query exceptions</span></span>

<span data-ttu-id="efed4-142">Por motivos de rendimiento, EF Core no encapsula cada llamada para leer un valor del proveedor de base de datos en un bloque try-catch.</span><span class="sxs-lookup"><span data-stu-id="efed4-142">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="efed4-143">Sin embargo, en ocasiones se producen excepciones que son difíciles de diagnosticar, especialmente cuando la base de datos devuelve un valor NULL cuando el modelo no lo permite.</span><span class="sxs-lookup"><span data-stu-id="efed4-143">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="efed4-144">La activación de hará que <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduzca estos bloques try-catch y, por tanto, proporcione errores más detallados.</span><span class="sxs-lookup"><span data-stu-id="efed4-144">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="efed4-145">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-145">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="efed4-146">Filtrado</span><span class="sxs-lookup"><span data-stu-id="efed4-146">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="efed4-147">Niveles de registro</span><span class="sxs-lookup"><span data-stu-id="efed4-147">Log levels</span></span>

<span data-ttu-id="efed4-148">Cada mensaje de registro de EF Core se asigna a un nivel definido por la <xref:Microsoft.Extensions.Logging.LogLevel> enumeración.</span><span class="sxs-lookup"><span data-stu-id="efed4-148">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="efed4-149">De forma predeterminada, EF Core registro simple incluye todos los mensajes en el `Debug` nivel o superior.</span><span class="sxs-lookup"><span data-stu-id="efed4-149">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="efed4-150">`LogTo` se puede pasar un nivel mínimo más alto para filtrar algunos mensajes.</span><span class="sxs-lookup"><span data-stu-id="efed4-150">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="efed4-151">Por ejemplo, si `Information` se pasa el resultado en un conjunto mínimo de registros limitado al acceso a la base de datos y a algunos mensajes de mantenimiento.</span><span class="sxs-lookup"><span data-stu-id="efed4-151">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="efed4-152">Mensajes específicos</span><span class="sxs-lookup"><span data-stu-id="efed4-152">Specific messages</span></span>

<span data-ttu-id="efed4-153">A todos los mensajes de registro se les asigna un <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="efed4-153">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="efed4-154">Se puede tener acceso a estos identificadores desde la <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> clase o la <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> clase para mensajes específicos relacionales.</span><span class="sxs-lookup"><span data-stu-id="efed4-154">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="efed4-155">Un proveedor de bases de datos también puede tener identificadores específicos del proveedor en una clase similar.</span><span class="sxs-lookup"><span data-stu-id="efed4-155">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="efed4-156">Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="efed4-156">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="efed4-157">`LogTo` se puede configurar para registrar solo los mensajes asociados a uno o varios identificadores de eventos.</span><span class="sxs-lookup"><span data-stu-id="efed4-157">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="efed4-158">Por ejemplo, para registrar solo los mensajes para el contexto que se está inicializando o eliminando:</span><span class="sxs-lookup"><span data-stu-id="efed4-158">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="efed4-159">Categorías de mensajes</span><span class="sxs-lookup"><span data-stu-id="efed4-159">Message categories</span></span>

<span data-ttu-id="efed4-160">Cada mensaje de registro se asigna a una categoría de registrador jerárquico con nombre.</span><span class="sxs-lookup"><span data-stu-id="efed4-160">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="efed4-161">Las categorías son:</span><span class="sxs-lookup"><span data-stu-id="efed4-161">The categories are:</span></span>

| <span data-ttu-id="efed4-162">Category</span><span class="sxs-lookup"><span data-stu-id="efed4-162">Category</span></span>                                             | <span data-ttu-id="efed4-163">error de Hadoop</span><span class="sxs-lookup"><span data-stu-id="efed4-163">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="efed4-164">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="efed4-164">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="efed4-165">Todos los mensajes de EF Core</span><span class="sxs-lookup"><span data-stu-id="efed4-165">All EF Core messages</span></span>
| <span data-ttu-id="efed4-166">Microsoft. EntityFrameworkCore. Database</span><span class="sxs-lookup"><span data-stu-id="efed4-166">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="efed4-167">Todas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-167">All database interactions</span></span>
| <span data-ttu-id="efed4-168">Microsoft. EntityFrameworkCore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="efed4-168">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="efed4-169">Usos de una conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-169">Uses of a database connection</span></span>
| <span data-ttu-id="efed4-170">Microsoft. EntityFrameworkCore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="efed4-170">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="efed4-171">Usos de un comando de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-171">Uses of a database command</span></span>
| <span data-ttu-id="efed4-172">Microsoft. EntityFrameworkCore. Database. Transaction</span><span class="sxs-lookup"><span data-stu-id="efed4-172">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="efed4-173">Usos de una transacción de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-173">Uses of a database transaction</span></span>
| <span data-ttu-id="efed4-174">Microsoft. EntityFrameworkCore. Update</span><span class="sxs-lookup"><span data-stu-id="efed4-174">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="efed4-175">Guardar entidades, excluidas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-175">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="efed4-176">Microsoft. EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="efed4-176">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="efed4-177">Todas las interacciones entre modelos y metadatos</span><span class="sxs-lookup"><span data-stu-id="efed4-177">All model and metadata interactions</span></span>
| <span data-ttu-id="efed4-178">Microsoft. EntityFrameworkCore. Model. Validation</span><span class="sxs-lookup"><span data-stu-id="efed4-178">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="efed4-179">Validación de modelos</span><span class="sxs-lookup"><span data-stu-id="efed4-179">Model validation</span></span>
| <span data-ttu-id="efed4-180">Microsoft. EntityFrameworkCore. Query</span><span class="sxs-lookup"><span data-stu-id="efed4-180">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="efed4-181">Consultas, excluidas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-181">Queries, excluding database interactions</span></span>
| <span data-ttu-id="efed4-182">Microsoft. EntityFrameworkCore. Infrastructure</span><span class="sxs-lookup"><span data-stu-id="efed4-182">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="efed4-183">Eventos generales, como la creación de contexto</span><span class="sxs-lookup"><span data-stu-id="efed4-183">General events, such as context creation</span></span>
| <span data-ttu-id="efed4-184">Microsoft. EntityFrameworkCore. scaffolding</span><span class="sxs-lookup"><span data-stu-id="efed4-184">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="efed4-185">Ingeniería inversa de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-185">Database reverse engineering</span></span>
| <span data-ttu-id="efed4-186">Microsoft. EntityFrameworkCore. Migrations</span><span class="sxs-lookup"><span data-stu-id="efed4-186">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="efed4-187">Migraciones</span><span class="sxs-lookup"><span data-stu-id="efed4-187">Migrations</span></span>
| <span data-ttu-id="efed4-188">Microsoft. EntityFrameworkCore. ChangeTracking</span><span class="sxs-lookup"><span data-stu-id="efed4-188">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="efed4-189">Interacciones de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="efed4-189">Change tracking interactions</span></span>

<span data-ttu-id="efed4-190">`LogTo` se puede configurar para registrar solo los mensajes de una o varias categorías.</span><span class="sxs-lookup"><span data-stu-id="efed4-190">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="efed4-191">Por ejemplo, para registrar solo interacciones de base de datos:</span><span class="sxs-lookup"><span data-stu-id="efed4-191">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="efed4-192">Tenga en cuenta que la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> clase proporciona una API jerárquica para buscar una categoría y evita la necesidad de codificar cadenas de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="efed4-192">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="efed4-193">Puesto que las categorías son jerárquicas, este ejemplo con la `Database` categoría incluirá todos los mensajes para las subcategorías `Database.Connection` , `Database.Command` y `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="efed4-193">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="efed4-194">Filtros personalizados</span><span class="sxs-lookup"><span data-stu-id="efed4-194">Custom filters</span></span>

<span data-ttu-id="efed4-195">`LogTo` permite usar un filtro personalizado para los casos en los que ninguna de las opciones de filtrado anteriores sean suficientes.</span><span class="sxs-lookup"><span data-stu-id="efed4-195">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="efed4-196">Por ejemplo, para registrar cualquier mensaje en `Information` el nivel o superior, así como mensajes para abrir y cerrar una conexión:</span><span class="sxs-lookup"><span data-stu-id="efed4-196">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

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
> <span data-ttu-id="efed4-197">El filtrado mediante filtros personalizados o el uso de cualquiera de las otras opciones que se muestran aquí es más eficaz que el filtrado en el delegado de LogTo.</span><span class="sxs-lookup"><span data-stu-id="efed4-197">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the LogTo delegate.</span></span> <span data-ttu-id="efed4-198">Esto se debe a que, si el filtro determina que el mensaje no se debe registrar, no se crea ni siquiera el mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="efed4-198">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="efed4-199">Configuración de mensajes específicos</span><span class="sxs-lookup"><span data-stu-id="efed4-199">Configuration for specific messages</span></span>

<span data-ttu-id="efed4-200">La API de EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> permite a las aplicaciones cambiar lo que ocurre cuando se encuentra un evento específico.</span><span class="sxs-lookup"><span data-stu-id="efed4-200">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="efed4-201">Se puede usar para:</span><span class="sxs-lookup"><span data-stu-id="efed4-201">This can be used to:</span></span>

* <span data-ttu-id="efed4-202">Cambiar el nivel de registro en el que se registra el evento</span><span class="sxs-lookup"><span data-stu-id="efed4-202">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="efed4-203">Omitir el registro del evento por completo</span><span class="sxs-lookup"><span data-stu-id="efed4-203">Skip logging the event altogether</span></span>
* <span data-ttu-id="efed4-204">Producir una excepción cuando se produce el evento</span><span class="sxs-lookup"><span data-stu-id="efed4-204">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="efed4-205">Cambiar el nivel de registro de un evento</span><span class="sxs-lookup"><span data-stu-id="efed4-205">Changing the log level for an event</span></span>

<span data-ttu-id="efed4-206">En el ejemplo anterior se usaba un filtro personalizado para registrar todos `LogLevel.Information` los mensajes en, así como dos eventos definidos para `LogLevel.Debug` .</span><span class="sxs-lookup"><span data-stu-id="efed4-206">The previous example used a custom filter to log every message at `LogLevel.Information` as well as two events defined for `LogLevel.Debug`.</span></span> <span data-ttu-id="efed4-207">Lo mismo se puede lograr cambiando el nivel de registro de los dos `Debug` eventos a `Information` :</span><span class="sxs-lookup"><span data-stu-id="efed4-207">The same can be achieved by changing the log level of the two `Debug` events to `Information`:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="efed4-208">Suprimir el registro de un evento</span><span class="sxs-lookup"><span data-stu-id="efed4-208">Suppress logging an event</span></span>

<span data-ttu-id="efed4-209">De forma similar, se puede suprimir un evento individual del registro.</span><span class="sxs-lookup"><span data-stu-id="efed4-209">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="efed4-210">Esto es especialmente útil para omitir una advertencia que se ha revisado y comprendido.</span><span class="sxs-lookup"><span data-stu-id="efed4-210">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="efed4-211">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-211">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="efed4-212">Throw para un evento</span><span class="sxs-lookup"><span data-stu-id="efed4-212">Throw for an event</span></span>

<span data-ttu-id="efed4-213">Por último, EF Core se pueden configurar para que se inicien para un evento determinado.</span><span class="sxs-lookup"><span data-stu-id="efed4-213">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="efed4-214">Esto es especialmente útil para cambiar una advertencia a un error.</span><span class="sxs-lookup"><span data-stu-id="efed4-214">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="efed4-215">(De hecho, este era el propósito original del `ConfigureWarnings` método, es decir, el nombre). Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-215">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a><span data-ttu-id="efed4-216">Contenido y formato del mensaje</span><span class="sxs-lookup"><span data-stu-id="efed4-216">Message contents and formatting</span></span>

<span data-ttu-id="efed4-217">El contenido predeterminado de `LogTo` se formatea en varias líneas.</span><span class="sxs-lookup"><span data-stu-id="efed4-217">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="efed4-218">La primera línea contiene metadatos de mensaje:</span><span class="sxs-lookup"><span data-stu-id="efed4-218">The first line contains message metadata:</span></span>

* <span data-ttu-id="efed4-219"><xref:Microsoft.Extensions.Logging.LogLevel>Como prefijo de cuatro caracteres</span><span class="sxs-lookup"><span data-stu-id="efed4-219">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="efed4-220">Marca de tiempo local, con formato para la referencia cultural actual</span><span class="sxs-lookup"><span data-stu-id="efed4-220">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="efed4-221"><xref:Microsoft.Extensions.Logging.EventId>En el formulario que se puede copiar y pegar para obtener el miembro de <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> o una de las otras `EventId` clases, más el valor de identificador sin formato</span><span class="sxs-lookup"><span data-stu-id="efed4-221">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="efed4-222">La categoría de eventos, tal y como se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="efed4-222">The event category, as described above.</span></span>

<span data-ttu-id="efed4-223">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-223">For example:</span></span>

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

<span data-ttu-id="efed4-224">Este contenido se puede personalizar pasando valores de [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="efed4-224">This content can be customized by passing values from [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --><span data-ttu-id="efed4-225">, como se muestra en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="efed4-225">, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="efed4-226">Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para obtener más control sobre el formato del registro.</span><span class="sxs-lookup"><span data-stu-id="efed4-226">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="efed4-227">Usar la hora UTC</span><span class="sxs-lookup"><span data-stu-id="efed4-227">Using UTC time</span></span>

<span data-ttu-id="efed4-228">De forma predeterminada, los timestamnps están diseñados para el consumo local durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="efed4-228">By default, timestamnps are designed for local consumption while debugging.</span></span> <span data-ttu-id="efed4-229">Use `DbContextLoggerOptions.DefaultWithUtcTime` para usar marcas de tiempo UTC independientes de la referencia cultural en su lugar, pero mantenga todo lo demás.</span><span class="sxs-lookup"><span data-stu-id="efed4-229">Use `DbContextLoggerOptions.DefaultWithUtcTime` to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="efed4-230">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-230">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="efed4-231">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="efed4-231">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="efed4-232">Registro de una sola línea</span><span class="sxs-lookup"><span data-stu-id="efed4-232">Single line logging</span></span>

<span data-ttu-id="efed4-233">A veces resulta útil obtener exactamente una línea por cada mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="efed4-233">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="efed4-234">Esto puede habilitarse mediante `DbContextLoggerOptions.SingleLine` .</span><span class="sxs-lookup"><span data-stu-id="efed4-234">This can be enabled by `DbContextLoggerOptions.SingleLine`.</span></span> <span data-ttu-id="efed4-235">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-235">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="efed4-236">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="efed4-236">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="efed4-237">Otras opciones de contenido</span><span class="sxs-lookup"><span data-stu-id="efed4-237">Other content options</span></span>

<span data-ttu-id="efed4-238">Otras marcas en [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="efed4-238">Other flags in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> <span data-ttu-id="efed4-239">se puede usar para reducir la cantidad de metadatos que se incluyen en el registro.</span><span class="sxs-lookup"><span data-stu-id="efed4-239">can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="efed4-240">Esto puede ser útil junto con el registro de una sola línea.</span><span class="sxs-lookup"><span data-stu-id="efed4-240">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="efed4-241">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-241">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

<span data-ttu-id="efed4-242">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="efed4-242">This example results in the following log formatting:</span></span>

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="efed4-243">Pasar de EF6</span><span class="sxs-lookup"><span data-stu-id="efed4-243">Moving from EF6</span></span>

<span data-ttu-id="efed4-244">EF Core registro simple difiere de <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6 en dos aspectos importantes:</span><span class="sxs-lookup"><span data-stu-id="efed4-244">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="efed4-245">Los mensajes de registro no se limitan únicamente a interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="efed4-245">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="efed4-246">El registro se debe configurar en el momento de inicialización del contexto.</span><span class="sxs-lookup"><span data-stu-id="efed4-246">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="efed4-247">En la primera diferencia, el filtrado descrito anteriormente se puede usar para limitar los mensajes que se registran.</span><span class="sxs-lookup"><span data-stu-id="efed4-247">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="efed4-248">La segunda diferencia es un cambio intencionado para mejorar el rendimiento, ya que no genera mensajes de registro cuando no es necesario.</span><span class="sxs-lookup"><span data-stu-id="efed4-248">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="efed4-249">Sin embargo, todavía es posible obtener un comportamiento similar a EF6 mediante la creación de una `Log` propiedad en `DbContext` y, a continuación, usarla solo cuando se haya establecido.</span><span class="sxs-lookup"><span data-stu-id="efed4-249">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="efed4-250">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="efed4-250">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
