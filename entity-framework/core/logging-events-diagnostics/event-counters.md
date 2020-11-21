---
title: 'Contadores de eventos: EF Core'
description: Seguimiento EF Core rendimiento y diagnóstico de anomalías con los contadores de eventos de .NET
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 46acfe82d8aeb7d16146bae0cc2cd4ff733e2831
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003694"
---
# <a name="event-counters"></a><span data-ttu-id="4340b-103">Contadores de eventos</span><span class="sxs-lookup"><span data-stu-id="4340b-103">Event Counters</span></span>

> [!NOTE]
> <span data-ttu-id="4340b-104">Esta característica se agregó en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="4340b-104">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="4340b-105">Entity Framework Core (EF Core) expone métricas numéricas continuas que pueden proporcionar una buena indicación del estado del programa.</span><span class="sxs-lookup"><span data-stu-id="4340b-105">Entity Framework Core (EF Core) exposes continuous numeric metrics which can provide a good indication of your program's health.</span></span> <span data-ttu-id="4340b-106">Estas métricas se pueden usar para los siguientes fines:</span><span class="sxs-lookup"><span data-stu-id="4340b-106">These metrics can be used for the following purposes:</span></span>

* <span data-ttu-id="4340b-107">Seguimiento de la carga de base de datos general en tiempo real cuando se ejecuta la aplicación</span><span class="sxs-lookup"><span data-stu-id="4340b-107">Track general database load in realtime as the application is running</span></span>
* <span data-ttu-id="4340b-108">Exponga prácticas de codificación problemáticas que puedan provocar un rendimiento degradado</span><span class="sxs-lookup"><span data-stu-id="4340b-108">Expose problematic coding practices which can lead to degraded performance</span></span>
* <span data-ttu-id="4340b-109">Seguimiento y aislamiento del comportamiento anómalo del programa</span><span class="sxs-lookup"><span data-stu-id="4340b-109">Track down and isolate anomalous program behavior</span></span>

<span data-ttu-id="4340b-110">EF Core informa de las métricas a través de la característica de contadores de eventos estándar de .NET; se recomienda leer [esta entrada de blog](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) para obtener una visión general rápida de cómo funcionan los contadores.</span><span class="sxs-lookup"><span data-stu-id="4340b-110">EF Core reports metrics via the standard .NET event counters feature; it's recommended to read [this blog post](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) for a quick overview of how counters work.</span></span>

## <a name="attach-to-a-process-using-dotnet-counters"></a><span data-ttu-id="4340b-111">Asociar a un proceso mediante dotnet-counters</span><span class="sxs-lookup"><span data-stu-id="4340b-111">Attach to a process using dotnet-counters</span></span>

<span data-ttu-id="4340b-112">La [herramienta dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) se puede utilizar para asociar a un proceso en ejecución y notificar EF Core los contadores de eventos con regularidad. no es necesario hacer nada especial en el programa para que estos contadores estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="4340b-112">The [dotnet-counters tool](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) can be used to attach to a running process and report EF Core event counters regularly; nothing special needs to be done in the program for these counters to be available.</span></span>

<span data-ttu-id="4340b-113">En primer lugar, instale la `dotnet-counters` herramienta: `dotnet tool install --global dotnet-counters` .</span><span class="sxs-lookup"><span data-stu-id="4340b-113">First, install the `dotnet-counters` tool: `dotnet tool install --global dotnet-counters`.</span></span>

<span data-ttu-id="4340b-114">A continuación, busque el ID. de proceso (PID) del proceso .NET que ejecuta la aplicación EF Core:</span><span class="sxs-lookup"><span data-stu-id="4340b-114">Next, find the process ID (PID) of the .NET process running your EF Core application:</span></span>

### <a name="windows"></a>[<span data-ttu-id="4340b-115">Windows</span><span class="sxs-lookup"><span data-stu-id="4340b-115">Windows</span></span>](#tab/windows)

