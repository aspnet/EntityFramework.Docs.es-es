---
title: Transacciones - Core EF
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: a2f890c0af1e83cbcc1d40d68540ff7132a9bafd
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="using-transactions"></a><span data-ttu-id="72ea5-102">Uso de transacciones</span><span class="sxs-lookup"><span data-stu-id="72ea5-102">Using Transactions</span></span>

<span data-ttu-id="72ea5-103">Las transacciones permiten varias operaciones de base de datos debe procesarse de forma atómica.</span><span class="sxs-lookup"><span data-stu-id="72ea5-103">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="72ea5-104">Si la transacción se confirma, todas las operaciones se aplican correctamente a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="72ea5-104">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="72ea5-105">Si la transacción se revierte, ninguna de las operaciones se aplican a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="72ea5-105">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="72ea5-106">Puede ver este artículo [ejemplo](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="72ea5-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="72ea5-107">Comportamiento de transacciones predeterminado</span><span class="sxs-lookup"><span data-stu-id="72ea5-107">Default transaction behavior</span></span>

<span data-ttu-id="72ea5-108">De forma predeterminada, si el proveedor de base de datos admite transacciones, todos los cambios en una sola llamada a `SaveChanges()` se aplican en una transacción.</span><span class="sxs-lookup"><span data-stu-id="72ea5-108">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="72ea5-109">Si se produce un error en cualquiera de los cambios, la transacción se revierte y ninguno de los cambios se aplican a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="72ea5-109">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="72ea5-110">Esto significa que `SaveChanges()` se garantiza que terminen completamente correctamente o dejar la base de datos sin modificar si se produce un error.</span><span class="sxs-lookup"><span data-stu-id="72ea5-110">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="72ea5-111">Para la mayoría de las aplicaciones, este comportamiento predeterminado es suficiente.</span><span class="sxs-lookup"><span data-stu-id="72ea5-111">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="72ea5-112">Debe controlar solo manualmente las transacciones si sus requisitos de aplicación consideren necesario.</span><span class="sxs-lookup"><span data-stu-id="72ea5-112">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="72ea5-113">Control de transacciones</span><span class="sxs-lookup"><span data-stu-id="72ea5-113">Controlling transactions</span></span>

<span data-ttu-id="72ea5-114">Puede usar el `DbContext.Database` API para iniciar, confirmar y deshacer las transacciones.</span><span class="sxs-lookup"><span data-stu-id="72ea5-114">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="72ea5-115">En el ejemplo siguiente se muestra dos `SaveChanges()` operaciones y un LINQ de consulta que se está ejecutando en una sola transacción.</span><span class="sxs-lookup"><span data-stu-id="72ea5-115">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="72ea5-116">No todos los proveedores de base de datos admiten transacciones.</span><span class="sxs-lookup"><span data-stu-id="72ea5-116">Not all database providers support transactions.</span></span> <span data-ttu-id="72ea5-117">Algunos proveedores pueden producir o no-op cuando la transacción se denominan API.</span><span class="sxs-lookup"><span data-stu-id="72ea5-117">Some providers may throw or no-op when transaction APIs are called.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ControllingTransaction/Sample.cs?highlight=3,17,18,19)] -->
``` csharp
        using (var context = new BloggingContext())
        {
            using (var transaction = context.Database.BeginTransaction())
            {
                try
                {
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();

                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
                    context.SaveChanges();

                    var blogs = context.Blogs
                        .OrderBy(b => b.Url)
                        .ToList();

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="72ea5-118">Transacción de contexto cruzado (sólo bases de datos relacionales)</span><span class="sxs-lookup"><span data-stu-id="72ea5-118">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="72ea5-119">También puede compartir una transacción en varias instancias de contexto.</span><span class="sxs-lookup"><span data-stu-id="72ea5-119">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="72ea5-120">Esta funcionalidad solo está disponible cuando se utiliza un proveedor de base de datos relacional porque requiere el uso de `DbTransaction` y `DbConnection`, que son específicos de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="72ea5-120">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="72ea5-121">Para compartir una transacción, los contextos deben compartir tanto un `DbConnection` y `DbTransaction`.</span><span class="sxs-lookup"><span data-stu-id="72ea5-121">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="72ea5-122">Permitir conexión proporcionarse externamente</span><span class="sxs-lookup"><span data-stu-id="72ea5-122">Allow connection to be externally provided</span></span>

<span data-ttu-id="72ea5-123">Compartir un `DbConnection` requiere la capacidad de pasar una conexión a un contexto cuando se construye.</span><span class="sxs-lookup"><span data-stu-id="72ea5-123">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="72ea5-124">La manera más fácil para permitir `DbConnection` proporcionarse externamente, consiste en dejar de usar el `DbContext.OnConfiguring` método para configurar el contexto y crear externamente `DbContextOptions` y pasarlas al constructor de contexto.</span><span class="sxs-lookup"><span data-stu-id="72ea5-124">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="72ea5-125">`DbContextOptionsBuilder`es la API que se usan en `DbContext.OnConfiguring` para configurar el contexto, ahora va a usar de forma externa para crear `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="72ea5-125">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?highlight=3,4,5)] -->
``` csharp
    public class BloggingContext : DbContext
    {
        public BloggingContext(DbContextOptions<BloggingContext> options)
            : base(options)
        { }

        public DbSet<Blog> Blogs { get; set; }
    }
```

