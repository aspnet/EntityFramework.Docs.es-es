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
# <a name="connection-management"></a>Administración de conexiones
En esta página se describe el comportamiento de Entity Framework con respecto a cómo pasar conexiones al contexto y la funcionalidad de la API **Database. Connection. Open ()** .  

## <a name="passing-connections-to-the-context"></a>Pasar conexiones al contexto  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Comportamiento de EF5 y versiones anteriores  

Hay dos constructores que aceptan conexiones:  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

Es posible utilizarlos, pero tiene que solucionar un par de limitaciones:  

1. Si pasa una conexión abierta a cualquiera de estas, la primera vez que el marco intenta utilizarla, se produce una excepción InvalidOperationException que indica que no puede volver a abrir una conexión que ya está abierta.  
2. La marca contextOwnsConnection se interpreta para indicar si la conexión del almacén subyacente debe desecharse cuando se desecha el contexto. Sin embargo, independientemente de esa configuración, la conexión del almacén siempre se cierra cuando se desecha el contexto. Por lo tanto, si tiene más de un DbContext con la misma conexión, el contexto que se elimine primero cerrará la conexión (de forma similar si ha mezclado una conexión ADO.NET existente con un DbContext, DbContext siempre cerrará la conexión cuando se elimine).  

Es posible solucionar la primera limitación anterior si se pasa una conexión cerrada y solo se ejecuta código que la abriría una vez que se han creado todos los contextos:  

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

La segunda limitación solo significa que debe abstenerse de desechar cualquier objeto DbContext hasta que esté listo para que se cierre la conexión.  

### <a name="behavior-in-ef6-and-future-versions"></a>Comportamiento en EF6 y versiones futuras  

En EF6 y versiones futuras, DbContext tiene los mismos dos constructores, pero ya no requiere que la conexión pasada al constructor se cierre cuando se reciba. Por lo tanto, ahora es posible:  

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

Además, la marca contextOwnsConnection controla ahora si la conexión se cierra y se desecha cuando se desecha el DbContext. Por lo tanto, en el ejemplo anterior, la conexión no se cierra cuando el contexto se desecha (línea 32) tal y como se encontraba en versiones anteriores de EF, sino cuando se desecha la propia conexión (línea 40).  

Por supuesto, sigue siendo posible que DbContext tome el control de la conexión (solo tiene que establecer contextOwnsConnection en true o usar uno de los otros constructores) si así lo desea.  

> [!NOTE]
> Existen algunas consideraciones adicionales al usar transacciones con este nuevo modelo. Para obtener más información, consulte [trabajar con transacciones](xref:ef6/saving/transactions).  

## <a name="databaseconnectionopen"></a>Database. Connection. Open ()  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Comportamiento de EF5 y versiones anteriores  

En EF5 y versiones anteriores hay un error que indica que **ObjectContext. Connection. State** no se actualizó para reflejar el verdadero estado de la conexión del almacén subyacente. Por ejemplo, si ejecutó el código siguiente, se puede devolver el estado **cerrado** aunque de hecho la conexión del almacén subyacente esté **abierta**.  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

Por separado, si abre la conexión de base de datos mediante una llamada a Database. Connection. Open (), se abrirá hasta la próxima vez que ejecute una consulta o llame a cualquier elemento que requiera una conexión de base de datos (por ejemplo, SaveChanges ()), pero después de que se cierre la conexión del almacén subyacente. El contexto volverá a abrir y a cerrar la conexión cada vez que se requiera otra operación de base de datos:  

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

### <a name="behavior-in-ef6-and-future-versions"></a>Comportamiento en EF6 y versiones futuras  

En el caso de EF6 y versiones futuras, hemos tomado el enfoque que es si el código de llamada elige abrir la conexión llamando al contexto. Database. Connection. Open () tiene una buena razón para hacerlo y el marco asumirá que desea tener el control sobre la apertura y el cierre de la conexión y dejará de cerrar la conexión automáticamente.  

> [!NOTE]
> Esto puede dar lugar a conexiones que están abiertas durante mucho tiempo, por lo que debe usarse con cuidado.  

También se actualizó el código para que ObjectContext. Connection. State ahora realiza un seguimiento del estado de la conexión subyacente correctamente.  

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
