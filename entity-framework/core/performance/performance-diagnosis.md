---
title: 'Diagnóstico de rendimiento: EF Core'
description: Diagnóstico del rendimiento de Entity Framework Core e identificación de cuellos de botella
author: roji
ms.date: 12/1/2020
uid: core/performance/performance-diagnosis
ms.openlocfilehash: 9416acf3326056ef7a5d732c4bd456dac751167b
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657739"
---
# <a name="performance-diagnosis"></a>Diagnóstico de rendimiento

En esta sección se describen las formas de detectar problemas de rendimiento en la aplicación de EF y, una vez que se ha identificado una área problemática, cómo analizarlos más adelante para identificar el problema raíz. Es importante diagnosticar e investigar detenidamente cualquier problema antes de pasar a cualquier conclusión y evitar que se asuma Dónde está la raíz del problema.

## <a name="identifying-slow-database-commands-via-logging"></a>Identificación de comandos lentos de base de datos mediante registro

Al final del día, EF prepara y ejecuta los comandos que se van a ejecutar en la base de datos. con bases de datos relacionales, esto significa ejecutar instrucciones SQL a través de la API de ADO.NET Database. Si una determinada consulta está tardando demasiado tiempo (por ejemplo, porque falta un índice), esto se puede detectar detectando los registros de ejecución de comandos y observando cuánto tiempo tardan en completarse.

EF hace que sea muy fácil capturar los tiempos de ejecución de los comandos, a través de un [registro simple](xref:core/logging-events-diagnostics/simple-logging) o de [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging):

### <a name="simple-logging"></a>[Registro sencillo](#tab/simple-logging)