<span data-ttu-id="72ea5-126">Una alternativa consiste en seguir usando `DbContext.OnConfiguring`, pero que aceptan un `DbConnection` que se guarda y, a continuación, se usa en `DbContext.OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="72ea5-126">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="72ea5-127">Conexión de recurso compartido y transacciones</span><span class="sxs-lookup"><span data-stu-id="72ea5-127">Share connection and transaction</span></span>

<span data-ttu-id="72ea5-128">Ahora puede crear varias instancias de contexto que comparten la misma conexión.</span><span class="sxs-lookup"><span data-stu-id="72ea5-128">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="72ea5-129">A continuación, utilice el `DbContext.Database.UseTransaction(DbTransaction)` API para dar de alta ambos contextos en la misma transacción.</span><span class="sxs-lookup"><span data-stu-id="72ea5-129">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?highlight=1,2,3,7,16,23,24,25)] -->
``` csharp
        var options = new DbContextOptionsBuilder<BloggingContext>()
            .UseSqlServer(new SqlConnection(connectionString))
            .Options;

        using (var context1 = new BloggingContext(options))
        {
            using (var transaction = context1.Database.BeginTransaction())
            {
                try
                {
                    context1.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context1.SaveChanges();

                    using (var context2 = new BloggingContext(options))
                    {
                        context2.Database.UseTransaction(transaction.GetDbTransaction());

                        var blogs = context2.Blogs
                            .OrderBy(b => b.Url)
                            .ToList();
                    }

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="72ea5-130">Usar DbTransactions externos (solo bases de datos relacionales)</span><span class="sxs-lookup"><span data-stu-id="72ea5-130">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="72ea5-131">Si usas varias tecnologías de acceso a datos para tener acceso a una base de datos relacional, puede que desee compartir un a transacción entre las operaciones realizadas por estas tecnologías diferentes.</span><span class="sxs-lookup"><span data-stu-id="72ea5-131">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="72ea5-132">El ejemplo siguiente, se muestra cómo realizar una operación de SqlClient de ADO.NET y una operación de Entity Framework Core en la misma transacción.</span><span class="sxs-lookup"><span data-stu-id="72ea5-132">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?highlight=4,10,21,26,27,28)] -->
``` csharp
        var connection = new SqlConnection(connectionString);
        connection.Open();

        using (var transaction = connection.BeginTransaction())
        {
            try
            {
                // Run raw ADO.NET command in the transaction
                var command = connection.CreateCommand();
                command.Transaction = transaction;
                command.CommandText = "DELETE FROM dbo.Blogs";
                command.ExecuteNonQuery();

                // Run an EF Core command in the transaction
                var options = new DbContextOptionsBuilder<BloggingContext>()
                    .UseSqlServer(connection)
                    .Options;

                using (var context = new BloggingContext(options))
                {
                    context.Database.UseTransaction(transaction);
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();
                }

                // Commit transaction if all commands succeed, transaction will auto-rollback
                // when disposed if either commands fails
                transaction.Commit();
            }
            catch (System.Exception)
            {
                // TODO: Handle failure
            }
```