1. <span data-ttu-id="4340b-116">Abra el administrador de tareas de Windows; para ello, haga clic con el botón secundario en la barra de tareas y seleccione "Administrador de tareas".</span><span class="sxs-lookup"><span data-stu-id="4340b-116">Open the Windows Task Manager by right-clicking on the task bar and selecting "Task Manager".</span></span>
2. <span data-ttu-id="4340b-117">Asegúrese de que la opción "más detalles" esté seleccionada en la parte inferior de la ventana.</span><span class="sxs-lookup"><span data-stu-id="4340b-117">Make sure that the "More details" option is selected at the bottom of the window.</span></span>
3. <span data-ttu-id="4340b-118">En la pestaña procesos, haga clic con el botón secundario en una columna y asegúrese de que la columna PID esté habilitada.</span><span class="sxs-lookup"><span data-stu-id="4340b-118">In the Processes tab, right-click a column and make sure that the PID column is enabled.</span></span>
4. <span data-ttu-id="4340b-119">Busque la aplicación en la lista de procesos y obtenga su identificador de proceso de la columna PID.</span><span class="sxs-lookup"><span data-stu-id="4340b-119">Locate your application in the process list, and get its process ID from the PID column.</span></span>

### <a name="linux-or-macos"></a>[<span data-ttu-id="4340b-120">Linux o macOS</span><span class="sxs-lookup"><span data-stu-id="4340b-120">Linux or macOS</span></span>](#tab/fluent-api)

1. <span data-ttu-id="4340b-121">Use el `ps` comando para enumerar todos los procesos que se ejecutan para el usuario.</span><span class="sxs-lookup"><span data-stu-id="4340b-121">Use the `ps` command to list all processes running for your user.</span></span>
2. <span data-ttu-id="4340b-122">Busque la aplicación en la lista de procesos y obtenga su identificador de proceso de la columna PID.</span><span class="sxs-lookup"><span data-stu-id="4340b-122">Locate your application in the process list, and get its process ID from the PID column.</span></span>

***

<span data-ttu-id="4340b-123">Dentro de la aplicación .NET, el ID. de proceso está disponible como `Process.GetCurrentProcess().Id` ; esto puede ser útil para imprimir el PID al iniciarse.</span><span class="sxs-lookup"><span data-stu-id="4340b-123">Inside your .NET application, the process ID is available as `Process.GetCurrentProcess().Id`; this can be useful for printing the PID upon startup.</span></span>

<span data-ttu-id="4340b-124">Por último, inicie `dotnet-counters` como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="4340b-124">Finally, launch `dotnet-counters` as follows:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

<span data-ttu-id="4340b-125">`dotnet-counters` ahora se asociará al proceso en ejecución y comenzará a notificar datos de contador continuos:</span><span class="sxs-lookup"><span data-stu-id="4340b-125">`dotnet-counters` will now attach to your running process and start reporting continuous counter data:</span></span>

```console
Press p to pause, r to resume, q to quit.
 Status: Running

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                         1
    Queries (Total)                                               189
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             0
```

## <a name="counters-and-their-meaning"></a><span data-ttu-id="4340b-126">Contadores y su significado</span><span class="sxs-lookup"><span data-stu-id="4340b-126">Counters and their meaning</span></span>

