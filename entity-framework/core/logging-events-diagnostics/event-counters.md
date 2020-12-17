---
title: 'Contadores de eventos: EF Core'
description: Seguimiento EF Core rendimiento y diagnóstico de anomalías con los contadores de eventos de .NET
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 73d360b266db3d3252defbf4a4035c0eb430e22e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635827"
---
# <a name="event-counters"></a>Contadores de eventos

> [!NOTE]
> Esta característica se agregó en EF Core 5.0.

Entity Framework Core (EF Core) expone métricas numéricas continuas que pueden proporcionar una buena indicación del estado del programa. Estas métricas se pueden usar para los siguientes fines:

* Seguimiento de la carga de base de datos general en tiempo real cuando se ejecuta la aplicación
* Exponga prácticas de codificación problemáticas que puedan provocar un rendimiento degradado
* Seguimiento y aislamiento del comportamiento anómalo del programa

EF Core informa de las métricas a través de la característica de contadores de eventos estándar de .NET; se recomienda leer [esta entrada de blog](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) para obtener una visión general rápida de cómo funcionan los contadores.

## <a name="attach-to-a-process-using-dotnet-counters"></a>Asociar a un proceso mediante dotnet-counters

La [herramienta dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) se puede utilizar para asociar a un proceso en ejecución y notificar EF Core los contadores de eventos con regularidad. no es necesario hacer nada especial en el programa para que estos contadores estén disponibles.

En primer lugar, instale la `dotnet-counters` herramienta: `dotnet tool install --global dotnet-counters` .

A continuación, busque el ID. de proceso (PID) del proceso .NET que ejecuta la aplicación EF Core:

### <a name="windows"></a>[Windows](#tab/windows)

1. Abra el administrador de tareas de Windows; para ello, haga clic con el botón secundario en la barra de tareas y seleccione "Administrador de tareas".
2. Asegúrese de que la opción "más detalles" esté seleccionada en la parte inferior de la ventana.
3. En la pestaña procesos, haga clic con el botón secundario en una columna y asegúrese de que la columna PID esté habilitada.
4. Busque la aplicación en la lista de procesos y obtenga su identificador de proceso de la columna PID.

### <a name="linux-or-macos"></a>[Linux o macOS](#tab/fluent-api)

1. Use el `ps` comando para enumerar todos los procesos que se ejecutan para el usuario.
2. Busque la aplicación en la lista de procesos y obtenga su identificador de proceso de la columna PID.

***

Dentro de la aplicación .NET, el ID. de proceso está disponible como `Process.GetCurrentProcess().Id` ; esto puede ser útil para imprimir el PID al iniciarse.

Por último, inicie `dotnet-counters` como se indica a continuación:

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

`dotnet-counters` ahora se asociará al proceso en ejecución y comenzará a notificar datos de contador continuos:

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

## <a name="counters-and-their-meaning"></a>Contadores y su significado

Nombre del contador                          | Descripción
------------------------------------- | ----
Active DbContexts                     | El número de instancias de DbContext activas y no desechadas actualmente en la aplicación. Si este número crece continuamente, puede tener una fuga porque las instancias de DbContext no se desechan correctamente. Tenga en cuenta que si está habilitada la [agrupación de contexto](xref:core/performance/advanced-performance-topics#dbcontext-pooling) , este número incluye instancias de DbContext agrupadas que no se usan actualmente.
Errores de operación de la estrategia de ejecución | Número de veces que una operación de base de datos no se pudo ejecutar. Si se habilita una estrategia de ejecución de reintento, esto incluye cada error individual en varios intentos en la misma operación. Se puede usar para detectar problemas transitorios con la infraestructura.
Errores de simultaneidad optimista       | Número de veces `SaveChanges` que se produjo un error de simultaneidad optimista porque los datos del almacén de datos se cambiaron desde que el código lo cargó. Esto corresponde a un <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> que se está iniciando.
Consultas                               | El número de consultas ejecutadas.
Tasa de aciertos de caché de consultas (%)              | La proporción de aciertos de caché de consultas. La primera vez que se ejecuta una consulta LINQ determinada por EF Core (excepto los parámetros), se debe compilar en qué es un proceso relativamente pesado. En una aplicación normal, se reutilizan todas las consultas y la tasa de aciertos de caché de consulta debe ser estable al 100% después de un período de preparación inicial. Si este número es inferior al 100% a lo largo del tiempo, puede experimentar un rendimiento degradado debido a compilaciones repetidas, lo que podría ser el resultado de una generación de consultas dinámicas que no es óptima.
SaveChanges                           | Número de veces que se ha `SaveChanges` llamado a. Tenga en cuenta que `SaveChanges` guarda varios cambios en un único lote, por lo que no representa necesariamente cada actualización individual realizada en una sola entidad.

## <a name="additional-resources"></a>Recursos adicionales

* [Documentación de .NET sobre los contadores de eventos](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
