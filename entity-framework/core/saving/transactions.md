---
title: 'Transacciones: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
uid: core/saving/transactions
ms.openlocfilehash: 390d89398ebfdf015804749e71ff0b61d3f278d3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413622"
---
# <a name="using-transactions"></a><span data-ttu-id="d8efa-102">Uso de las transacciones</span><span class="sxs-lookup"><span data-stu-id="d8efa-102">Using Transactions</span></span>

<span data-ttu-id="d8efa-103">Las transacciones permiten procesar varias operaciones de base de datos de manera atómica.</span><span class="sxs-lookup"><span data-stu-id="d8efa-103">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="d8efa-104">Si se confirma la transacción, todas las operaciones se aplicaron correctamente a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d8efa-104">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="d8efa-105">Si se revierte la transacción, ninguna de las operaciones se aplicó a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d8efa-105">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="d8efa-106">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="d8efa-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="d8efa-107">Comportamiento predeterminado de las transacciones</span><span class="sxs-lookup"><span data-stu-id="d8efa-107">Default transaction behavior</span></span>

<span data-ttu-id="d8efa-108">De manera predeterminada, si el proveedor de base de datos admite las transacciones, todos los cambios de una llamada sencilla a `SaveChanges()` se aplican a una transacción.</span><span class="sxs-lookup"><span data-stu-id="d8efa-108">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="d8efa-109">Si cualquiera de los cambios presenta un error, la transacción se revertirá y no se aplicará ninguno de los cambios a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d8efa-109">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="d8efa-110">Esto significa que se garantiza que `SaveChanges()` se complete correctamente o deje sin modificaciones la base de datos si se produce un error.</span><span class="sxs-lookup"><span data-stu-id="d8efa-110">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="d8efa-111">Este comportamiento predeterminado es suficiente para la mayoría de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="d8efa-111">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="d8efa-112">Solo debe controlar manualmente las transacciones si los requisitos de la aplicación lo consideran necesario.</span><span class="sxs-lookup"><span data-stu-id="d8efa-112">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="d8efa-113">Control de las transacciones</span><span class="sxs-lookup"><span data-stu-id="d8efa-113">Controlling transactions</span></span>

<span data-ttu-id="d8efa-114">Puede usar la API `DbContext.Database` para iniciar, confirmar y revertir las transacciones.</span><span class="sxs-lookup"><span data-stu-id="d8efa-114">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="d8efa-115">En el ejemplo siguiente se muestran dos operaciones `SaveChanges()` y una consulta LINQ que se ejecuta en una sola transacción.</span><span class="sxs-lookup"><span data-stu-id="d8efa-115">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="d8efa-116">No todos los proveedores de base de datos admiten transacciones.</span><span class="sxs-lookup"><span data-stu-id="d8efa-116">Not all database providers support transactions.</span></span> <span data-ttu-id="d8efa-117">Algunos proveedores pueden generar una excepción o una operación no efectiva cuando se llaman a las API de transacción.</span><span class="sxs-lookup"><span data-stu-id="d8efa-117">Some providers may throw or no-op when transaction APIs are called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="d8efa-118">Transacción de contexto cruzado (solo bases de datos relacionales)</span><span class="sxs-lookup"><span data-stu-id="d8efa-118">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="d8efa-119">También puede compartir una transacción en varias instancias de contexto.</span><span class="sxs-lookup"><span data-stu-id="d8efa-119">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="d8efa-120">Esta funcionalidad solo está disponible cuando se usa un proveedor de base de datos relacionales porque requiere el uso de `DbTransaction` y `DbConnection`, específicos para las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="d8efa-120">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="d8efa-121">Para compartir una transacción, los contextos deben compartir tanto `DbConnection` como `DbTransaction`.</span><span class="sxs-lookup"><span data-stu-id="d8efa-121">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="d8efa-122">Permitir conexiones proporcionadas externamente</span><span class="sxs-lookup"><span data-stu-id="d8efa-122">Allow connection to be externally provided</span></span>

