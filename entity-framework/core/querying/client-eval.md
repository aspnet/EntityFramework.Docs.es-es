---
title: 'Evaluación de cliente frente a servidor: EF Core'
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 429fec2d2da6eb8910ac20531455ec8183096ec6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370427"
---
# <a name="client-vs-server-evaluation"></a>Evaluación de cliente frente a servidor

Como norma general, Entity Framework Core intenta evaluar una consulta en el servidor lo máximo posible. EF Core convierte partes de la consulta en parámetros, que se pueden evaluar en el lado cliente. El resto de la consulta (junto con los parámetros generados) se proporciona al proveedor de base de datos para determinar la consulta de base de datos equivalente que se va a evaluar en el servidor. EF Core admite la evaluación de cliente parcial en la proyección de nivel superior (fundamentalmente, la última llamada a `Select()`). Si la proyección de nivel superior de la consulta no se puede traducir en el servidor, EF Core capturará los datos necesarios del servidor y evaluará las partes restantes de la consulta en el cliente. Si EF Core detecta una expresión, en cualquier lugar que no sea la proyección de nivel superior, que no se puede traducir en el servidor, inicia una excepción en tiempo de ejecución. Vea [Funcionamiento de las consultas](xref:core/querying/how-query-works) para saber cómo EF Core determina lo que no se puede traducir al servidor.

> [!NOTE]
> Antes de la versión 3.0, Entity Framework Core admitía la evaluación de cliente en cualquier parte de la consulta. Para obtener más información, vea la [sección sobre versiones anteriores](#previous-versions).

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) de este artículo en GitHub.

## <a name="client-evaluation-in-the-top-level-projection"></a>Evaluación de cliente en la proyección de nivel superior

En el ejemplo siguiente, se usa un método auxiliar para estandarizar las direcciones URL para blogs, que se devuelven desde una base de datos de SQL Server. Como el proveedor de SQL Server no tiene información de cómo se implementa este método, no es posible traducirlo a código SQL. Todos los demás aspectos de la consulta se evalúan en la base de datos, pero es el cliente quien pasa la `URL` devuelta mediante este método.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>Evaluación de cliente no admitida

Aunque la evaluación de cliente es útil, en ocasiones puede generar un rendimiento bajo. Considere la consulta siguiente, en la que ahora el método auxiliar se usa en un filtro WHERE. Como el filtro no se puede aplicar en la base de datos, se deben extraer todos los datos de la memoria para aplicar el filtro en el cliente. Según el filtro y la cantidad de datos en el servidor, la evaluación de cliente podría dar lugar a un rendimiento deficiente. Por tanto, Entity Framework Core bloquea esa evaluación de cliente e inicia una excepción en tiempo de ejecución.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>Evaluación explícita de cliente

Es posible que tenga que forzar la evaluación de cliente de forma explícita en ciertos casos como los siguientes:

- La cantidad de datos es pequeña, por lo que la evaluación en el cliente no incurre en una gran penalización del rendimiento.
- El operador de LINQ que se usa carece de traducción del lado servidor.

En esos casos, puede participar de forma explícita en la evaluación de cliente si llamada a métodos como `AsEnumerable` o `ToList` (`AsAsyncEnumerable` o `ToListAsync` para async). Al usar `AsEnumerable` se haría streaming de los resultados, pero al usar `ToList` se almacenarían en búfer mediante la creación de una lista, que también consume memoria adicional. Como si se realizara la enumeración varias veces, el almacenamiento de los resultados en una lista es más útil, ya que solo hay una consulta a la base de datos. En función del uso determinado, debe evaluar qué método es más útil para cada caso.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ExplicitClientEval)]

## <a name="potential-memory-leak-in-client-evaluation"></a>Posible fuga de memoria en la evaluación de cliente

Como la traducción y compilación de consultas son costosas, EF Core almacena en caché el plan de consulta compilado. El delegado en caché puede usar el código de cliente mientras se realiza la evaluación de cliente de la proyección de nivel superior. EF Core genera parámetros para las partes evaluadas por el cliente del árbol y reutiliza el plan de consulta en el que reemplaza los valores de parámetro. Pero determinadas constantes del árbol de expresión no se pueden convertir en parámetros. Si el delegado en caché contiene ese tipo de constantes, esos objetos no se pueden recolectar como elementos no utilizados porque todavía se hace referencia a ellos. Si este tipo de objeto contiene un elemento DbContext u otros servicios, podría hacer que el uso de memoria de la aplicación crezca con el tiempo. Este comportamiento suele ser un signo de fuga de memoria. EF Core inicia una excepción cada vez que detecta constantes de un tipo que no se puede asignar mediante el proveedor de base de datos actual. Las causas comunes y sus soluciones son las siguientes:

- **Uso de un método de instancia**: cuando se usan métodos de instancia en una proyección de cliente, el árbol de expresión contiene una constante de la instancia. Si el método no usa ningún dato de la instancia, considere la posibilidad de convertirlo en estático. Si necesita datos de la instancia en el cuerpo del método, pase los datos específicos como argumento al método.
- **Paso de argumentos constantes al método**: este caso surge generalmente al usar `this` en un argumento para el método de cliente. Puede dividir el argumento en varios argumentos escalares, que podrá asignar el proveedor de base de datos.
- **Otras constantes**: si se detecta una constante en cualquier otro caso, puede evaluar si es necesaria para el procesamiento. Si es necesario tener la constante, o bien si no puede usar una solución de los casos anteriores, cree una variable local para almacenar el valor y use la variable local en la consulta. EF Core convertirá la variable local en un parámetro.

## <a name="previous-versions"></a>Versiones anteriores

La sección siguiente se aplica a las versiones de EF Core anteriores a la 3.0.

En las versiones anteriores de EF Core se admitía la evaluación de cliente en cualquier parte de la consulta, no solo en la proyección de nivel superior. Por ese motivo las consultas similares a la publicada en la sección [Evaluación de cliente no admitida](#unsupported-client-evaluation) funcionaban correctamente. Como este comportamiento podría provocar problemas de rendimiento inadvertidos, EF Core registró una advertencia de evaluación de cliente. Para obtener más información sobre cómo ver la salida de registro, vea [Registro](xref:core/miscellaneous/logging).

Opcionalmente, en EF Core se permitía cambiar el comportamiento predeterminado para iniciar una excepción o no hacer nada al realizar la evaluación de cliente (excepto para la proyección). El comportamiento de inicio de excepción haría que fuese similar al de la versión 3.0. Para cambiar el comportamiento, debe configurar las advertencias al establecer las opciones del contexto, normalmente en `DbContext.OnConfiguring`, o bien en `Startup.cs` si usa ASP.NET Core.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
