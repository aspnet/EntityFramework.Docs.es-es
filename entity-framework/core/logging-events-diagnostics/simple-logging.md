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
# <a name="simple-logging"></a><span data-ttu-id="c829f-103">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="c829f-103">Simple Logging</span></span>

> [!NOTE]
> <span data-ttu-id="c829f-104">Esta característica se incluyó por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="c829f-104">This feature was introduced in EF Core 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="c829f-105">Puede [descargar el ejemplo de este artículo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Logging/SimpleLogging) en github.</span><span class="sxs-lookup"><span data-stu-id="c829f-105">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Logging/SimpleLogging) from GitHub.</span></span>

<span data-ttu-id="c829f-106">Se puede usar el registro simple de Entity Framework Core (EF Core) para obtener fácilmente los registros durante el desarrollo y la depuración de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="c829f-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="c829f-107">Esta forma de registro requiere una configuración mínima y ningún paquete de NuGet adicional.</span><span class="sxs-lookup"><span data-stu-id="c829f-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="c829f-108">EF Core también se integra con [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging), que requiere más configuración, pero suele ser más adecuado para el registro en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="c829f-108">EF Core also integrates with [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="c829f-109">Configuración</span><span class="sxs-lookup"><span data-stu-id="c829f-109">Configuration</span></span>

<span data-ttu-id="c829f-110">Se puede acceder a los registros de EF Core desde cualquier tipo de aplicación mediante el uso de <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> al [configurar una instancia de DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c829f-110">EF Core logs can be accessed from any type of application through the use of <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="c829f-111">Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c829f-111">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c829f-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-112">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="c829f-113">Como alternativa, `LogTo` se puede llamar a como parte de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o al crear una instancia de que se va a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> pasar al `DbContext` constructor.</span><span class="sxs-lookup"><span data-stu-id="c829f-113">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="c829f-114">La configuración se sigue llamando cuando se usa AddDbContext o se pasa una instancia de DbContextOptions al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="c829f-114">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="c829f-115">Esto hace que sea el lugar ideal para aplicar la configuración de contexto con independencia de cómo se construya el DbContext.</span><span class="sxs-lookup"><span data-stu-id="c829f-115">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="c829f-116">Dirigir los registros</span><span class="sxs-lookup"><span data-stu-id="c829f-116">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="c829f-117">Registro en la consola</span><span class="sxs-lookup"><span data-stu-id="c829f-117">Logging to the console</span></span>

<span data-ttu-id="c829f-118">`LogTo` requiere un <xref:System.Action%601> delegado que acepte una cadena.</span><span class="sxs-lookup"><span data-stu-id="c829f-118">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="c829f-119">EF Core llamará a este delegado con una cadena para cada mensaje de registro generado.</span><span class="sxs-lookup"><span data-stu-id="c829f-119">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="c829f-120">Después, el delegado debe hacer algo con el mensaje especificado.</span><span class="sxs-lookup"><span data-stu-id="c829f-120">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="c829f-121">El <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> método se utiliza a menudo para este delegado, como se muestra anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c829f-121">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="c829f-122">Esto hace que cada mensaje de registro se escriba en la consola.</span><span class="sxs-lookup"><span data-stu-id="c829f-122">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="c829f-123">Registro en la ventana de depuración</span><span class="sxs-lookup"><span data-stu-id="c829f-123">Logging to the debug window</span></span>

<span data-ttu-id="c829f-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> se puede usar para enviar la salida a la ventana de depuración en Visual Studio o en otros IDE.</span><span class="sxs-lookup"><span data-stu-id="c829f-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="c829f-125">En este caso, se debe usar la [Sintaxis lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) porque la `Debug` clase se compila fuera de las compilaciones de versión.</span><span class="sxs-lookup"><span data-stu-id="c829f-125">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="c829f-126">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-126">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="c829f-127">Registro en un archivo</span><span class="sxs-lookup"><span data-stu-id="c829f-127">Logging to a file</span></span>

<span data-ttu-id="c829f-128">Para escribir en un archivo, es necesario crear un <xref:System.IO.StreamWriter> o similar para el archivo.</span><span class="sxs-lookup"><span data-stu-id="c829f-128">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="c829f-129">El <xref:System.IO.StreamWriter.WriteLine%2A> método se puede usar como en los otros ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="c829f-129">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="c829f-130">Recuerde asegurarse de que el archivo se cierra limpiamente eliminando el escritor cuando se desecha el contexto.</span><span class="sxs-lookup"><span data-stu-id="c829f-130">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="c829f-131">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-131">For example:</span></span>

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
> <span data-ttu-id="c829f-132">Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para registrar archivos en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="c829f-132">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="c829f-133">Obtención de mensajes detallados</span><span class="sxs-lookup"><span data-stu-id="c829f-133">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="c829f-134">Información confidencial</span><span class="sxs-lookup"><span data-stu-id="c829f-134">Sensitive data</span></span>

<span data-ttu-id="c829f-135">De forma predeterminada, EF Core no incluirá los valores de los datos en los mensajes de excepción.</span><span class="sxs-lookup"><span data-stu-id="c829f-135">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="c829f-136">Esto se debe a que estos datos pueden ser confidenciales y se pueden revelar en el uso de producción si no se controla una excepción.</span><span class="sxs-lookup"><span data-stu-id="c829f-136">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="c829f-137">Sin embargo, conocer los valores de datos, especialmente para las claves, puede ser muy útil al depurar.</span><span class="sxs-lookup"><span data-stu-id="c829f-137">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="c829f-138">Esto se puede habilitar en EF Core mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="c829f-138">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="c829f-139">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-139">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="c829f-140">Excepciones de consulta detalladas</span><span class="sxs-lookup"><span data-stu-id="c829f-140">Detailed query exceptions</span></span>

<span data-ttu-id="c829f-141">Por motivos de rendimiento, EF Core no encapsula cada llamada para leer un valor del proveedor de base de datos en un bloque try-catch.</span><span class="sxs-lookup"><span data-stu-id="c829f-141">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="c829f-142">Sin embargo, en ocasiones se producen excepciones que son difíciles de diagnosticar, especialmente cuando la base de datos devuelve un valor NULL cuando el modelo no lo permite.</span><span class="sxs-lookup"><span data-stu-id="c829f-142">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="c829f-143">La activación de hará que <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduzca estos bloques try-catch y, por tanto, proporcione errores más detallados.</span><span class="sxs-lookup"><span data-stu-id="c829f-143">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="c829f-144">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-144">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="c829f-145">Filtrado</span><span class="sxs-lookup"><span data-stu-id="c829f-145">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="c829f-146">Niveles de registro</span><span class="sxs-lookup"><span data-stu-id="c829f-146">Log levels</span></span>

<span data-ttu-id="c829f-147">Cada mensaje de registro de EF Core se asigna a un nivel definido por la <xref:Microsoft.Extensions.Logging.LogLevel> enumeración.</span><span class="sxs-lookup"><span data-stu-id="c829f-147">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="c829f-148">De forma predeterminada, EF Core registro simple incluye todos los mensajes en el `Debug` nivel o superior.</span><span class="sxs-lookup"><span data-stu-id="c829f-148">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="c829f-149">`LogTo` se puede pasar un nivel mínimo más alto para filtrar algunos mensajes.</span><span class="sxs-lookup"><span data-stu-id="c829f-149">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="c829f-150">Por ejemplo, si `Information` se pasa el resultado en un conjunto mínimo de registros limitado al acceso a la base de datos y a algunos mensajes de mantenimiento.</span><span class="sxs-lookup"><span data-stu-id="c829f-150">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="c829f-151">Mensajes específicos</span><span class="sxs-lookup"><span data-stu-id="c829f-151">Specific messages</span></span>

<span data-ttu-id="c829f-152">A todos los mensajes de registro se les asigna un <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="c829f-152">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="c829f-153">Se puede tener acceso a estos identificadores desde la <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> clase o la <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> clase para mensajes específicos relacionales.</span><span class="sxs-lookup"><span data-stu-id="c829f-153">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="c829f-154">Un proveedor de bases de datos también puede tener identificadores específicos del proveedor en una clase similar.</span><span class="sxs-lookup"><span data-stu-id="c829f-154">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="c829f-155">Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c829f-155">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="c829f-156">`LogTo` se puede configurar para registrar solo los mensajes asociados a uno o varios identificadores de eventos.</span><span class="sxs-lookup"><span data-stu-id="c829f-156">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="c829f-157">Por ejemplo, para registrar solo los mensajes para el contexto que se está inicializando o eliminando:</span><span class="sxs-lookup"><span data-stu-id="c829f-157">For example, to log only messages for the context being initialized or disposed:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="c829f-158">Categorías de mensajes</span><span class="sxs-lookup"><span data-stu-id="c829f-158">Message categories</span></span>

<span data-ttu-id="c829f-159">Cada mensaje de registro se asigna a una categoría de registrador jerárquico con nombre.</span><span class="sxs-lookup"><span data-stu-id="c829f-159">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="c829f-160">Las categorías son:</span><span class="sxs-lookup"><span data-stu-id="c829f-160">The categories are:</span></span>

| <span data-ttu-id="c829f-161">Category</span><span class="sxs-lookup"><span data-stu-id="c829f-161">Category</span></span>                                             | <span data-ttu-id="c829f-162">error de Hadoop</span><span class="sxs-lookup"><span data-stu-id="c829f-162">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="c829f-163">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="c829f-163">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="c829f-164">Todos los mensajes de EF Core</span><span class="sxs-lookup"><span data-stu-id="c829f-164">All EF Core messages</span></span>
| <span data-ttu-id="c829f-165">Microsoft. EntityFrameworkCore. Database</span><span class="sxs-lookup"><span data-stu-id="c829f-165">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="c829f-166">Todas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-166">All database interactions</span></span>
| <span data-ttu-id="c829f-167">Microsoft. EntityFrameworkCore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="c829f-167">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="c829f-168">Usos de una conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-168">Uses of a database connection</span></span>
| <span data-ttu-id="c829f-169">Microsoft. EntityFrameworkCore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="c829f-169">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="c829f-170">Usos de un comando de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-170">Uses of a database command</span></span>
| <span data-ttu-id="c829f-171">Microsoft. EntityFrameworkCore. Database. Transaction</span><span class="sxs-lookup"><span data-stu-id="c829f-171">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="c829f-172">Usos de una transacción de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-172">Uses of a database transaction</span></span>
| <span data-ttu-id="c829f-173">Microsoft. EntityFrameworkCore. Update</span><span class="sxs-lookup"><span data-stu-id="c829f-173">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="c829f-174">Guardar entidades, excluidas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-174">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="c829f-175">Microsoft. EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="c829f-175">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="c829f-176">Todas las interacciones entre modelos y metadatos</span><span class="sxs-lookup"><span data-stu-id="c829f-176">All model and metadata interactions</span></span>
| <span data-ttu-id="c829f-177">Microsoft. EntityFrameworkCore. Model. Validation</span><span class="sxs-lookup"><span data-stu-id="c829f-177">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="c829f-178">Validación de modelos</span><span class="sxs-lookup"><span data-stu-id="c829f-178">Model validation</span></span>
| <span data-ttu-id="c829f-179">Microsoft. EntityFrameworkCore. Query</span><span class="sxs-lookup"><span data-stu-id="c829f-179">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="c829f-180">Consultas, excluidas las interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-180">Queries, excluding database interactions</span></span>
| <span data-ttu-id="c829f-181">Microsoft. EntityFrameworkCore. Infrastructure</span><span class="sxs-lookup"><span data-stu-id="c829f-181">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="c829f-182">Eventos generales, como la creación de contexto</span><span class="sxs-lookup"><span data-stu-id="c829f-182">General events, such as context creation</span></span>
| <span data-ttu-id="c829f-183">Microsoft. EntityFrameworkCore. scaffolding</span><span class="sxs-lookup"><span data-stu-id="c829f-183">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="c829f-184">Ingeniería inversa de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-184">Database reverse engineering</span></span>
| <span data-ttu-id="c829f-185">Microsoft. EntityFrameworkCore. Migrations</span><span class="sxs-lookup"><span data-stu-id="c829f-185">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="c829f-186">Migraciones</span><span class="sxs-lookup"><span data-stu-id="c829f-186">Migrations</span></span>
| <span data-ttu-id="c829f-187">Microsoft. EntityFrameworkCore. ChangeTracking</span><span class="sxs-lookup"><span data-stu-id="c829f-187">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="c829f-188">Interacciones de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="c829f-188">Change tracking interactions</span></span>

<span data-ttu-id="c829f-189">`LogTo` se puede configurar para registrar solo los mensajes de una o varias categorías.</span><span class="sxs-lookup"><span data-stu-id="c829f-189">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="c829f-190">Por ejemplo, para registrar solo interacciones de base de datos:</span><span class="sxs-lookup"><span data-stu-id="c829f-190">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="c829f-191">Tenga en cuenta que la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> clase proporciona una API jerárquica para buscar una categoría y evita la necesidad de codificar cadenas de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="c829f-191">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="c829f-192">Puesto que las categorías son jerárquicas, este ejemplo con la `Database` categoría incluirá todos los mensajes para las subcategorías `Database.Connection` , `Database.Command` y `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="c829f-192">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="c829f-193">Filtros personalizados</span><span class="sxs-lookup"><span data-stu-id="c829f-193">Custom filters</span></span>

<span data-ttu-id="c829f-194">`LogTo` permite usar un filtro personalizado para los casos en los que ninguna de las opciones de filtrado anteriores sean suficientes.</span><span class="sxs-lookup"><span data-stu-id="c829f-194">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="c829f-195">Por ejemplo, para registrar cualquier mensaje en `Information` el nivel o superior, así como mensajes para abrir y cerrar una conexión:</span><span class="sxs-lookup"><span data-stu-id="c829f-195">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

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
> <span data-ttu-id="c829f-196">El filtrado mediante filtros personalizados o el uso de cualquiera de las otras opciones que se muestran aquí es más eficaz que el filtrado del `LogTo` delegado.</span><span class="sxs-lookup"><span data-stu-id="c829f-196">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the `LogTo` delegate.</span></span> <span data-ttu-id="c829f-197">Esto se debe a que, si el filtro determina que el mensaje no se debe registrar, no se crea ni siquiera el mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="c829f-197">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="c829f-198">Configuración de mensajes específicos</span><span class="sxs-lookup"><span data-stu-id="c829f-198">Configuration for specific messages</span></span>

<span data-ttu-id="c829f-199">La API de EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> permite a las aplicaciones cambiar lo que ocurre cuando se encuentra un evento específico.</span><span class="sxs-lookup"><span data-stu-id="c829f-199">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="c829f-200">Se puede usar para:</span><span class="sxs-lookup"><span data-stu-id="c829f-200">This can be used to:</span></span>

* <span data-ttu-id="c829f-201">Cambiar el nivel de registro en el que se registra el evento</span><span class="sxs-lookup"><span data-stu-id="c829f-201">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="c829f-202">Omitir el registro del evento por completo</span><span class="sxs-lookup"><span data-stu-id="c829f-202">Skip logging the event altogether</span></span>
* <span data-ttu-id="c829f-203">Producir una excepción cuando se produce el evento</span><span class="sxs-lookup"><span data-stu-id="c829f-203">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="c829f-204">Cambiar el nivel de registro de un evento</span><span class="sxs-lookup"><span data-stu-id="c829f-204">Changing the log level for an event</span></span>

<span data-ttu-id="c829f-205">En el ejemplo anterior se usaba un filtro personalizado para registrar todos `LogLevel.Information` los mensajes en, así como dos eventos definidos para `LogLevel.Debug` .</span><span class="sxs-lookup"><span data-stu-id="c829f-205">The previous example used a custom filter to log every message at `LogLevel.Information` as well as two events defined for `LogLevel.Debug`.</span></span> <span data-ttu-id="c829f-206">Lo mismo se puede lograr cambiando el nivel de registro de los dos `Debug` eventos a `Information` :</span><span class="sxs-lookup"><span data-stu-id="c829f-206">The same can be achieved by changing the log level of the two `Debug` events to `Information`:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="c829f-207">Suprimir el registro de un evento</span><span class="sxs-lookup"><span data-stu-id="c829f-207">Suppress logging an event</span></span>

<span data-ttu-id="c829f-208">De forma similar, se puede suprimir un evento individual del registro.</span><span class="sxs-lookup"><span data-stu-id="c829f-208">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="c829f-209">Esto es especialmente útil para omitir una advertencia que se ha revisado y comprendido.</span><span class="sxs-lookup"><span data-stu-id="c829f-209">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="c829f-210">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-210">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="c829f-211">Throw para un evento</span><span class="sxs-lookup"><span data-stu-id="c829f-211">Throw for an event</span></span>

<span data-ttu-id="c829f-212">Por último, EF Core se pueden configurar para que se inicien para un evento determinado.</span><span class="sxs-lookup"><span data-stu-id="c829f-212">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="c829f-213">Esto es especialmente útil para cambiar una advertencia a un error.</span><span class="sxs-lookup"><span data-stu-id="c829f-213">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="c829f-214">(De hecho, este era el propósito original del `ConfigureWarnings` método, es decir, el nombre). Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-214">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a><span data-ttu-id="c829f-215">Contenido y formato del mensaje</span><span class="sxs-lookup"><span data-stu-id="c829f-215">Message contents and formatting</span></span>

<span data-ttu-id="c829f-216">El contenido predeterminado de `LogTo` se formatea en varias líneas.</span><span class="sxs-lookup"><span data-stu-id="c829f-216">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="c829f-217">La primera línea contiene metadatos de mensaje:</span><span class="sxs-lookup"><span data-stu-id="c829f-217">The first line contains message metadata:</span></span>

* <span data-ttu-id="c829f-218"><xref:Microsoft.Extensions.Logging.LogLevel>Como prefijo de cuatro caracteres</span><span class="sxs-lookup"><span data-stu-id="c829f-218">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="c829f-219">Marca de tiempo local, con formato para la referencia cultural actual</span><span class="sxs-lookup"><span data-stu-id="c829f-219">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="c829f-220"><xref:Microsoft.Extensions.Logging.EventId>En el formulario que se puede copiar y pegar para obtener el miembro de <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> o una de las otras `EventId` clases, más el valor de identificador sin formato</span><span class="sxs-lookup"><span data-stu-id="c829f-220">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="c829f-221">La categoría de eventos, tal y como se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c829f-221">The event category, as described above.</span></span>

<span data-ttu-id="c829f-222">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-222">For example:</span></span>

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

<span data-ttu-id="c829f-223">Este contenido se puede personalizar pasando valores de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> , como se muestra en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="c829f-223">This content can be customized by passing values from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions>, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="c829f-224">Considere la posibilidad de usar [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) para obtener más control sobre el formato del registro.</span><span class="sxs-lookup"><span data-stu-id="c829f-224">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="c829f-225">Usar la hora UTC</span><span class="sxs-lookup"><span data-stu-id="c829f-225">Using UTC time</span></span>

<span data-ttu-id="c829f-226">De forma predeterminada, las marcas de tiempo están diseñadas para el consumo local durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="c829f-226">By default, timestamps are designed for local consumption while debugging.</span></span> <span data-ttu-id="c829f-227">Use <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> para usar marcas de tiempo UTC independientes de la referencia cultural en su lugar, pero mantenga todo lo demás.</span><span class="sxs-lookup"><span data-stu-id="c829f-227">Use <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="c829f-228">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-228">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="c829f-229">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="c829f-229">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="c829f-230">Registro de una sola línea</span><span class="sxs-lookup"><span data-stu-id="c829f-230">Single line logging</span></span>

<span data-ttu-id="c829f-231">A veces resulta útil obtener exactamente una línea por cada mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="c829f-231">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="c829f-232">Esto puede habilitarse mediante <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c829f-232">This can be enabled by <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c829f-233">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-233">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="c829f-234">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="c829f-234">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="c829f-235">Otras opciones de contenido</span><span class="sxs-lookup"><span data-stu-id="c829f-235">Other content options</span></span>

<span data-ttu-id="c829f-236">Otras marcas de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> se pueden usar para reducir la cantidad de metadatos que se incluyen en el registro.</span><span class="sxs-lookup"><span data-stu-id="c829f-236">Other flags in <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="c829f-237">Esto puede ser útil junto con el registro de una sola línea.</span><span class="sxs-lookup"><span data-stu-id="c829f-237">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="c829f-238">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-238">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

<span data-ttu-id="c829f-239">En este ejemplo se da como resultado el siguiente formato de registro:</span><span class="sxs-lookup"><span data-stu-id="c829f-239">This example results in the following log formatting:</span></span>

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="c829f-240">Pasar de EF6</span><span class="sxs-lookup"><span data-stu-id="c829f-240">Moving from EF6</span></span>

<span data-ttu-id="c829f-241">EF Core registro simple difiere de <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6 en dos aspectos importantes:</span><span class="sxs-lookup"><span data-stu-id="c829f-241">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="c829f-242">Los mensajes de registro no se limitan únicamente a interacciones de base de datos</span><span class="sxs-lookup"><span data-stu-id="c829f-242">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="c829f-243">El registro se debe configurar en el momento de inicialización del contexto.</span><span class="sxs-lookup"><span data-stu-id="c829f-243">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="c829f-244">En la primera diferencia, el filtrado descrito anteriormente se puede usar para limitar los mensajes que se registran.</span><span class="sxs-lookup"><span data-stu-id="c829f-244">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="c829f-245">La segunda diferencia es un cambio intencionado para mejorar el rendimiento, ya que no genera mensajes de registro cuando no es necesario.</span><span class="sxs-lookup"><span data-stu-id="c829f-245">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="c829f-246">Sin embargo, todavía es posible obtener un comportamiento similar a EF6 mediante la creación de una `Log` propiedad en `DbContext` y, a continuación, usarla solo cuando se haya establecido.</span><span class="sxs-lookup"><span data-stu-id="c829f-246">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="c829f-247">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c829f-247">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
