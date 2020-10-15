---
title: 'Administración de conexiones: EF6'
description: Administración de conexiones en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e8c5c3615b4019be4c1915d3acaa91d60a55c85d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063301"
---
# <a name="connection-management"></a><span data-ttu-id="e0a3c-103">Administración de conexiones</span><span class="sxs-lookup"><span data-stu-id="e0a3c-103">Connection management</span></span>
<span data-ttu-id="e0a3c-104">En esta página se describe el comportamiento de Entity Framework con respecto a cómo pasar conexiones al contexto y la funcionalidad de la API **Database. Connection. Open ()** .</span><span class="sxs-lookup"><span data-stu-id="e0a3c-104">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="e0a3c-105">Pasar conexiones al contexto</span><span class="sxs-lookup"><span data-stu-id="e0a3c-105">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="e0a3c-106">Comportamiento de EF5 y versiones anteriores</span><span class="sxs-lookup"><span data-stu-id="e0a3c-106">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="e0a3c-107">Hay dos constructores que aceptan conexiones:</span><span class="sxs-lookup"><span data-stu-id="e0a3c-107">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="e0a3c-108">Es posible utilizarlos, pero tiene que solucionar un par de limitaciones:</span><span class="sxs-lookup"><span data-stu-id="e0a3c-108">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="e0a3c-109">Si pasa una conexión abierta a cualquiera de estas, la primera vez que el marco intenta utilizarla, se produce una excepción InvalidOperationException que indica que no puede volver a abrir una conexión que ya está abierta.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-109">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="e0a3c-110">La marca contextOwnsConnection se interpreta para indicar si la conexión del almacén subyacente debe desecharse cuando se desecha el contexto.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-110">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="e0a3c-111">Sin embargo, independientemente de esa configuración, la conexión del almacén siempre se cierra cuando se desecha el contexto.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-111">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="e0a3c-112">Por lo tanto, si tiene más de un DbContext con la misma conexión, el contexto que se elimine primero cerrará la conexión (de forma similar si ha mezclado una conexión ADO.NET existente con un DbContext, DbContext siempre cerrará la conexión cuando se elimine).</span><span class="sxs-lookup"><span data-stu-id="e0a3c-112">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="e0a3c-113">Es posible solucionar la primera limitación anterior si se pasa una conexión cerrada y solo se ejecuta código que la abriría una vez que se han creado todos los contextos:</span><span class="sxs-lookup"><span data-stu-id="e0a3c-113">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

<span data-ttu-id="e0a3c-114">La segunda limitación solo significa que debe abstenerse de desechar cualquier objeto DbContext hasta que esté listo para que se cierre la conexión.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-114">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="e0a3c-115">Comportamiento en EF6 y versiones futuras</span><span class="sxs-lookup"><span data-stu-id="e0a3c-115">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="e0a3c-116">En EF6 y versiones futuras, DbContext tiene los mismos dos constructores, pero ya no requiere que la conexión pasada al constructor se cierre cuando se reciba.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-116">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="e0a3c-117">Por lo tanto, ahora es posible:</span><span class="sxs-lookup"><span data-stu-id="e0a3c-117">So this is now possible:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

<span data-ttu-id="e0a3c-118">Además, la marca contextOwnsConnection controla ahora si la conexión se cierra y se desecha cuando se desecha el DbContext.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-118">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="e0a3c-119">Por lo tanto, en el ejemplo anterior, la conexión no se cierra cuando el contexto se desecha (línea 32) tal y como se encontraba en versiones anteriores de EF, sino cuando se desecha la propia conexión (línea 40).</span><span class="sxs-lookup"><span data-stu-id="e0a3c-119">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="e0a3c-120">Por supuesto, sigue siendo posible que DbContext tome el control de la conexión (solo tiene que establecer contextOwnsConnection en true o usar uno de los otros constructores) si así lo desea.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-120">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="e0a3c-121">Existen algunas consideraciones adicionales al usar transacciones con este nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-121">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="e0a3c-122">Para obtener más información, consulte [trabajar con transacciones](xref:ef6/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="e0a3c-122">For details see [Working with Transactions](xref:ef6/saving/transactions).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="e0a3c-123">Database. Connection. Open ()</span><span class="sxs-lookup"><span data-stu-id="e0a3c-123">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="e0a3c-124">Comportamiento de EF5 y versiones anteriores</span><span class="sxs-lookup"><span data-stu-id="e0a3c-124">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="e0a3c-125">En EF5 y versiones anteriores hay un error que indica que **ObjectContext. Connection. State** no se actualizó para reflejar el verdadero estado de la conexión del almacén subyacente.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-125">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="e0a3c-126">Por ejemplo, si ejecutó el código siguiente, se puede devolver el estado **cerrado** aunque de hecho la conexión del almacén subyacente esté **abierta**.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-126">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="e0a3c-127">Por separado, si abre la conexión de base de datos mediante una llamada a Database. Connection. Open (), se abrirá hasta la próxima vez que ejecute una consulta o llame a cualquier elemento que requiera una conexión de base de datos (por ejemplo, SaveChanges ()), pero después de que se cierre la conexión del almacén subyacente.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-127">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="e0a3c-128">El contexto volverá a abrir y a cerrar la conexión cada vez que se requiera otra operación de base de datos:</span><span class="sxs-lookup"><span data-stu-id="e0a3c-128">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="e0a3c-129">Comportamiento en EF6 y versiones futuras</span><span class="sxs-lookup"><span data-stu-id="e0a3c-129">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="e0a3c-130">En el caso de EF6 y versiones futuras, hemos tomado el enfoque que es si el código de llamada elige abrir la conexión llamando al contexto. Database. Connection. Open () tiene una buena razón para hacerlo y el marco asumirá que desea tener el control sobre la apertura y el cierre de la conexión y dejará de cerrar la conexión automáticamente.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-130">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="e0a3c-131">Esto puede dar lugar a conexiones que están abiertas durante mucho tiempo, por lo que debe usarse con cuidado.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-131">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="e0a3c-132">También se actualizó el código para que ObjectContext. Connection. State ahora realiza un seguimiento del estado de la conexión subyacente correctamente.</span><span class="sxs-lookup"><span data-stu-id="e0a3c-132">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
