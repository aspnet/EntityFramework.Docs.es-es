---
title: 'Consultas eficientes: EF Core'
description: Guía de rendimiento para consultas eficaces con Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-querying
ms.openlocfilehash: e14837b779f2fbe8d5bf10206c6a336a952fc35b
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023879"
---
# <a name="efficient-querying"></a>Consultas eficaces

Realizar consultas de forma eficaz es un gran asunto, que abarca temas como índices, estrategias de carga de entidades relacionadas y muchas otras. En esta sección se detallan algunos temas comunes para agilizar las consultas y los problemas que suelen tener los usuarios.

## <a name="use-indexes-properly"></a>Usar índices correctamente

La principal decisión sobre si una consulta se ejecuta con rapidez o no es si se utilizarán correctamente los índices cuando sea apropiado: las bases de datos se utilizan normalmente para almacenar grandes cantidades de datos y las consultas que atraviesan tablas enteras suelen ser orígenes de graves problemas de rendimiento. No es fácil detectar los problemas de indexación, ya que no es evidente de inmediato si una consulta determinada va a utilizar un índice o no. Por ejemplo:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Indexes)]

Una buena manera de detectar problemas de indexación consiste en localizar primero una consulta lenta y, a continuación, examinar su plan de consulta a través de la herramienta favorita de la base de datos. Consulte la página de [diagnóstico de rendimiento](xref:core/performance/performance-diagnosis) para obtener más información sobre cómo hacerlo. El plan de consulta muestra si la consulta atraviesa toda la tabla o utiliza un índice.

Como norma general, no hay ningún conocimiento especial de EF para usar índices ni para diagnosticar problemas de rendimiento relacionados con ellos. los conocimientos generales de base de datos relacionados con los índices son tan relevantes para las aplicaciones EF como en las aplicaciones que no usan EF. A continuación se enumeran algunas directrices generales que se deben tener en cuenta al usar índices:

* Aunque los índices agilizan las consultas, también ralentizan las actualizaciones, ya que deben mantenerse actualizadas. Evite definir índices que no sean necesarios y considere la posibilidad de usar [filtros de índice](xref:core/modeling/indexes#index-filter) para limitar el índice a un subconjunto de las filas, lo que reduce esta sobrecarga.
* Los índices compuestos pueden acelerar las consultas que filtran varias columnas, pero también pueden acelerar las consultas que no filtran en todas las columnas del índice, en función de la ordenación. Por ejemplo, un índice en las columnas A y B acelera las consultas que filtra a y B, así como las consultas que solo se filtran por, pero no acelera el filtrado de consultas solo por B.
* Si una consulta filtra por una expresión sobre una columna (por ejemplo `price / 2` ,), no se puede usar un índice simple. Sin embargo, puede definir una [columna persistente almacenada](xref:core/modeling/generated-properties#computed-columns) para la expresión y crear un índice sobre ella. Algunas bases de datos también admiten índices de expresión, que se pueden usar directamente para acelerar las consultas que filtran cualquier expresión.
* Las distintas bases de datos permiten configurar los índices de varias maneras y, en muchos casos EF Core proveedores los exponen a través de la API fluida. Por ejemplo, el proveedor de SQL Server le permite configurar si un índice está [agrupado](xref:core/providers/sql-server/indexes#clustering)o establecer su [factor de relleno](xref:core/providers/sql-server/indexes#fill-factor). Consulte la documentación del proveedor para obtener más información.

## <a name="project-only-properties-you-need"></a>Solo las propiedades necesarias del proyecto

EF Core hace que sea muy fácil consultar las instancias de la entidad y, a continuación, usar esas instancias en el código. Sin embargo, la consulta de instancias de entidad puede extraer con frecuencia más datos de los necesarios en la base de datos. Tenga en cuenta lo siguiente.

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectEntities)]

Aunque este código solo necesita realmente la propiedad de cada blog `Url` , se captura toda la entidad de blog y las columnas innecesarias se transfieren de la base de datos:

```sql
SELECT [b].[BlogId], [b].[CreationDate], [b].[Name], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
```

Esto se puede optimizar mediante el uso `Select` de para indicar a EF Qué columnas se deben proyectar:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectSingleProperty)]

El SQL resultante extrae solo las columnas necesarias:

```csharp
SELECT [b].[Url]
FROM [Blogs] AS [b]
```