<span data-ttu-id="d8efa-123">Compartir una `DbConnection` requiere la capacidad de pasar una conexión a un contexto cuando se construya.</span><span class="sxs-lookup"><span data-stu-id="d8efa-123">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="d8efa-124">La manera más sencilla de permitir que `DbConnection` se proporcione de manera externa es dejar de usar el método `DbContext.OnConfiguring` para configurar el contexto y crear externamente `DbContextOptions` y pasarlas al constructor del contexto.</span><span class="sxs-lookup"><span data-stu-id="d8efa-124">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="d8efa-125">`DbContextOptionsBuilder` es la API que usó en `DbContext.OnConfiguring` para configurar el contexto y ahora va a usarla para crear externamente `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="d8efa-125">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="d8efa-126">Una alternativa es seguir usando `DbContext.OnConfiguring`, pero aceptar una `DbConnection` que se guarda y luego se usa en `DbContext.OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="d8efa-126">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

``` csharp
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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="d8efa-127">Compartir conexión y transacción</span><span class="sxs-lookup"><span data-stu-id="d8efa-127">Share connection and transaction</span></span>

<span data-ttu-id="d8efa-128">Ahora puede crear varias instancias de contexto que comparten la misma conexión.</span><span class="sxs-lookup"><span data-stu-id="d8efa-128">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="d8efa-129">Luego use la API `DbContext.Database.UseTransaction(DbTransaction)` para inscribir ambos contextos en la misma transacción.</span><span class="sxs-lookup"><span data-stu-id="d8efa-129">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="d8efa-130">Uso de DbTransactions externas (solo bases de datos relacionales)</span><span class="sxs-lookup"><span data-stu-id="d8efa-130">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="d8efa-131">Si usa varias tecnologías de acceso a datos para acceder a una base de datos relacional, es posible que quiera compartir una transacción entre las operaciones que estas distintas tecnologías realizan.</span><span class="sxs-lookup"><span data-stu-id="d8efa-131">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="d8efa-132">En el ejemplo siguiente se muestra cómo realizar una operación SqlClient de ADO.NET y una operación de Entity Framework Core en la misma transacción.</span><span class="sxs-lookup"><span data-stu-id="d8efa-132">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="d8efa-133">Uso de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="d8efa-133">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="d8efa-134">Esta característica es nueva en EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="d8efa-134">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="d8efa-135">Es posible usar transacciones de ambiente si necesita coordinar en un ámbito mayor.</span><span class="sxs-lookup"><span data-stu-id="d8efa-135">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

<span data-ttu-id="d8efa-136">También es posible inscribir en una transacción explícita.</span><span class="sxs-lookup"><span data-stu-id="d8efa-136">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="d8efa-137">Limitaciones de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="d8efa-137">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="d8efa-138">EF Core se basa en los proveedores de base de datos para implementar la compatibilidad con System.Transactions.</span><span class="sxs-lookup"><span data-stu-id="d8efa-138">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="d8efa-139">Si bien la compatibilidad es bastante común entre los proveedores de ADO.NET para .NET Framework, la API solo se agregó recientemente a .NET Core y, por tanto, la compatibilidad no siempre está tan extendida.</span><span class="sxs-lookup"><span data-stu-id="d8efa-139">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not as widespread.</span></span> <span data-ttu-id="d8efa-140">Si un proveedor no implementa la compatibilidad con System.Transactions, es posible que las llamadas a estas API se omitan completamente.</span><span class="sxs-lookup"><span data-stu-id="d8efa-140">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="d8efa-141">SqlClient para .NET no lo admite desde la versión 2.1 en adelante.</span><span class="sxs-lookup"><span data-stu-id="d8efa-141">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="d8efa-142">SqlClient para .NET Core 2.0 generará una excepción si intent usar la característica.</span><span class="sxs-lookup"><span data-stu-id="d8efa-142">SqlClient for .NET Core 2.0 will throw an exception if you attempt to use the feature.</span></span>

   > [!IMPORTANT]  
   > <span data-ttu-id="d8efa-143">Se recomienda probar que la API se comporte correctamente con el proveedor antes de usarla para administrar las transacciones.</span><span class="sxs-lookup"><span data-stu-id="d8efa-143">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="d8efa-144">Si no es así, recomendamos que se ponga en contacto con el mantenedor del proveedor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="d8efa-144">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="d8efa-145">A partir de la versión 2.1, la implementación de System.Transactions en .NET Core no incluye compatibilidad con transacciones distribuidas, por lo que no puede usar`TransactionScope` ni `CommittableTransaction` para coordinar las transacciones entre varios administradores de recursos.</span><span class="sxs-lookup"><span data-stu-id="d8efa-145">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
