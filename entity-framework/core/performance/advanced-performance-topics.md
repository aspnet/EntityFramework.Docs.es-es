---
title: Temas de rendimiento avanzados
description: Temas de rendimiento avanzados para Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657771"
---
# <a name="advanced-performance-topics"></a>Temas de rendimiento avanzados

## <a name="dbcontext-pooling"></a>Agrupación de DbContext

`AddDbContextPool` habilita la agrupación de `DbContext` instancias de. La agrupación de contexto puede aumentar el rendimiento en escenarios de gran escala, como servidores Web, mediante la reutilización de instancias de contexto, en lugar de crear nuevas instancias para cada solicitud.

El patrón típico en una aplicación ASP.NET Core con EF Core implica registrar un tipo personalizado en <xref:Microsoft.EntityFrameworkCore.DbContext> el contenedor de [inserción de dependencias](/aspnet/core/fundamentals/dependency-injection) y obtener instancias de ese tipo a través de parámetros de constructor en controladores o Razor pages. Mediante la inserción de constructores, se crea una nueva instancia de contexto para cada solicitud.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> habilita un grupo de instancias de contexto reutilizables. Para usar la agrupación de contexto, use el `AddDbContextPool` método en lugar de `AddDbContext` durante el registro del servicio:

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Cuando `AddDbContextPool` se usa, en el momento en que se solicita una instancia de contexto, EF comprueba primero si hay una instancia disponible en el grupo. Una vez que termina el procesamiento de la solicitud, se restablece cualquier estado en la instancia y la propia instancia se devuelve al grupo.

Esto es conceptualmente similar a la forma en que funciona la agrupación de conexiones en los proveedores de ADO.NET y tiene la ventaja de ahorrar parte del costo de inicialización de la instancia de contexto.

El `poolSize` parámetro de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> establece el número máximo de instancias retenidas por el grupo. Una vez que `poolSize` se supera, las nuevas instancias de contexto no se almacenan en caché y EF recurre al comportamiento de no agrupación de la creación de instancias a petición.

### <a name="limitations"></a>Limitaciones

Se debe crear un perfiles de las aplicaciones y probarlas para mostrar que la inicialización del contexto supone un costo significativo.

`AddDbContextPool` tiene algunas limitaciones en lo que se puede hacer en el `OnConfiguring` método del contexto.

> [!WARNING]
> Evite el uso de la agrupación de contexto en aplicaciones que mantienen el estado. Por ejemplo, los campos privados en el contexto que no se deberían compartir entre las solicitudes. EF Core solo restablece el estado que se conoce antes de agregar una instancia de contexto al grupo.

La agrupación de contexto funciona mediante la reutilización de la misma instancia de contexto en todas las solicitudes. Esto significa que se registra de forma eficaz como [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) en lo que se refiere a la propia instancia para que pueda persistir.

La agrupación de contexto está pensada para escenarios en los que la configuración de contexto, que incluye los servicios resueltos, se fija entre solicitudes. En los casos en los que se requieren los servicios de [ámbito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) o es necesario cambiar la configuración, no utilice la agrupación. La ganancia de rendimiento de la agrupación suele ser despreciable, excepto en escenarios muy optimizados.

## <a name="query-caching-and-parameterization"></a>Almacenamiento en caché y parametrización de consultas

Cuando EF recibe un árbol de consulta LINQ para su ejecución, primero debe "compilar" ese árbol en una consulta SQL. Dado que se trata de un proceso intensivo, EF almacena en caché las consultas por la *forma* de árbol de consulta: las consultas con la misma estructura reutilizan las salidas de compilación almacenadas en caché internamente y pueden omitir la compilación repetida. Las distintas consultas pueden seguir haciendo referencia a *valores* diferentes, pero siempre y cuando estos valores estén correctamente parametrizados, la estructura será la misma y el almacenamiento en caché funcionará correctamente.

Tenga en cuenta las dos consultas siguientes:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

Dado que los árboles de expresión contienen constantes diferentes, el árbol de expresión difiere y cada una de estas consultas se compilará por separado mediante EF Core. Además, cada consulta genera un comando SQL ligeramente diferente:

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