Si necesita proyectar más de una columna, salga de un tipo anónimo de C# con las propiedades que desee.

Tenga en cuenta que esta técnica es muy útil para las consultas de solo lectura, pero las cosas son más complicadas si necesita *Actualizar* los blogs capturados, ya que el seguimiento de cambios de EF solo funciona con instancias de entidad. Es posible realizar actualizaciones sin cargar entidades enteras adjuntando una instancia de blog modificada e indicando a EF qué propiedades han cambiado, pero es una técnica más avanzada que puede no merecer la pena.

## <a name="limit-the-resultset-size"></a>Limitar el tamaño del conjunto de resultados

De forma predeterminada, una consulta devuelve todas las filas que coincidan con sus filtros:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NoLimit)]

Dado que el número de filas devueltas depende de los datos reales de la base de datos, es imposible saber cuántos datos se cargarán desde la base de datos, cuánta memoria ocuparán los resultados y cuánta carga adicional se generará al procesar estos resultados (por ejemplo, enviándolos a un explorador del usuario a través de la red). En realidad, las bases de datos de prueba suelen contener poca información, por lo que todo funciona bien durante las pruebas, pero los problemas de rendimiento aparecen repentinamente cuando la consulta comienza a ejecutarse en datos reales y se devuelven muchas filas.

Como resultado, normalmente merece la pena pensar en limitar el número de resultados:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Limit25)]

Como mínimo, la interfaz de usuario podría mostrar un mensaje que indica que pueden existir más filas en la base de datos (y permitir que se recuperen de alguna otra manera). Una solución completa implementaría la *paginación*, donde la interfaz de usuario solo muestra un número determinado de filas a la vez y permite a los usuarios avanzar a la siguiente página según sea necesario. Normalmente, esto combina <xref:System.Linq.Enumerable.Take%2A> los <xref:System.Linq.Enumerable.Skip%2A> operadores y para seleccionar un intervalo específico en el conjunto de resultados cada vez.

## <a name="avoid-cartesian-explosion-when-loading-related-entities"></a>Evitar la explosión cartesiano al cargar entidades relacionadas

En las bases de datos relacionales, todas las entidades relacionadas se cargan mediante la introducción de instrucciones JOIN en consultas únicas.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Si un blog típico tiene varias entradas relacionadas, las filas de estas entradas duplicarán la información del blog, lo que genera un problema conocido como "explosión cartesiana". A medida que se cargan más relaciones uno a varios, la cantidad de datos duplicados puede crecer y afectar negativamente al rendimiento de la aplicación.

EF permite evitar este efecto mediante el uso de "consultas divididas", que cargan las entidades relacionadas a través de consultas independientes. Para obtener más información, lea [la documentación sobre consultas divididas y únicas](xref:core/querying/single-split-queries).

> [!NOTE]
> La implementación actual de [consultas divididas](xref:core/querying/single-split-queries) ejecuta un viaje de ida y vuelta para cada consulta. Tenemos previsto mejorar esto en el futuro y ejecutar todas las consultas en un solo viaje de ida y vuelta.

## <a name="load-related-entities-eagerly-when-possible"></a>Cargar las entidades relacionadas concienzudamente cuando sea posible

Se recomienda leer [la página dedicada en entidades relacionadas](xref:core/querying/related-data) antes de continuar con esta sección.

