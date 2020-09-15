---
title: 'Resistencia de conexión y lógica de reintento: EF6'
description: Resistencia de conexión y lógica de reintento en Entity Framework 6
author: AndriySvyryd
ms.date: 11/20/2019
uid: ef6/fundamentals/connection-resiliency/retry-logic
ms.openlocfilehash: 3e71e973be5a23c86f873e9adf1bf00f4b6def58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073193"
---
# <a name="connection-resiliency-and-retry-logic"></a>Resistencia de conexión y lógica de reintento
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

Las aplicaciones que se conectan a un servidor de base de datos siempre han sido vulnerables a las interrupciones de conexión debido a errores de back-end y a la inestabilidad de red. Sin embargo, en un entorno basado en LAN que trabaja con servidores de bases de datos dedicados, estos errores son lo suficientemente raros como para que no se requiera la lógica adicional para controlar esos errores. Con el aumento de los servidores de bases de datos basados en la nube, como Windows Azure SQL Database y las conexiones a través de redes menos confiables, ahora es más común que se produzcan interrupciones de conexión. Esto puede deberse a técnicas defensivas que usan las bases de datos en la nube para garantizar la equidad del servicio, como la limitación de la conexión o la inestabilidad en la red, lo que produce tiempos de espera intermitentes y otros errores transitorios.  

La resistencia de la conexión hace referencia a la capacidad de EF de reintentar automáticamente cualquier comando que produzca un error debido a estas interrupciones de conexión.  

## <a name="execution-strategies"></a>Estrategias de ejecución  

El reintento de conexión se realiza a través de una implementación de la interfaz IDbExecutionStrategy. Las implementaciones de IDbExecutionStrategy serán responsables de aceptar una operación y, si se produce una excepción, determinar si es adecuado y volver a intentar si es así. Hay cuatro estrategias de ejecución que se incluyen con EF:  

1. **DefaultExecutionStrategy**: esta estrategia de ejecución no vuelve a intentar ninguna operación, es el valor predeterminado para las bases de datos que no sean SQL Server.  
2. **DefaultSqlExecutionStrategy**: esta es una estrategia de ejecución interna que se utiliza de forma predeterminada. Sin embargo, esta estrategia no vuelve a intentarlo, sino que encapsulará las excepciones que podrían ser transitorias para informar a los usuarios de que pueden querer habilitar la resistencia de la conexión.  
3. **DbExecutionStrategy**: esta clase es adecuada como una clase base para otras estrategias de ejecución, incluidas sus propias personalizaciones. Implementa una directiva de reintentos exponencial, donde el reintento inicial se produce con un retraso de cero y el retraso aumenta exponencialmente hasta que se alcanza el número máximo de reintentos. Esta clase tiene un método ShouldRetryOn abstracto que se puede implementar en estrategias de ejecución derivadas para controlar las excepciones que se deben Reintentar.  
4. **SqlAzureExecutionStrategy**: esta estrategia de ejecución hereda de DbExecutionStrategy y volverá a intentarlo en las excepciones que se sabe que son posiblemente transitorias al trabajar con Azure SQL Database.

> [!NOTE]
> Las estrategias de ejecución 2 y 4 se incluyen en el proveedor de SQL Server que se incluye con EF, que está en el ensamblado EntityFramework. SqlServer y están diseñados para funcionar con SQL Server.  

## <a name="enabling-an-execution-strategy"></a>Habilitar una estrategia de ejecución  

La manera más sencilla de indicar a EF que use una estrategia de ejecución es con el método SetExecutionStrategy de la clase [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) :  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

Este código indica a EF que use SqlAzureExecutionStrategy al conectarse a SQL Server.  

## <a name="configuring-the-execution-strategy"></a>Configuración de la estrategia de ejecución  

