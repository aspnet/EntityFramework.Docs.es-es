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
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="8f1b8-103">Resistencia de conexión y lógica de reintento</span><span class="sxs-lookup"><span data-stu-id="8f1b8-103">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="8f1b8-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="8f1b8-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="8f1b8-106">Las aplicaciones que se conectan a un servidor de base de datos siempre han sido vulnerables a las interrupciones de conexión debido a errores de back-end y a la inestabilidad de red.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-106">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="8f1b8-107">Sin embargo, en un entorno basado en LAN que trabaja con servidores de bases de datos dedicados, estos errores son lo suficientemente raros como para que no se requiera la lógica adicional para controlar esos errores.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-107">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="8f1b8-108">Con el aumento de los servidores de bases de datos basados en la nube, como Windows Azure SQL Database y las conexiones a través de redes menos confiables, ahora es más común que se produzcan interrupciones de conexión.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-108">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="8f1b8-109">Esto puede deberse a técnicas defensivas que usan las bases de datos en la nube para garantizar la equidad del servicio, como la limitación de la conexión o la inestabilidad en la red, lo que produce tiempos de espera intermitentes y otros errores transitorios.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-109">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="8f1b8-110">La resistencia de la conexión hace referencia a la capacidad de EF de reintentar automáticamente cualquier comando que produzca un error debido a estas interrupciones de conexión.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-110">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="8f1b8-111">Estrategias de ejecución</span><span class="sxs-lookup"><span data-stu-id="8f1b8-111">Execution Strategies</span></span>  

<span data-ttu-id="8f1b8-112">El reintento de conexión se realiza a través de una implementación de la interfaz IDbExecutionStrategy.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-112">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="8f1b8-113">Las implementaciones de IDbExecutionStrategy serán responsables de aceptar una operación y, si se produce una excepción, determinar si es adecuado y volver a intentar si es así.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-113">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="8f1b8-114">Hay cuatro estrategias de ejecución que se incluyen con EF:</span><span class="sxs-lookup"><span data-stu-id="8f1b8-114">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="8f1b8-115">**DefaultExecutionStrategy**: esta estrategia de ejecución no vuelve a intentar ninguna operación, es el valor predeterminado para las bases de datos que no sean SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-115">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="8f1b8-116">**DefaultSqlExecutionStrategy**: esta es una estrategia de ejecución interna que se utiliza de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-116">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="8f1b8-117">Sin embargo, esta estrategia no vuelve a intentarlo, sino que encapsulará las excepciones que podrían ser transitorias para informar a los usuarios de que pueden querer habilitar la resistencia de la conexión.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-117">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="8f1b8-118">**DbExecutionStrategy**: esta clase es adecuada como una clase base para otras estrategias de ejecución, incluidas sus propias personalizaciones.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-118">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="8f1b8-119">Implementa una directiva de reintentos exponencial, donde el reintento inicial se produce con un retraso de cero y el retraso aumenta exponencialmente hasta que se alcanza el número máximo de reintentos.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-119">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="8f1b8-120">Esta clase tiene un método ShouldRetryOn abstracto que se puede implementar en estrategias de ejecución derivadas para controlar las excepciones que se deben Reintentar.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-120">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="8f1b8-121">**SqlAzureExecutionStrategy**: esta estrategia de ejecución hereda de DbExecutionStrategy y volverá a intentarlo en las excepciones que se sabe que son posiblemente transitorias al trabajar con Azure SQL Database.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-121">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="8f1b8-122">Las estrategias de ejecución 2 y 4 se incluyen en el proveedor de SQL Server que se incluye con EF, que está en el ensamblado EntityFramework. SqlServer y están diseñados para funcionar con SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-122">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="8f1b8-123">Habilitar una estrategia de ejecución</span><span class="sxs-lookup"><span data-stu-id="8f1b8-123">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="8f1b8-124">La manera más sencilla de indicar a EF que use una estrategia de ejecución es con el método SetExecutionStrategy de la clase [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) :</span><span class="sxs-lookup"><span data-stu-id="8f1b8-124">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="8f1b8-125">Este código indica a EF que use SqlAzureExecutionStrategy al conectarse a SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-125">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="8f1b8-126">Configuración de la estrategia de ejecución</span><span class="sxs-lookup"><span data-stu-id="8f1b8-126">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="8f1b8-127">El constructor de SqlAzureExecutionStrategy puede aceptar dos parámetros, MaxRetryCount y MaxDelay.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-127">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="8f1b8-128">MaxRetry Count es el número máximo de veces que se reintentará la estrategia.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-128">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="8f1b8-129">MaxDelay es un intervalo de tiempo que representa el retraso máximo entre los reintentos que usará la estrategia de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-129">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="8f1b8-130">Para establecer el número máximo de reintentos en 1 y el retraso máximo en 30 segundos, ejecutaría lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8f1b8-130">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execute the following:</span></span>  

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