Cuando se trabaja con entidades relacionadas, normalmente sabemos de antemano lo que necesitamos cargar: un ejemplo típico sería la carga de un determinado conjunto de blogs, junto con todas sus publicaciones. En estos escenarios, siempre es mejor usar la [carga diligente](xref:core/querying/related-data/eager), de modo que EF pueda capturar todos los datos necesarios en un viaje de ida y vuelta. La característica de [inclusión filtrada](xref:core/querying/related-data/eager#filtered-include) , introducida en EF Core 5,0, también le permite limitar las entidades relacionadas que le gustaría cargar, manteniendo el proceso de carga diligente y, por lo tanto, factible en un único viaje de ida y vuelta:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

En otros escenarios, es posible que no sepa qué entidad relacionada vamos a necesitar antes de obtener su entidad principal. Por ejemplo, al cargar un blog, es posible que necesitemos consultar algún otro origen de datos, posiblemente un servicio WebService, para saber si estamos interesados en las publicaciones de ese blog. En estos casos, la carga [explícita](xref:core/querying/related-data/explicit) o [diferida](xref:core/querying/related-data/lazy) se puede usar para capturar entidades relacionadas por separado y rellenar la navegación de entradas del blog. Tenga en cuenta que, dado que estos métodos no son diligentes, requieren viajes de ida y vuelta adicionales a la base de datos, que es el origen de la ralentización. en función de su escenario específico, puede ser más eficaz cargar siempre todas las publicaciones, en lugar de ejecutar los viajes de ida adicionales y obtener de forma selectiva solo las publicaciones que necesite.

### <a name="beware-of-lazy-loading"></a>Tenga cuidado con la carga diferida

La [carga diferida](xref:core/querying/related-data/lazy) suele parecer una manera muy útil de escribir la lógica de base de datos, ya que EF Core carga automáticamente las entidades relacionadas de la base de datos, ya que el código tiene acceso a ellas. Esto evita la carga de entidades relacionadas que no son necesarias (como la [carga explícita](xref:core/querying/related-data/explicit)) y, aparentemente, libera al programador de tener que tratar las entidades relacionadas de forma conjunta. Sin embargo, la carga diferida es especialmente propensa para producir viajes de ida y vuelta innecesarios, lo que puede ralentizar la aplicación.

Tenga en cuenta lo siguiente.

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NPlusOne)]

Este fragmento de código aparentemente inocente recorre en iteración todos los blogs y sus publicaciones, para imprimirlos. La activación del [registro de instrucciones](xref:core/logging-events-diagnostics/index) de EF Core revela lo siguiente:

```console
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[BlogId], [b].[Rating], [b].[Url]
      FROM [Blogs] AS [b]
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (5ms) [Parameters=[@__p_0='1'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='2'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='3'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0

... and so on
```

¿Qué ocurre aquí? ¿Por qué se envían todas estas consultas para los bucles simples anteriores? Con la carga diferida, las publicaciones de un blog solo se cargan (de forma diferida) cuando se tiene acceso a su propiedad posts. como resultado, cada iteración de la instrucción foreach interna desencadena una consulta de base de datos adicional, en su propio recorrido de ida y vuelta. Como resultado, después de que la consulta inicial cargue todos los blogs, tendrá otra consulta *por cada blog* y cargará todas sus entradas; a veces, esto se denomina el problema *N + 1* y puede causar problemas de rendimiento muy significativos.