El constructor de SqlAzureExecutionStrategy puede aceptar dos parámetros, MaxRetryCount y MaxDelay. MaxRetry Count es el número máximo de veces que se reintentará la estrategia. MaxDelay es un intervalo de tiempo que representa el retraso máximo entre los reintentos que usará la estrategia de ejecución.  

Para establecer el número máximo de reintentos en 1 y el retraso máximo en 30 segundos, ejecutaría lo siguiente:  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

El SqlAzureExecutionStrategy se volverá a intentar de inmediato la primera vez que se produzca un error transitorio, pero se retrasará más tiempo entre cada reintento hasta que se supere el límite máximo de reintentos o hasta que el tiempo total alcanza el retraso máximo.  

Las estrategias de ejecución solo volverán a intentar un número limitado de excepciones que normalmente son transitorias; todavía tendrá que controlar otros errores, así como detectar la excepción RetryLimitExceeded en caso de que un error no sea transitorio o tarde demasiado en resolverse.  

Hay algunas limitaciones conocidas al usar una estrategia de ejecución de reintento:  

## <a name="streaming-queries-are-not-supported"></a>No se admiten las consultas de streaming  

De forma predeterminada, EF6 y versiones posteriores almacenarán en búfer los resultados de la consulta en lugar de transmitirlos por secuencias. Si desea que los resultados se transmitan, puede usar el método asstreaming para cambiar una consulta de LINQ to Entities a streaming.  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

No se admite la transmisión por secuencias cuando se registra una estrategia de ejecución de reintento. Esta limitación existe porque la conexión podría quitar parte de los resultados que se devuelven. Cuando esto ocurre, EF debe volver a ejecutar toda la consulta pero no tiene una forma confiable de saber qué resultados se han devuelto (los datos pueden haber cambiado desde que se envió la consulta inicial, por lo que los resultados pueden volver a un orden diferente, los resultados pueden no tener un identificador único, etc.).  

## <a name="user-initiated-transactions-are-not-supported"></a>No se admiten las transacciones iniciadas por el usuario  

Cuando haya configurado una estrategia de ejecución que tenga como resultado reintentos, hay algunas limitaciones sobre el uso de transacciones.  

De forma predeterminada, EF realizará cualquier actualización de la base de datos dentro de una transacción. No es necesario hacer nada para habilitarlo; EF siempre lo hace automáticamente.  

Por ejemplo, en el código siguiente, se realiza automáticamente SaveChanges en una transacción. Si se produjera un error de SaveChanges después de insertar uno de los nuevos sitios, la transacción se revertiría y no se aplicaría ningún cambio en la base de datos. El contexto también se deja en un estado que permite llamar a SaveChanges de nuevo para volver a aplicar los cambios.  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

Cuando no se usa una estrategia de ejecución de reintento, se pueden ajustar varias operaciones en una sola transacción. Por ejemplo, el código siguiente ajusta dos llamadas de SaveChanges en una sola transacción. Si se produce un error en cualquier parte de cualquiera de las operaciones, no se aplica ninguno de los cambios.  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

Esto no se admite cuando se usa una estrategia de ejecución de reintento porque EF no es consciente de las operaciones anteriores y cómo volver a intentarlo. Por ejemplo, si se produce un error en el segundo SaveChanges, EF ya no tiene la información necesaria para volver a intentar la primera llamada a SaveChanges.  

### <a name="solution-manually-call-execution-strategy"></a>Solución: estrategia de ejecución de llamadas manualmente  

La solución consiste en usar manualmente la estrategia de ejecución y darle todo el conjunto de lógica que se va a ejecutar, de modo que pueda volver a intentar todo lo posible si se produce un error en una de las operaciones. Cuando una estrategia de ejecución derivada de DbExecutionStrategy se está ejecutando, suspenderá la estrategia de ejecución implícita usada en SaveChanges.  

Tenga en cuenta que los contextos deben construirse dentro del bloque de código para que se vuelva a intentar. Esto garantiza que se está iniciando con un estado limpio para cada reintento.  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });
```  