Dado que SQL es diferente, el servidor de base de datos probablemente también tendrá que generar un plan de consulta para ambas consultas, en lugar de reutilizar el mismo plan.

Una pequeña modificación en las consultas puede cambiar considerablemente:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

Dado que el nombre del blog ahora tiene *parámetros*, ambas consultas tienen la misma forma de árbol y EF solo debe compilarse una vez. El SQL generado también tiene parámetros, lo que permite que la base de datos vuelva a usar el mismo plan de consulta:

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

Tenga en cuenta que no hay necesidad de parametrizar cada una de las consultas: es absolutamente preciso tener algunas consultas con constantes y, en realidad, las bases de datos (y EF) pueden realizar algunas tareas de optimización en torno a constantes que no son posibles cuando la consulta está parametrizada. Vea la sección sobre [consultas construidas dinámicamente](#dynamically-constructed-queries) para obtener un ejemplo en el que la parametrización adecuada es fundamental.

> [!NOTE]
> Los [contadores de eventos](xref:core/logging-events-diagnostics/event-counters) de EF Core notifican la tasa de aciertos de caché de consultas. En una aplicación normal, este contador alcanza el 100% poco después del inicio del programa, una vez que la mayoría de las consultas se han ejecutado al menos una vez. Si este contador permanece estable por debajo del 100%, es una indicación de que la aplicación puede estar haciendo algo que derrota a la caché de consultas. es una buena idea investigarlo.

> [!NOTE]
> La forma en que la base de datos administra los planes de consulta depende de la base de datos. Por ejemplo, SQL Server mantiene implícitamente una memoria caché del plan de consulta LRU, mientras que PostgreSQL no (pero las instrucciones preparadas pueden producir un efecto final muy similar). Consulte la documentación de la base de datos para obtener más detalles.

## <a name="dynamically-constructed-queries"></a>Consultas construidas dinámicamente

En algunas situaciones, es necesario crear dinámicamente las consultas LINQ en lugar de especificarlas de forma inadecuada en el código fuente. Esto puede ocurrir, por ejemplo, en un sitio web que recibe detalles de consulta arbitrarios de un cliente, con operadores de consulta abiertos (ordenación, filtrado, paginación...). En principio, si se realiza correctamente, las consultas construidas dinámicamente pueden ser tan eficaces como las normales (aunque no es posible usar la optimización de consultas compiladas con consultas dinámicas). Sin embargo, en la práctica, suelen ser el origen de los problemas de rendimiento, ya que es fácil producir accidentalmente árboles de expresión con formas que difieren cada vez.

En el ejemplo siguiente se usan dos técnicas para crear una consulta de forma dinámica: agregamos un `Where` operador a la consulta solo si el parámetro especificado no es NULL. Tenga en cuenta que no se trata de un buen caso de uso para la creación dinámica de una consulta, pero se usa para simplificar:

### <a name="with-constant"></a>[Con constante](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[With (parámetro)](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

Las pruebas comparativas de estas dos técnicas proporcionan los siguientes resultados:

|        Método |       Media |    Error |    StdDev |   Gen. 0 |  Gen. 1 | Gen. 2 | Allocated |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  WithConstant | 1.096,7 EE. UU. | 12,54 EE. UU. |  11,12 EE. UU. | 13,6719 | 1,9531 |     - |  83,91 KB |
| WithParameter |   570,8 EE. UU. | 42,43 EE. UU. | 124,43 EE. UU. |  5,8594 |      - |     - |  37,16 KB |

Aunque la diferencia de submilisegundos parezca pequeña, tenga en cuenta que la versión constante contamina continuamente la memoria caché y hace que se vuelvan a compilar otras consultas, lo que ralentiza también su funcionamiento.

> [!NOTE]
> Evite construir consultas con la API de árbol de expresión a menos que realmente necesite. Aparte de la complejidad de la API, es muy fácil producir involuntariamente problemas de rendimiento significativos al utilizarlos.