<span data-ttu-id="8f1b8-131">El SqlAzureExecutionStrategy se volverá a intentar de inmediato la primera vez que se produzca un error transitorio, pero se retrasará más tiempo entre cada reintento hasta que se supere el límite máximo de reintentos o hasta que el tiempo total alcanza el retraso máximo.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-131">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="8f1b8-132">Las estrategias de ejecución solo volverán a intentar un número limitado de excepciones que normalmente son transitorias; todavía tendrá que controlar otros errores, así como detectar la excepción RetryLimitExceeded en caso de que un error no sea transitorio o tarde demasiado en resolverse.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-132">The execution strategies will only retry a limited number of exceptions that are usually transient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="8f1b8-133">Hay algunas limitaciones conocidas al usar una estrategia de ejecución de reintento:</span><span class="sxs-lookup"><span data-stu-id="8f1b8-133">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="8f1b8-134">No se admiten las consultas de streaming</span><span class="sxs-lookup"><span data-stu-id="8f1b8-134">Streaming queries are not supported</span></span>  

<span data-ttu-id="8f1b8-135">De forma predeterminada, EF6 y versiones posteriores almacenarán en búfer los resultados de la consulta en lugar de transmitirlos por secuencias.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-135">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="8f1b8-136">Si desea que los resultados se transmitan, puede usar el método asstreaming para cambiar una consulta de LINQ to Entities a streaming.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-136">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="8f1b8-137">No se admite la transmisión por secuencias cuando se registra una estrategia de ejecución de reintento.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-137">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="8f1b8-138">Esta limitación existe porque la conexión podría quitar parte de los resultados que se devuelven.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-138">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="8f1b8-139">Cuando esto ocurre, EF debe volver a ejecutar toda la consulta pero no tiene una forma confiable de saber qué resultados se han devuelto (los datos pueden haber cambiado desde que se envió la consulta inicial, por lo que los resultados pueden volver a un orden diferente, los resultados pueden no tener un identificador único, etc.).</span><span class="sxs-lookup"><span data-stu-id="8f1b8-139">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="8f1b8-140">No se admiten las transacciones iniciadas por el usuario</span><span class="sxs-lookup"><span data-stu-id="8f1b8-140">User initiated transactions are not supported</span></span>  

<span data-ttu-id="8f1b8-141">Cuando haya configurado una estrategia de ejecución que tenga como resultado reintentos, hay algunas limitaciones sobre el uso de transacciones.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-141">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="8f1b8-142">De forma predeterminada, EF realizará cualquier actualización de la base de datos dentro de una transacción.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-142">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="8f1b8-143">No es necesario hacer nada para habilitarlo; EF siempre lo hace automáticamente.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-143">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="8f1b8-144">Por ejemplo, en el código siguiente, se realiza automáticamente SaveChanges en una transacción.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-144">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="8f1b8-145">Si se produjera un error de SaveChanges después de insertar uno de los nuevos sitios, la transacción se revertiría y no se aplicaría ningún cambio en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-145">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="8f1b8-146">El contexto también se deja en un estado que permite llamar a SaveChanges de nuevo para volver a aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-146">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="8f1b8-147">Cuando no se usa una estrategia de ejecución de reintento, se pueden ajustar varias operaciones en una sola transacción.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-147">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="8f1b8-148">Por ejemplo, el código siguiente ajusta dos llamadas de SaveChanges en una sola transacción.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-148">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="8f1b8-149">Si se produce un error en cualquier parte de cualquiera de las operaciones, no se aplica ninguno de los cambios.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-149">If any part of either operation fails then none of the changes are applied.</span></span>  

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

<span data-ttu-id="8f1b8-150">Esto no se admite cuando se usa una estrategia de ejecución de reintento porque EF no es consciente de las operaciones anteriores y cómo volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-150">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="8f1b8-151">Por ejemplo, si se produce un error en el segundo SaveChanges, EF ya no tiene la información necesaria para volver a intentar la primera llamada a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-151">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="solution-manually-call-execution-strategy"></a><span data-ttu-id="8f1b8-152">Solución: estrategia de ejecución de llamadas manualmente</span><span class="sxs-lookup"><span data-stu-id="8f1b8-152">Solution: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="8f1b8-153">La solución consiste en usar manualmente la estrategia de ejecución y darle todo el conjunto de lógica que se va a ejecutar, de modo que pueda volver a intentar todo lo posible si se produce un error en una de las operaciones.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-153">The solution is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="8f1b8-154">Cuando una estrategia de ejecución derivada de DbExecutionStrategy se está ejecutando, suspenderá la estrategia de ejecución implícita usada en SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-154">When an execution strategy derived from DbExecutionStrategy is running it will suspend the implicit execution strategy used in SaveChanges.</span></span>  

<span data-ttu-id="8f1b8-155">Tenga en cuenta que los contextos deben construirse dentro del bloque de código para que se vuelva a intentar.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-155">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="8f1b8-156">Esto garantiza que se está iniciando con un estado limpio para cada reintento.</span><span class="sxs-lookup"><span data-stu-id="8f1b8-156">This ensures that we are starting with a clean state for each retry.</span></span>  

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
