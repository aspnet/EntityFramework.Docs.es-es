---
title: 'Transacciones: EF Core'
description: Administración de transacciones para obtener atomicidad al guardar datos con Entity Framework Core
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: b5e1fa2a0bcc466f22f03fee7ecaef9dcea1efaf
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003554"
---
# <a name="using-transactions"></a><span data-ttu-id="ef3cb-103">Usar transacciones</span><span class="sxs-lookup"><span data-stu-id="ef3cb-103">Using Transactions</span></span>

<span data-ttu-id="ef3cb-104">Las transacciones permiten procesar varias operaciones de base de datos de manera atómica.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="ef3cb-105">Si se confirma la transacción, todas las operaciones se aplicaron correctamente a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="ef3cb-106">Si se revierte la transacción, ninguna de las operaciones se aplicó a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]
> <span data-ttu-id="ef3cb-107">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="ef3cb-108">Comportamiento predeterminado de las transacciones</span><span class="sxs-lookup"><span data-stu-id="ef3cb-108">Default transaction behavior</span></span>

<span data-ttu-id="ef3cb-109">De manera predeterminada, si el proveedor de base de datos admite las transacciones, todos los cambios de una llamada sencilla a `SaveChanges` se aplican a una transacción.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges` are applied in a transaction.</span></span> <span data-ttu-id="ef3cb-110">Si cualquiera de los cambios presenta un error, la transacción se revertirá y no se aplicará ninguno de los cambios a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="ef3cb-111">Esto significa que se garantiza que `SaveChanges` se complete correctamente o deje sin modificaciones la base de datos si se produce un error.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-111">This means that `SaveChanges` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="ef3cb-112">Este comportamiento predeterminado es suficiente para la mayoría de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="ef3cb-113">Solo debe controlar manualmente las transacciones si los requisitos de la aplicación lo consideran necesario.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="ef3cb-114">Control de las transacciones</span><span class="sxs-lookup"><span data-stu-id="ef3cb-114">Controlling transactions</span></span>

<span data-ttu-id="ef3cb-115">Puede usar la API `DbContext.Database` para iniciar, confirmar y revertir las transacciones.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="ef3cb-116">En el ejemplo siguiente se muestran dos operaciones `SaveChanges` y una consulta LINQ que se ejecuta en una sola transacción:</span><span class="sxs-lookup"><span data-stu-id="ef3cb-116">The following example shows two `SaveChanges` operations and a LINQ query being executed in a single transaction:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

<span data-ttu-id="ef3cb-117">Si bien todos los proveedores de bases de datos relacionales admiten transacciones, otros tipos de proveedores pueden generar errores o no operar cuando se llama a las API de transacciones.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-117">While all relational database providers support transactions, other providers types may throw or no-op when transaction APIs are called.</span></span>

## <a name="savepoints"></a><span data-ttu-id="ef3cb-118">Puntos de retorno</span><span class="sxs-lookup"><span data-stu-id="ef3cb-118">Savepoints</span></span>

> [!NOTE]
> <span data-ttu-id="ef3cb-119">Esta característica se incluyó por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-119">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="ef3cb-120">Cuando se invoca a `SaveChanges` y ya hay una transacción en curso en el contexto, EF crea automáticamente un *punto de retorno* antes de guardar los datos.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-120">When `SaveChanges` is invoked and a transaction is already in progress on the context, EF automatically creates a *savepoint* before saving any data.</span></span> <span data-ttu-id="ef3cb-121">Los puntos de retorno son puntos dentro de una transacción de base de datos a los que se puede revertir más tarde en caso de que ocurra un error o por cualquier otro motivo.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-121">Savepoints are points within a database transaction which may later be rolled back to, if an error occurs or for any other reason.</span></span> <span data-ttu-id="ef3cb-122">Si `SaveChanges` encuentra algún error, revierte automáticamente la transacción al punto de retorno, y la transacción se mantiene en el mismo estado que si nunca se hubiera iniciado.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-122">If `SaveChanges` encounters any error, it automatically rolls the transaction back to the savepoint, leaving the transaction in the same state as if it had never started.</span></span> <span data-ttu-id="ef3cb-123">Esto le permite posiblemente corregir problemas y volver a intentar guardar, en particular cuando ocurren problemas de [simultaneidad optimista](xref:core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="ef3cb-123">This allows you to possibly correct issues and retry saving, in particular when [optimistic concurrency](xref:core/saving/concurrency) issues occur.</span></span>

> [!WARNING]
> <span data-ttu-id="ef3cb-124">Los puntos de retorno son incompatibles con los conjuntos de resultados activos múltiples de SQL Server y no se usan.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-124">Savepoints are incompatible with SQL Server's Multiple Active Result Sets, and are not used.</span></span> <span data-ttu-id="ef3cb-125">Si se produce un error durante `SaveChanges`, es posible que la transacción se quede en un estado desconocido.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-125">If an error occurs during `SaveChanges`, the transaction may be left in an unknown state.</span></span>

<span data-ttu-id="ef3cb-126">También es posible administrar los puntos de retorno de forma manual, del mismo modo que con las transacciones.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-126">It's also possible to manually manage savepoints, just as it is with transactions.</span></span> <span data-ttu-id="ef3cb-127">En el ejemplo siguiente se crea un punto de retorno dentro de una transacción y se revierte cuando se produce un error:</span><span class="sxs-lookup"><span data-stu-id="ef3cb-127">The following example creates a savepoint within a transaction, and rolls back to it on failure:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a><span data-ttu-id="ef3cb-128">Transacción entre contextos</span><span class="sxs-lookup"><span data-stu-id="ef3cb-128">Cross-context transaction</span></span>

<span data-ttu-id="ef3cb-129">También puede compartir una transacción en varias instancias de contexto.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-129">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="ef3cb-130">Esta funcionalidad solo está disponible cuando se usa un proveedor de base de datos relacionales porque requiere el uso de `DbTransaction` y `DbConnection`, específicos para las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-130">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="ef3cb-131">Para compartir una transacción, los contextos deben compartir tanto `DbConnection` como `DbTransaction`.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-131">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="ef3cb-132">Permitir conexiones proporcionadas externamente</span><span class="sxs-lookup"><span data-stu-id="ef3cb-132">Allow connection to be externally provided</span></span>

<span data-ttu-id="ef3cb-133">Compartir una `DbConnection` requiere la capacidad de pasar una conexión a un contexto cuando se construya.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-133">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="ef3cb-134">La manera más sencilla de permitir que `DbConnection` se proporcione de manera externa es dejar de usar el método `DbContext.OnConfiguring` para configurar el contexto y crear externamente `DbContextOptions` y pasarlas al constructor del contexto.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-134">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]
> <span data-ttu-id="ef3cb-135">`DbContextOptionsBuilder` es la API que usó en `DbContext.OnConfiguring` para configurar el contexto y ahora va a usarla para crear externamente `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-135">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="ef3cb-136">Una alternativa es seguir usando `DbContext.OnConfiguring`, pero aceptar una `DbConnection` que se guarda y luego se usa en `DbContext.OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-136">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

```csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a><span data-ttu-id="ef3cb-137">Compartir conexión y transacción</span><span class="sxs-lookup"><span data-stu-id="ef3cb-137">Share connection and transaction</span></span>

<span data-ttu-id="ef3cb-138">Ahora puede crear varias instancias de contexto que comparten la misma conexión.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-138">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="ef3cb-139">Luego use la API `DbContext.Database.UseTransaction(DbTransaction)` para inscribir ambos contextos en la misma transacción.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-139">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="ef3cb-140">Uso de DbTransactions externas (solo bases de datos relacionales)</span><span class="sxs-lookup"><span data-stu-id="ef3cb-140">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="ef3cb-141">Si usa varias tecnologías de acceso a datos para acceder a una base de datos relacional, es posible que quiera compartir una transacción entre las operaciones que estas distintas tecnologías realizan.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-141">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="ef3cb-142">En el ejemplo siguiente se muestra cómo realizar una operación SqlClient de ADO.NET y una operación de Entity Framework Core en la misma transacción.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-142">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a><span data-ttu-id="ef3cb-143">Utilizar System.Transactions</span><span class="sxs-lookup"><span data-stu-id="ef3cb-143">Using System.Transactions</span></span>

<span data-ttu-id="ef3cb-144">Es posible usar transacciones de ambiente si necesita coordinar en un ámbito mayor.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-144">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

<span data-ttu-id="ef3cb-145">También es posible inscribir en una transacción explícita.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-145">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="ef3cb-146">Limitaciones de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="ef3cb-146">Limitations of System.Transactions</span></span>

1. <span data-ttu-id="ef3cb-147">EF Core se basa en los proveedores de base de datos para implementar la compatibilidad con System.Transactions.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-147">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="ef3cb-148">Si un proveedor no implementa la compatibilidad con System.Transactions, es posible que las llamadas a estas API se omitan completamente.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-148">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="ef3cb-149">SqlClient lo admite.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-149">SqlClient supports it.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="ef3cb-150">Se recomienda probar que la API se comporte correctamente con el proveedor antes de usarla para administrar las transacciones.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-150">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="ef3cb-151">Si no es así, recomendamos que se ponga en contacto con el mantenedor del proveedor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-151">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="ef3cb-152">A partir de .NET Core 2.1, la implementación de System.Transactions en no incluye compatibilidad con transacciones distribuidas, por lo que no puede usar`TransactionScope` ni `CommittableTransaction` para coordinar las transacciones entre varios administradores de recursos.</span><span class="sxs-lookup"><span data-stu-id="ef3cb-152">As of .NET Core 2.1, the System.Transactions implementation does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