<span data-ttu-id="4340b-127">Nombre del contador</span><span class="sxs-lookup"><span data-stu-id="4340b-127">Counter name</span></span>                          | <span data-ttu-id="4340b-128">Descripción</span><span class="sxs-lookup"><span data-stu-id="4340b-128">Description</span></span>
------------------------------------- | ----
<span data-ttu-id="4340b-129">Active DbContexts</span><span class="sxs-lookup"><span data-stu-id="4340b-129">Active DbContexts</span></span>                     | <span data-ttu-id="4340b-130">El número de instancias de DbContext activas y no desechadas actualmente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4340b-130">The number of active, undisposed DbContext instances currently in your application.</span></span> <span data-ttu-id="4340b-131">Si este número crece continuamente, puede tener una fuga porque las instancias de DbContext no se desechan correctamente.</span><span class="sxs-lookup"><span data-stu-id="4340b-131">If this number grows continuously, you may have a leak because DbContext instances aren't being properly disposed.</span></span> <span data-ttu-id="4340b-132">Tenga en cuenta que si está habilitada la [agrupación de contexto](xref:core/miscellaneous/context-pooling) , este número incluye instancias de DbContext agrupadas que no se usan actualmente.</span><span class="sxs-lookup"><span data-stu-id="4340b-132">Note that if [context pooling](xref:core/miscellaneous/context-pooling) is enabled, this number includes pooled DbContext instances not currently in use.</span></span>
<span data-ttu-id="4340b-133">Errores de operación de la estrategia de ejecución</span><span class="sxs-lookup"><span data-stu-id="4340b-133">Execution Strategy Operation Failures</span></span> | <span data-ttu-id="4340b-134">Número de veces que una operación de base de datos no se pudo ejecutar.</span><span class="sxs-lookup"><span data-stu-id="4340b-134">The number of times a database operation failed to execute.</span></span> <span data-ttu-id="4340b-135">Si se habilita una estrategia de ejecución de reintento, esto incluye cada error individual en varios intentos en la misma operación.</span><span class="sxs-lookup"><span data-stu-id="4340b-135">If a retrying execution strategy is enabled, this includes each individual failure within multiple attempts on the same operation.</span></span> <span data-ttu-id="4340b-136">Se puede usar para detectar problemas transitorios con la infraestructura.</span><span class="sxs-lookup"><span data-stu-id="4340b-136">This can be used to detect transient issues with your infrastructure.</span></span>
<span data-ttu-id="4340b-137">Errores de simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="4340b-137">Optimistic Concurrency Failures</span></span>       | <span data-ttu-id="4340b-138">Número de veces `SaveChanges` que se produjo un error de simultaneidad optimista porque los datos del almacén de datos se cambiaron desde que el código lo cargó.</span><span class="sxs-lookup"><span data-stu-id="4340b-138">The number of times `SaveChanges` failed because of an optimistic concurrency error, because data in the data store was changed since your code loaded it.</span></span> <span data-ttu-id="4340b-139">Esto corresponde a un <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> que se está iniciando.</span><span class="sxs-lookup"><span data-stu-id="4340b-139">This corresponds to a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> being thrown.</span></span>
<span data-ttu-id="4340b-140">Consultas</span><span class="sxs-lookup"><span data-stu-id="4340b-140">Queries</span></span>                               | <span data-ttu-id="4340b-141">El número de consultas ejecutadas.</span><span class="sxs-lookup"><span data-stu-id="4340b-141">The number of queries executed.</span></span>
<span data-ttu-id="4340b-142">Tasa de aciertos de caché de consultas (%)</span><span class="sxs-lookup"><span data-stu-id="4340b-142">Query Cache Hit Rate (%)</span></span>              | <span data-ttu-id="4340b-143">La proporción de aciertos de caché de consultas.</span><span class="sxs-lookup"><span data-stu-id="4340b-143">The ratio of query cache hits to misses.</span></span> <span data-ttu-id="4340b-144">La primera vez que se ejecuta una consulta LINQ determinada por EF Core (excepto los parámetros), se debe compilar en qué es un proceso relativamente pesado.</span><span class="sxs-lookup"><span data-stu-id="4340b-144">The first time a given LINQ query is executed by EF Core (excluding parameters), it must be compiled in what is a relatively heavy process.</span></span> <span data-ttu-id="4340b-145">En una aplicación normal, se reutilizan todas las consultas y la tasa de aciertos de caché de consulta debe ser estable al 100% después de un período de preparación inicial.</span><span class="sxs-lookup"><span data-stu-id="4340b-145">In a normal application, all queries are reused, and the query cache hit rate should be stable at 100% after an initial warmup period.</span></span> <span data-ttu-id="4340b-146">Si este número es inferior al 100% a lo largo del tiempo, puede experimentar un rendimiento degradado debido a compilaciones repetidas, lo que podría ser el resultado de una generación de consultas dinámicas que no es óptima.</span><span class="sxs-lookup"><span data-stu-id="4340b-146">If this number is less than 100% over time, you may experience degraded perf due to repeated compilations, which could be a result of suboptimal dynamic query generation.</span></span>
<span data-ttu-id="4340b-147">SaveChanges</span><span class="sxs-lookup"><span data-stu-id="4340b-147">SaveChanges</span></span>                           | <span data-ttu-id="4340b-148">Número de veces que se ha `SaveChanges` llamado a.</span><span class="sxs-lookup"><span data-stu-id="4340b-148">The number of times `SaveChanges` has been called.</span></span> <span data-ttu-id="4340b-149">Tenga en cuenta que `SaveChanges` guarda varios cambios en un único lote, por lo que no representa necesariamente cada actualización individual realizada en una sola entidad.</span><span class="sxs-lookup"><span data-stu-id="4340b-149">Note that `SaveChanges` saves multiple changes in a single batch, so this doesn't necessarily represent each individual update done on a single entity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4340b-150">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4340b-150">Additional resources</span></span>

* [<span data-ttu-id="4340b-151">Documentación de .NET sobre los contadores de eventos</span><span class="sxs-lookup"><span data-stu-id="4340b-151">.NET documentation on event counters</span></span>](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