Suponiendo que vamos a necesitar todas las publicaciones de blogs, tiene sentido usar la carga diligente aquí en su lugar. Se puede usar el operador [include](xref:core/querying/related-data/eager#eager-loading) para realizar la carga, pero como solo se necesitan las direcciones URL de los blogs (y solo se deben [cargar los elementos necesarios](xref:core/performance/efficient-querying#project-only-properties-you-need)). En su lugar, usaremos una proyección:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#EagerlyLoadRelatedAndProject)]

Esto hará que EF Core Capture todos los blogs, junto con sus posts, en una sola consulta. En algunos casos, también puede resultar útil evitar efectos de explosión cartesiano mediante el uso de [consultas divididas](xref:core/querying/single-split-queries).

> [!WARNING]
> Dado que la carga diferida facilita enormemente el desencadenamiento del problema N + 1, se recomienda evitarlo. La carga diligente o explícita la hacen muy claras en el código fuente cuando se produce un viaje de base de datos.

## <a name="buffering-and-streaming"></a>Almacenamiento en búfer y transmisión por secuencias

El almacenamiento en búfer hace referencia a la carga de todos los resultados de la consulta en la memoria, mientras que el streaming significa que EF trata a la aplicación como un resultado único cada vez, nunca con el conjunto de resultados completo en la memoria. En principio, los requisitos de memoria de una consulta de streaming son fijos, es decir, si la consulta devuelve 1 fila o 1000; por otro lado, una consulta de almacenamiento en búfer requiere más memoria para devolver más filas. En el caso de las consultas que generan conjuntos de resultados grandes, puede ser un factor de rendimiento importante.

El hecho de que un búfer de consulta o secuencias dependa de cómo se evalúa:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#BufferingAndStreaming)]

Si las consultas devuelven solo unos cuantos resultados, probablemente no tenga que preocuparse de ello. Sin embargo, si la consulta puede devolver un gran número de filas, merece la pena hacer streaming en lugar de almacenar en búfer.

> [!NOTE]
> Evite el uso de <xref:System.Linq.Enumerable.ToList%2A> o <xref:System.Linq.Enumerable.ToArray%2A> si piensa usar otro operador LINQ en el resultado; de esta forma, se almacenarán todos los resultados en la memoria. En su lugar, use <xref:System.Linq.Enumerable.AsEnumerable%2A>.

### <a name="internal-buffering-by-ef"></a>Almacenamiento en búfer interno por EF

En determinadas situaciones, EF almacenará en búfer el conjunto de resultados internamente, independientemente de cómo se evalúe la consulta. Los dos casos en los que ocurre esto son:

* Cuando se implementa una estrategia de ejecución de reintento. Esto se hace para asegurarse de que se devuelven los mismos resultados si la consulta se vuelve a intentar más tarde.
* Cuando se usa la [consulta Split](xref:core/querying/single-split-queries) , se almacenan en búfer los conjuntos de filas de todas las consultas, excepto la última, a menos que se habilite MARS en SQL Server. Esto se debe a que normalmente no es posible tener varios conjuntos de consultas de consulta activos al mismo tiempo.

Tenga en cuenta que este almacenamiento en búfer interno se produce además de cualquier almacenamiento en búfer que se produzca mediante operadores LINQ. Por ejemplo, si se usa <xref:System.Linq.Enumerable.ToList%2A> en una consulta y se implementa una estrategia de ejecución de reintento, el conjunto de resultados se carga en la memoria *dos veces*: una vez internamente por EF y otra <xref:System.Linq.Enumerable.ToList%2A> .

## <a name="tracking-no-tracking-and-identity-resolution"></a>Seguimiento, no seguimiento y resolución de identidad

Se recomienda leer [la página dedicada en el seguimiento y sin seguimiento](xref:core/querying/tracking) antes de continuar con esta sección.

EF realiza un seguimiento de las instancias de la entidad de forma predeterminada, de modo que los cambios en ellas se detectan y se conservan cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a. Otro efecto de las consultas de seguimiento es que EF detecta si ya se ha cargado una instancia de los datos y devolverá automáticamente la instancia de la que se ha realizado un seguimiento en lugar de devolver una nueva. Esto se denomina *resolución de identidad*. Desde la perspectiva del rendimiento, el seguimiento de cambios implica lo siguiente:

* EF mantiene internamente un diccionario de instancias de las que se ha realizado un seguimiento. Cuando se cargan nuevos datos, EF comprueba el diccionario para ver si ya se ha realizado un seguimiento de una instancia para la clave de esa entidad (resolución de identidad). El mantenimiento y las búsquedas del diccionario tardan un tiempo en cargar los resultados de la consulta.
* Antes de entregar una instancia cargada a la aplicación, EF realiza *instantáneas* de la instancia y mantiene la instantánea internamente. Cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a, la instancia de la aplicación se compara con la instantánea para detectar los cambios que se van a conservar. La instantánea ocupa más memoria y el propio proceso de la instantánea lleva tiempo; a veces es posible especificar un comportamiento de la instantánea diferente, posiblemente más eficaz, a través de los [comparadores de valores](xref:core/modeling/value-comparers), o usar los proxies de seguimiento de cambios para omitir el proceso de la instantánea (aunque esto incluye su propio conjunto de desventajas).

En los escenarios de solo lectura donde los cambios no se guardan en la base de datos, se pueden evitar las sobrecargas anteriores mediante el uso [de consultas sin seguimiento](xref:core/querying/tracking#no-tracking-queries). Sin embargo, puesto que las consultas sin seguimiento no realizan la resolución de identidad, una fila de base de datos a la que hacen referencia varias filas cargadas se materializará como instancias diferentes.

Para ilustrar, supongamos que estamos cargando un gran número de publicaciones de la base de datos, así como el blog al que se hace referencia en cada publicación. Si se producen 100 publicaciones para hacer referencia al mismo blog, una consulta de seguimiento lo detecta a través de la resolución de identidades y todas las instancias de post hacen referencia a la misma instancia de blog desduplicada. Por el contrario, una consulta sin seguimiento duplica el mismo blog 100 veces y el código de aplicación debe escribirse en consecuencia.

Estos son los resultados de una prueba comparativa que compara el seguimiento con el comportamiento sin seguimiento de una consulta que carga 10 blogs con 20 publicaciones cada una. [El código fuente está disponible aquí](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/QueryTrackingBehavior.cs), no dude en usarlo como base para sus propias mediciones.

|       Método | NumBlogs | NumPostsPerBlog |       Media |    Error |   StdDev |     Mediana | Proporción | RatioSD |   Gen. 0 |   Gen. 1 | Gen. 2 | Allocated |
|------------- |--------- |---------------- |-----------:|---------:|---------:|-----------:|------:|--------:|--------:|--------:|------:|----------:|
|   Realizar un seguimiento |       10 |              20 | 1.414,7 EE. UU. | 27,20 EE. UU. | 45,44 EE. UU. | 1.405,5 EE. UU. |  1.00 |    0.00 | 60,5469 | 13,6719 |     - | 380,11 KB |
| AsNoTracking |       10 |              20 |   993,3 EE. UU. | 24,04 EE. UU. | 65,40 EE. UU. |   966,2 EE. UU. |  0.71 |    0,05 | 37,1094 |  6,8359 |     - | 232,89 KB |

Por último, es posible realizar actualizaciones sin la sobrecarga que supone el seguimiento de cambios, ya que se usa una consulta sin seguimiento y, después, se asocia la instancia devuelta al contexto, lo que especifica los cambios que se van a realizar. Esto transfiere la carga del seguimiento de cambios de EF al usuario y solo debe intentarse si se ha demostrado que la sobrecarga del seguimiento de cambios es inaceptable a través de la generación de perfiles o la prueba comparativa.

## <a name="using-raw-sql"></a>Uso de SQL sin formato

En algunos casos, existe un SQL más optimizado para la consulta, que EF no genera. Esto puede ocurrir cuando la construcción de SQL es una extensión específica de la base de datos que no es compatible, o simplemente porque EF no se traduce todavía en él. En estos casos, escribir SQL a mano puede proporcionar un aumento considerable del rendimiento y EF admite varias maneras de hacerlo.

* Use SQL sin formato [directamente en la consulta](xref:core/querying/raw-sql), por ejemplo, a través de <xref:Microsoft.EntityFrameworkCore.RelationalQueryableExtensions.FromSqlRaw%2A> . EF incluso le permite componer sobre el SQL sin formato con consultas LINQ normales, lo que le permite expresar solo una parte de la consulta en SQL sin procesar. Esta es una buena técnica cuando el código SQL sin procesar solo debe usarse en una sola consulta en el código base.
* Definir una [función definida por el usuario](xref:core/querying/database-functions) (UDF) y, a continuación, llamarla desde las consultas. Tenga en cuenta que, dado que 5,0, EF permite que las UDF devuelvan conjuntos de resultados completos, que se conocen como funciones con valores de tabla (TVF), y también permite asignar un `DbSet` a una función, lo que hace que tenga el aspecto de solo otra tabla.
* Defina una vista de base de datos y realice una consulta a partir de ella en las consultas. Tenga en cuenta que, a diferencia de las funciones, las vistas no pueden aceptar parámetros.

> [!NOTE]
> SQL sin procesar generalmente debe usarse como último recurso, después de asegurarse de que EF no puede generar el SQL que desea y cuando el rendimiento es lo suficientemente importante para que la consulta determinada lo justifique. El uso de SQL sin procesar aporta importantes desventajas de mantenimiento.

## <a name="asynchronous-programming"></a>Programación asincrónica

Como norma general, para que la aplicación sea escalable, es importante usar siempre las API asincrónicas en lugar de una sincrónica (por ejemplo, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> en lugar de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ). Las API sincrónicas bloquean el subproceso mientras dure la e/s de la base de datos, lo que aumenta la necesidad de subprocesos y el número de cambios de contexto de subprocesos que se deben producir.

Para obtener más información, vea la página sobre [programación asincrónica](xref:core/miscellaneous/async).

> [!WARNING]
> Evite mezclar código sincrónico y asincrónico en la misma aplicación: es muy fácil desencadenar problemas sutiles de colapso de grupos de subprocesos.

## <a name="additional-resources"></a>Recursos adicionales

Vea la [sección rendimiento](xref:core/querying/null-comparisons#writing-performant-queries) de la página de documentación de comparación nula para ver algunas prácticas recomendadas al comparar valores que aceptan valores NULL.
