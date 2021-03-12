---
title: 'Uso de agentes de escucha de diagnóstico: EF Core'
description: Uso de DiagnosticListener para el consumo global de diagnósticos de EF Core
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: 7035acaa6d306e73a0a2c071532ece0d8e9a0a1d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024256"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a><span data-ttu-id="0dff6-103">Uso de agentes de escucha de diagnóstico en EF Core</span><span class="sxs-lookup"><span data-stu-id="0dff6-103">Using Diagnostic Listeners in EF Core</span></span>

> [!TIP]
> <span data-ttu-id="0dff6-104">Puede [descargar el ejemplo de este artículo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) en github.</span><span class="sxs-lookup"><span data-stu-id="0dff6-104">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) from GitHub.</span></span>

<span data-ttu-id="0dff6-105">Las escuchas de diagnóstico permiten escuchar cualquier evento de EF Core que se produzca en el proceso de .NET actual.</span><span class="sxs-lookup"><span data-stu-id="0dff6-105">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span> <span data-ttu-id="0dff6-106">La <xref:System.Diagnostics.DiagnosticListener> clase forma parte de un [mecanismo común en .net](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para obtener información de diagnóstico de aplicaciones en ejecución.</span><span class="sxs-lookup"><span data-stu-id="0dff6-106">The <xref:System.Diagnostics.DiagnosticListener> class is a part of a [common mechanism across .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) for obtaining diagnostic information from running applications.</span></span>

<span data-ttu-id="0dff6-107">Las escuchas de diagnóstico no son adecuadas para obtener eventos de una sola instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="0dff6-107">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="0dff6-108">Los [interceptores](xref:core/logging-events-diagnostics/interceptors) de EF Core proporcionan acceso a los mismos eventos con registro por contexto.</span><span class="sxs-lookup"><span data-stu-id="0dff6-108">EF Core [interceptors](xref:core/logging-events-diagnostics/interceptors) provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="0dff6-109">Las escuchas de diagnóstico no están diseñadas para el registro.</span><span class="sxs-lookup"><span data-stu-id="0dff6-109">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="0dff6-110">Considere la posibilidad de usar un [registro simple](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) para el registro.</span><span class="sxs-lookup"><span data-stu-id="0dff6-110">Consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) for logging.</span></span>

## <a name="example-observing-diagnostic-events"></a><span data-ttu-id="0dff6-111">Ejemplo: observación de eventos de diagnóstico</span><span class="sxs-lookup"><span data-stu-id="0dff6-111">Example: Observing diagnostic events</span></span>

<span data-ttu-id="0dff6-112">La resolución de eventos EF Core es un proceso de dos pasos.</span><span class="sxs-lookup"><span data-stu-id="0dff6-112">Resolving EF Core events is a two-step process.</span></span> <span data-ttu-id="0dff6-113">En primer lugar [](/dotnet/standard/events/observer-design-pattern) , `DiagnosticListener` se debe crear un observador para sí mismo:</span><span class="sxs-lookup"><span data-stu-id="0dff6-113">First, an [observer](/dotnet/standard/events/observer-design-pattern) for `DiagnosticListener` itself must be created:</span></span>

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

<span data-ttu-id="0dff6-114">El `OnNext` método busca el DiagnosticListener que procede de EF Core.</span><span class="sxs-lookup"><span data-stu-id="0dff6-114">The `OnNext` method looks for the DiagnosticListener that comes from EF Core.</span></span> <span data-ttu-id="0dff6-115">Este agente de escucha tiene el nombre "Microsoft. EntityFrameworkCore", que se puede obtener de la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> clase tal y como se muestra.</span><span class="sxs-lookup"><span data-stu-id="0dff6-115">This listener has the name "Microsoft.EntityFrameworkCore", which can be obtained from the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class as shown.</span></span>

