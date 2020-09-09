---
title: 'Resistencia de conexión: EF Core'
description: Uso de la resistencia de conexión para reintentar automáticamente los comandos con Entity Framework Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 6dd3d3eadb218ab32f373e44e2013d017e2966d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617774"
---
# <a name="connection-resiliency"></a><span data-ttu-id="3641e-103">Resistencia de conexión</span><span class="sxs-lookup"><span data-stu-id="3641e-103">Connection Resiliency</span></span>

<span data-ttu-id="3641e-104">La resistencia de conexión reintenta automáticamente los comandos de base de datos con errores.</span><span class="sxs-lookup"><span data-stu-id="3641e-104">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="3641e-105">La característica se puede usar con cualquier base de datos proporcionando una "estrategia de ejecución", que encapsula la lógica necesaria para detectar errores y volver a ejecutar los comandos.</span><span class="sxs-lookup"><span data-stu-id="3641e-105">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="3641e-106">EF Core proveedores pueden proporcionar estrategias de ejecución adaptadas a condiciones de error de base de datos específicas y directivas de reintentos óptimas.</span><span class="sxs-lookup"><span data-stu-id="3641e-106">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="3641e-107">Como ejemplo, el proveedor de SQL Server incluye una estrategia de ejecución que se adapta específicamente a SQL Server (incluido SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="3641e-107">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="3641e-108">Es consciente de los tipos de excepción que se pueden reintentar y tienen valores predeterminados razonables para el número máximo de reintentos, el retraso entre reintentos, etc.</span><span class="sxs-lookup"><span data-stu-id="3641e-108">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="3641e-109">Cuando se configuran las opciones para el contexto, se especifica una estrategia de ejecución.</span><span class="sxs-lookup"><span data-stu-id="3641e-109">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="3641e-110">Normalmente, se encuentra en el `OnConfiguring` método del contexto derivado:</span><span class="sxs-lookup"><span data-stu-id="3641e-110">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="3641e-111">o en `Startup.cs` para una aplicación ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="3641e-111">or in `Startup.cs` for an ASP.NET Core application:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="3641e-112">Estrategia de ejecución personalizada</span><span class="sxs-lookup"><span data-stu-id="3641e-112">Custom execution strategy</span></span>

<span data-ttu-id="3641e-113">Existe un mecanismo para registrar una estrategia de ejecución personalizada propia si desea cambiar cualquiera de los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="3641e-113">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="3641e-114">Estrategias de ejecución y transacciones</span><span class="sxs-lookup"><span data-stu-id="3641e-114">Execution strategies and transactions</span></span>

<span data-ttu-id="3641e-115">Una estrategia de ejecución que reintenta automáticamente los errores debe ser capaz de reproducir cada operación en un bloque de reintentos en el que se produce un error.</span><span class="sxs-lookup"><span data-stu-id="3641e-115">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="3641e-116">Cuando se habilitan los reintentos, cada operación que se realiza a través de EF Core se convierte en su propia operación admite reintentos.</span><span class="sxs-lookup"><span data-stu-id="3641e-116">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="3641e-117">Es decir, cada consulta y cada llamada a `SaveChanges()` se reintentará como una unidad si se produce un error transitorio.</span><span class="sxs-lookup"><span data-stu-id="3641e-117">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="3641e-118">Sin embargo, si el código inicia una transacción mediante `BeginTransaction()` la definición de su propio grupo de operaciones que deben tratarse como una unidad, y es necesario reproducir todo el contenido dentro de la transacción, se producirá un error.</span><span class="sxs-lookup"><span data-stu-id="3641e-118">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="3641e-119">Recibirá una excepción similar a la siguiente si intenta hacerlo al usar una estrategia de ejecución:</span><span class="sxs-lookup"><span data-stu-id="3641e-119">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="3641e-120">InvalidOperationException: la estrategia de ejecución configurada ' SqlServerRetryingExecutionStrategy ' no admite transacciones iniciadas por el usuario.</span><span class="sxs-lookup"><span data-stu-id="3641e-120">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="3641e-121">Use la estrategia de ejecución que devuelve "DbContext.Database.CreateExecutionStrategy()" para ejecutar todas las operaciones en la transacción como una unidad que se puede reintentar.</span><span class="sxs-lookup"><span data-stu-id="3641e-121">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="3641e-122">La solución consiste en invocar manualmente la estrategia de ejecución con un delegado que representa todo lo que se debe ejecutar.</span><span class="sxs-lookup"><span data-stu-id="3641e-122">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="3641e-123">Si se produce un error transitorio, la estrategia de ejecución invoca al delegado de nuevo.</span><span class="sxs-lookup"><span data-stu-id="3641e-123">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="3641e-124">Este enfoque también se puede usar con transacciones de ambiente.</span><span class="sxs-lookup"><span data-stu-id="3641e-124">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="3641e-125">Error de confirmación de la transacción y el problema Idempotencia</span><span class="sxs-lookup"><span data-stu-id="3641e-125">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="3641e-126">En general, cuando se produce un error de conexión, se revierte la transacción actual.</span><span class="sxs-lookup"><span data-stu-id="3641e-126">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="3641e-127">Sin embargo, si se quita la conexión mientras se confirma la transacción, se desconoce el estado resultante de la transacción.</span><span class="sxs-lookup"><span data-stu-id="3641e-127">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> 

<span data-ttu-id="3641e-128">De forma predeterminada, la estrategia de ejecución reintentará la operación como si la transacción se revirtió, pero si no es así, se producirá una excepción si el nuevo estado de la base de datos es incompatible o podría provocar daños en los **datos** si la operación no se basa en un estado determinado, por ejemplo, al insertar una nueva fila con valores de clave generados automáticamente.</span><span class="sxs-lookup"><span data-stu-id="3641e-128">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="3641e-129">Hay varias maneras de solucionar este error.</span><span class="sxs-lookup"><span data-stu-id="3641e-129">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="3641e-130">Opción 1: hacer (casi) nada</span><span class="sxs-lookup"><span data-stu-id="3641e-130">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="3641e-131">La probabilidad de que se produzca un error de conexión durante la confirmación de la transacción es baja, por lo que puede ser aceptable que la aplicación no funcione correctamente si esta condición se produce realmente.</span><span class="sxs-lookup"><span data-stu-id="3641e-131">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="3641e-132">Sin embargo, debe evitar el uso de claves generadas por el almacén para asegurarse de que se produce una excepción en lugar de agregar una fila duplicada.</span><span class="sxs-lookup"><span data-stu-id="3641e-132">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="3641e-133">Considere la posibilidad de usar un valor GUID generado por el cliente o un generador de valores del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="3641e-133">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="3641e-134">Opción 2: recompilar el estado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="3641e-134">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="3641e-135">Descartar el actual `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="3641e-135">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="3641e-136">Cree un nuevo `DbContext` y restaure el estado de la aplicación desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3641e-136">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="3641e-137">Informe al usuario de que es posible que la última operación no se haya completado correctamente.</span><span class="sxs-lookup"><span data-stu-id="3641e-137">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="3641e-138">Opción 3: agregar comprobación de estado</span><span class="sxs-lookup"><span data-stu-id="3641e-138">Option 3 - Add state verification</span></span>

<span data-ttu-id="3641e-139">Para la mayoría de las operaciones que cambian el estado de la base de datos es posible agregar código que comprueba si se ha realizado correctamente.</span><span class="sxs-lookup"><span data-stu-id="3641e-139">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="3641e-140">EF proporciona un método de extensión para facilitar esta tarea `IExecutionStrategy.ExecuteInTransaction` .</span><span class="sxs-lookup"><span data-stu-id="3641e-140">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="3641e-141">Este método inicia y confirma una transacción y también acepta una función en el `verifySucceeded` parámetro que se invoca cuando se produce un error transitorio durante la confirmación de la transacción.</span><span class="sxs-lookup"><span data-stu-id="3641e-141">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="3641e-142">Aquí `SaveChanges` se invoca con `acceptAllChangesOnSuccess` establecido en `false` para evitar cambiar el estado de la `Blog` entidad a si se `Unchanged` `SaveChanges` realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="3641e-142">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="3641e-143">Esto permite volver a intentar la misma operación si se produce un error en la confirmación y se revierte la transacción.</span><span class="sxs-lookup"><span data-stu-id="3641e-143">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="3641e-144">Opción 4: realizar un seguimiento manual de la transacción</span><span class="sxs-lookup"><span data-stu-id="3641e-144">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="3641e-145">Si necesita usar claves generadas por el almacén o necesita una manera genérica de controlar los errores de confirmación que no dependen de la operación realizada, se podría asignar a cada transacción un identificador que se comprueba cuando se produce un error en la confirmación.</span><span class="sxs-lookup"><span data-stu-id="3641e-145">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="3641e-146">Agregue una tabla a la base de datos utilizada para realizar el seguimiento del estado de las transacciones.</span><span class="sxs-lookup"><span data-stu-id="3641e-146">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="3641e-147">Inserte una fila en la tabla al principio de cada transacción.</span><span class="sxs-lookup"><span data-stu-id="3641e-147">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="3641e-148">Si se produce un error en la conexión durante la confirmación, Compruebe la presencia de la fila correspondiente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3641e-148">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="3641e-149">Si la confirmación se realiza correctamente, elimine la fila correspondiente para evitar el crecimiento de la tabla.</span><span class="sxs-lookup"><span data-stu-id="3641e-149">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="3641e-150">Asegúrese de que el contexto utilizado para la comprobación tenga definida una estrategia de ejecución, ya que es probable que se produzca un error en la conexión durante la comprobación si se produjo un error durante la confirmación de la transacción.</span><span class="sxs-lookup"><span data-stu-id="3641e-150">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