[!code-csharp[Main](../../../samples/core/Performance/BloggingContext.cs#SimpleLogging)]

### <a name="microsoftextensionslogging"></a>[Microsoft.Extensions.Logging](#tab/microsoft-extensions-logging)

[!code-csharp[Main](../../../samples/core/Performance/ExtensionsLoggingContext.cs#ExtensionsLogging)]

***

Cuando el nivel de registro se establece en `LogLevel.Information` , EF emite un mensaje de registro para cada ejecución del comando con el tiempo necesario:

```log
info: 06/12/2020 09:12:36.117 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (4ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[Id], [b].[Name]
      FROM [Blogs] AS [b]
      WHERE [b].[Name] = N'foo'
```

El comando anterior tardó 4 milisegundos. Si un comando determinado tarda más de lo esperado, ha encontrado una posible causa para un problema de rendimiento y ahora se puede centrar en él para comprender por qué se ejecuta lentamente. El registro de comandos también puede revelar casos en los que se están realizando interconexións de base de datos inesperadas. Esto se mostraría como varios comandos en los que solo se esperaba uno.

> [!WARNING]
> Mantener el registro de la ejecución de comandos habilitado en el entorno de producción suele ser una buena idea. El registro reduce la velocidad de la aplicación y puede crear rápidamente archivos de registro enormes que pueden llenar el disco del servidor. Se recomienda mantener solo el inicio de sesión durante un breve intervalo de tiempo para recopilar datos y supervisar cuidadosamente la aplicación, o bien para capturar los datos de registro en un sistema de preproducción.

## <a name="correlating-database-commands-to-linq-queries"></a>Correlacionar comandos de base de datos en consultas LINQ

Un problema con el registro de la ejecución de comandos es que a veces es difícil correlacionar las consultas SQL y las consultas LINQ: los comandos SQL que ejecuta EF pueden ser muy diferentes de las consultas LINQ desde las que se generaron. Para ayudar a solucionar este problema, puede que desee usar la característica de [etiquetas de consulta](xref:core/querying/tags) de EF, que permite insertar un pequeño comentario de identificación en la consulta SQL:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

La etiqueta se muestra en los registros:

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

A menudo merece la pena etiquetar las consultas más importantes de una aplicación de esta manera, para que los registros de ejecución de comandos sean más inmediatos y legibles.

## <a name="other-interfaces-for-capturing-performance-data"></a>Otras interfaces para capturar datos de rendimiento

Hay varias alternativas a la característica de registro de EF para capturar los tiempos de ejecución del comando, lo que puede ser más eficaz. Las bases de datos suelen incluir sus propias herramientas de análisis de rendimiento y seguimiento, que normalmente proporcionan información específica de la base de datos mucho más enriquecida, más allá de los tiempos de ejecución simples. la configuración, las capacidades y el uso reales varían considerablemente entre las bases de datos.

Por ejemplo, [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) es un cliente eficaz que puede conectarse a su instancia de SQL Server y proporcionar información valiosa sobre el rendimiento y la administración. Queda fuera del ámbito de esta sección entrar en los detalles, pero dos funciones que merece la pena mencionar son el [monitor de actividad](/sql/relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio), que proporciona un panel activo de la actividad del servidor (incluidas las consultas más costosas) y la característica [eventos extendidos (XEvent)](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) , que permite definir sesiones de captura de datos arbitrarias que se pueden adaptar a sus necesidades exactas. [La documentación SQL Server sobre supervisión](/sql/relational-databases/performance/monitor-and-tune-for-performance) proporciona más información sobre estas características, así como otras.

Otro enfoque para capturar datos de rendimiento consiste en recopilar información emitida automáticamente por EF o el controlador de base de datos a través de la `DiagnosticSource` interfaz y, a continuación, analizar los datos o mostrarlos en un panel. Si usa Azure, [aplicación de Azure Insights](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) proporciona una excelente supervisión de forma rápida, integrando el rendimiento de la base de datos y los tiempos de ejecución de las consultas en el análisis de la rapidez con que se atienden las solicitudes Web. Puede encontrar más información sobre esto en el [tutorial de rendimiento de Application Insights](/azure/azure-monitor/learn/tutorial-performance)y en la [Página de Azure SQL Analytics](/azure/azure-monitor/insights/azure-sql).

## <a name="inspecting-query-execution-plans"></a>Inspeccionar planes de ejecución de consultas

Una vez que haya finalizado una consulta problemática que requiere optimización, el paso siguiente suele analizar el plan de *ejecución* de la consulta. Cuando las bases de datos reciben una instrucción SQL, normalmente producen un plan del modo en que se va a ejecutar el plan; en ocasiones, esto requiere la toma de decisiones complicada en función de los índices que se han definido, la cantidad de datos que hay en las tablas, etc. (casualmente, el propio plan normalmente se debe almacenar en caché en el servidor para obtener un rendimiento óptimo). Las bases de datos relacionales proporcionan normalmente una manera para que los usuarios vean el plan de consulta, junto con el costo calculado para distintas partes de la consulta. Esto es muy útil para mejorar las consultas.

Para empezar a trabajar en SQL Server, consulte la documentación sobre los [planes de ejecución de consultas](/sql/relational-databases/performance/execution-plans). El flujo de trabajo de análisis típico sería usar [SQL Server Management Studio](/sql/relational-databases/performance/display-an-actual-execution-plan), pegar el SQL de una consulta lenta identificada mediante uno de los medios anteriores y [generar un plan de ejecución gráfico](/sql/relational-databases/performance/display-an-actual-execution-plan):

![Mostrar un plan de ejecución de SQL Server](_static/actualexecplan.png)

Aunque los planes de ejecución pueden parecer complicados al principio, merece la pena dedicar algo de tiempo a familiarizarse con ellos. Es especialmente importante tener en cuenta los costos asociados a cada nodo del plan y para identificar cómo se usan (o no) los índices en los distintos nodos.

Aunque la información anterior es específica de SQL Server, otras bases de datos normalmente proporcionan el mismo tipo de herramientas con una visualización similar.

> [!IMPORTANT]
> A veces, las bases de datos generan distintos planes de consulta en función de los datos reales de la base de datos. Por ejemplo, si una tabla contiene solo unas pocas filas, una base de datos puede optar por no utilizar un índice en esa tabla, pero para realizar un recorrido de tabla completo en su lugar. Si analiza los planes de consulta en una base de datos de prueba, asegúrese siempre de que contenga datos similares a los del sistema de producción.

## <a name="event-counters"></a>Contadores de eventos

Las secciones anteriores se centraban en cómo obtener información sobre los comandos y cómo se ejecutan estos comandos en la base de datos. Además, EF expone un conjunto de *contadores de eventos* que proporcionan información más detallada sobre lo que está ocurriendo en el propio EF y cómo lo usa la aplicación. Estos contadores pueden ser muy útiles para diagnosticar problemas de rendimiento y anomalías de rendimiento específicos, como los [problemas de almacenamiento en caché](xref:core/performance/advanced-performance-topics#dynamically-constructed-queries) de las consultas que causan una recompilación constante, pérdidas de DbContext desechadas y otras.

Para obtener más información, consulte la página dedicada en los [contadores de eventos de EF](xref:core/logging-events-diagnostics/event-counters) .

## <a name="benchmarking-with-ef-core"></a>Pruebas comparativas con EF Core

Al final del día, a veces es necesario saber si una forma determinada de escribir o ejecutar una consulta es más rápida que otra. Es importante no asumir ni especular la respuesta, y es muy fácil reunir una prueba comparativa rápida para obtener la respuesta. Al escribir las pruebas comparativas, se recomienda encarecidamente usar la biblioteca [BenchmarkDotNet](https://benchmarkdotnet.org/index.html) conocida, que controla muchos problemas que los usuarios encuentran al intentar escribir sus propias pruebas comparativas: ¿ha realizado algunas iteraciones de preparación? ¿Cuántas iteraciones ejecuta realmente la prueba comparativa y por qué? Echemos un vistazo a lo que tiene un banco de pruebas con EF Core.

> [!TIP]
> El proyecto de prueba comparativa completa para el origen siguiente está disponible [aquí](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs). Se recomienda copiarla y usarla como plantilla para sus propias pruebas comparativas.

Como escenario de pruebas comparativas simples, vamos a comparar los siguientes métodos diferentes para calcular el promedio de clasificación de todos los blogs de nuestra base de datos:

* Cargar todas las entidades, sumar sus clasificaciones individuales y calcular el promedio.
* Igual que antes, use solo una consulta sin seguimiento. Esto debería ser más rápido, ya que no se realiza la resolución de identidades y las entidades no son instantáneas para los fines del seguimiento de cambios.
* Evite la carga de todas las instancias de la entidad de blog, ya que solo tiene que proyectar la clasificación. Nos evita transferir las demás columnas innecesarias del tipo de entidad de blog.
* Calcule el promedio en la base de datos haciéndolo parte de la consulta. Esta es la forma más rápida, ya que todo se calcula en la base de datos y solo el resultado se transfiere de nuevo al cliente.

Con BenchmarkDotNet, se escribe el código que se va a comparar como un método simple, al igual que una prueba unitaria-y BenchmarkDotNet ejecuta automáticamente cada método para un número suficiente de iteraciones, lo que mide de forma confiable cuánto tiempo tarda y cuánta memoria se asigna. Este es el método diferente ([el código de prueba comparativa completa se puede ver aquí](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs)):

### <a name="load-entities"></a>[Cargar entidades](#tab/load-entities)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntities)]

### <a name="load-entities-no-tracking"></a>[Cargar entidades, sin seguimiento](#tab/load-entities-no-tracking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntitiesNoTracking)]

### <a name="project-only-ranking"></a>[Clasificación solo del proyecto](#tab/project-only-ranking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=ProjectOnlyRanking)]

### <a name="calculate-in-database"></a>[Calcular en la base de datos](#tab/calculate-in-database)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=CalculateInDatabase)]

***

Los resultados se muestran a continuación, como se imprime en BenchmarkDotNet:

|                 Método |       Media |    Error |   StdDev |     Mediana | Proporción | RatioSD |    Gen. 0 |   Gen. 1 | Gen. 2 |  Allocated |
|----------------------- |-----------:|---------:|---------:|-----------:|------:|--------:|---------:|--------:|------:|-----------:|
|           LoadEntities | 2.860,4 EE. UU. | 54,31 EE. UU. | 93,68 EE. UU. | 2.844,5 EE. UU. |  4.55 |    0,33 | 210,9375 | 70,3125 |     - | 1309,56 KB |
| LoadEntitiesNoTracking | 1.353,0 EE. UU. | 21,26 EE. UU. | 18,85 EE. UU. | 1.355,6 EE. UU. |  2.10 |    0.14 |  87,8906 |  3,9063 |     - |  540,09 KB |
|     ProjectOnlyRanking |   910,9 EE. UU. | 20,91 EE. UU. | 61,65 EE. UU. |   892,9 EE. UU. |  1,46 |    0.14 |  41,0156 |  0,9766 |     - |  252,08 KB |
|    CalculateInDatabase |   627,1 EE. UU. | 14,58 EE. UU. | 42,54 EE. UU. |   626,4 EE. UU. |  1.00 |    0.00 |   4,8828 |       - |     - |   33,27 KB |

> [!NOTE]
> Como los métodos crean una instancia y disposean el contexto dentro del método, estas operaciones se cuentan para la prueba comparativa, aunque en realidad no forman parte del proceso de consulta. Esto no es importante si el objetivo es comparar dos alternativas entre sí (dado que la creación de instancias del contexto y la eliminación son iguales) y proporciona una medida más holística para toda la operación.

Una limitación de BenchmarkDotNet es que mide el rendimiento sencillo de un único subproceso de los métodos proporcionados y, por tanto, no es adecuado para escenarios de pruebas comparativas.

> [!IMPORTANT]
> Asegúrese siempre de que los datos de la base de datos son similares a los datos de producción al realizar pruebas comparativas; de lo contrario, los resultados de las pruebas comparativas pueden no representar el rendimiento real en producción.