<span data-ttu-id="0dff6-116">Este observador debe registrarse en todo el mundo, por ejemplo, en el método de la aplicación `Main` :</span><span class="sxs-lookup"><span data-stu-id="0dff6-116">This observer must then be registered globally, for example in the application's `Main` method:</span></span>

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

<span data-ttu-id="0dff6-117">En segundo lugar, una vez que se encuentra el EF Core DiagnosticListener, se crea un nuevo observador de clave-valor para suscribirse a los eventos de EF Core reales.</span><span class="sxs-lookup"><span data-stu-id="0dff6-117">Second, once the EF Core DiagnosticListener is found, a new key-value observer is created to subscribe to the actual EF Core events.</span></span> <span data-ttu-id="0dff6-118">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0dff6-118">For example:</span></span>

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

<span data-ttu-id="0dff6-119">El `OnNext` método es esta hora llamada con un par clave-valor para cada evento de EF Core.</span><span class="sxs-lookup"><span data-stu-id="0dff6-119">The `OnNext` method is this time called with a key/value pair for each EF Core event.</span></span> <span data-ttu-id="0dff6-120">La clave es el nombre del evento, que se puede obtener de uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="0dff6-120">The key is the name of the event, which can be obtained from one of:</span></span>

* <span data-ttu-id="0dff6-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> para eventos comunes a todos los proveedores de bases de datos de EF Core</span><span class="sxs-lookup"><span data-stu-id="0dff6-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="0dff6-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> para eventos comunes a todos los proveedores de bases de datos relacionales</span><span class="sxs-lookup"><span data-stu-id="0dff6-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="0dff6-123">Clase similar para los eventos específicos del proveedor de base de datos actual.</span><span class="sxs-lookup"><span data-stu-id="0dff6-123">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="0dff6-124">Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0dff6-124">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="0dff6-125">El valor del par clave-valor es un tipo de carga específico para el evento.</span><span class="sxs-lookup"><span data-stu-id="0dff6-125">The value of the key/value pair is a payload type specific to the event.</span></span> <span data-ttu-id="0dff6-126">El tipo de carga que se espera se documenta en cada evento definido en estas clases de eventos.</span><span class="sxs-lookup"><span data-stu-id="0dff6-126">The type of payload to expect is documented on each event defined in these event classes.</span></span>

<span data-ttu-id="0dff6-127">Por ejemplo, el código anterior controla los <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> eventos y <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> .</span><span class="sxs-lookup"><span data-stu-id="0dff6-127">For example, the code above handles the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> and the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> events.</span></span> <span data-ttu-id="0dff6-128">Para el primero de ellos, la carga es <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> .</span><span class="sxs-lookup"><span data-stu-id="0dff6-128">For the first of these, the payload is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData>.</span></span> <span data-ttu-id="0dff6-129">En el segundo caso, es <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .</span><span class="sxs-lookup"><span data-stu-id="0dff6-129">For the second, it is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData>.</span></span>

> [!TIP]
> <span data-ttu-id="0dff6-130">ToString se invalida en cada EF Core clase de datos de evento para generar el mensaje de registro equivalente para el evento.</span><span class="sxs-lookup"><span data-stu-id="0dff6-130">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="0dff6-131">Por ejemplo, al llamar a se `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0 inicializado ' BlogsContext ' con el proveedor ' Microsoft. EntityFrameworkCore. SQLite ' con Options: None".</span><span class="sxs-lookup"><span data-stu-id="0dff6-131">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

<span data-ttu-id="0dff6-132">El [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) contiene una aplicación de consola simple que realiza cambios en la base de datos de blogs e imprime los eventos de diagnóstico encontrados.</span><span class="sxs-lookup"><span data-stu-id="0dff6-132">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) contains a simple console application that makes changes to the blogging database and prints out the diagnostic events encountered.</span></span>

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion

        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

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

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

<span data-ttu-id="0dff6-133">La salida de este código muestra los eventos detectados:</span><span class="sxs-lookup"><span data-stu-id="0dff6-133">The output from this code shows the events detected:</span></span>

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
