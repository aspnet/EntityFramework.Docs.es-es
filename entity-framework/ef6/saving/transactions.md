---
title: 'Trabajo con transacciones: EF6'
description: Trabajo con transacciones en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 525b5cf605c1b61225ee2b9f1e0559a8e13f3052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064437"
---
# <a name="working-with-transactions"></a>Trabajar con transacciones
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

En este documento se describe el uso de transacciones en EF6, incluidas las mejoras que hemos agregado desde EF5 para facilitar el trabajo con transacciones.  

## <a name="what-ef-does-by-default"></a>Qué hace EF de forma predeterminada  

En todas las versiones de Entity Framework, siempre que ejecute **SaveChanges ()** para insertar, actualizar o eliminar en la base de datos, el marco de trabajo encapsulará esa operación en una transacción. Esta transacción dura solo el tiempo suficiente para ejecutar la operación y, a continuación, se completa. Al ejecutar otra operación de este tipo, se inicia una nueva transacción.  

A partir de EF6 **Database.ExecuteSqlCommand ()** , de forma predeterminada, se ajustará el comando en una transacción si aún no hay ninguna presente. Hay sobrecargas de este método que le permiten invalidar este comportamiento si lo desea. Además, en la ejecución EF6 de procedimientos almacenados incluidos en el modelo a través de API como **ObjectContext.ExecuteFunction ()** hace lo mismo (salvo que no se puede reemplazar el comportamiento predeterminado en ese momento).  

En cualquier caso, el nivel de aislamiento de la transacción es cualquier nivel de aislamiento en el que el proveedor de base de datos considere su configuración predeterminada. De forma predeterminada, por ejemplo, en SQL Server este es READ COMMITTED.  

Entity Framework no encapsula las consultas en una transacción.  

Esta funcionalidad predeterminada es adecuada para muchos usuarios y, si es así, no es necesario hacer nada diferente en EF6; solo tiene que escribir el código como lo hizo siempre.  

Sin embargo, algunos usuarios requieren un mayor control sobre sus transacciones; esto se trata en las secciones siguientes.  

## <a name="how-the-apis-work"></a>Cómo funcionan las API  

Antes de EF6 Entity Framework insista en abrir la propia conexión de base de datos (se produjo una excepción si se pasa una conexión que ya estaba abierta). Puesto que una transacción solo se puede iniciar en una conexión abierta, esto significaba que la única forma en que un usuario podía encapsular varias operaciones en una transacción era usar [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) o usar la propiedad **ObjectContext. Connection** y comenzar a llamar a **Open ()** y **BeginTransaction ()** directamente en el objeto **EntityConnection** devuelto. Además, las llamadas API que contacten con la base de datos generarán un error si hubiera iniciado una transacción en la conexión de base de datos subyacente por su cuenta.  

> [!NOTE]
> La limitación de aceptar solo conexiones cerradas se quitó en Entity Framework 6. Para obtener más información, consulte [Administración de conexiones](xref:ef6/fundamentals/connection-management).  

A partir de EF6, el marco de trabajo ahora proporciona:  

1. **Database. BeginTransaction ()** : método más sencillo para que un usuario inicie y complete transacciones en un DbContext existente, lo que permite combinar varias operaciones dentro de la misma transacción y, por lo tanto, todas confirmadas o revertidas como una. También permite al usuario especificar más fácilmente el nivel de aislamiento para la transacción.  
2. **Database. UseTransaction ()** : permite que DbContext use una transacción que se inició fuera del Entity Framework.  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>Combinar varias operaciones en una transacción dentro del mismo contexto  

**Database. BeginTransaction ()** tiene dos invalidaciones: una que toma un [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) explícito y otra que no toma ningún argumento y usa el valor de IsolationLevel predeterminado del proveedor de base de datos subyacente. Ambas invalidaciones devuelven un objeto **DbContextTransaction** que proporciona los métodos **Commit ()** y **Rollback ()** que realizan la confirmación y reversión en la transacción del almacén subyacente.  

La **DbContextTransaction** está pensada para ser eliminada una vez que se ha confirmado o revertido. Una manera fácil de lograrlo es **usar (...) {.** ..} sintaxis que llamará automáticamente a **Dispose ()** cuando se complete el bloque Using:  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void StartOwnTransactionWithinContext()
        {
            using (var context = new BloggingContext())
            {
                using (var dbContextTransaction = context.Database.BeginTransaction())
                {
                    context.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'"
                        );

                    var query = context.Posts.Where(p => p.Blog.Rating >= 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }

                    context.SaveChanges();

                    dbContextTransaction.Commit();
                }
            }
        }
    }
}
```  

> [!NOTE]
> Iniciar una transacción requiere que la conexión del almacén subyacente esté abierta. Por tanto, si se llama a Database. BeginTransaction (), se abrirá la conexión si aún no está abierta. Si DbContextTransaction abrió la conexión, se cerrará cuando se llame a Dispose ().  

### <a name="passing-an-existing-transaction-to-the-context"></a>Pasar una transacción existente al contexto  

A veces, desea una transacción que es incluso más amplia en el ámbito y que incluye operaciones en la misma base de datos pero fuera de EF completamente. Para ello, debe abrir la conexión e iniciar la transacción usted mismo y, a continuación, indicar a EF a) que use la conexión de base de datos ya abierta y b) para usar la transacción existente en esa conexión.  

Para ello, debe definir y usar un constructor en la clase de contexto que herede de uno de los constructores DbContext que llevan i) un parámetro de conexión existente y II) el valor booleano contextOwnsConnection.  

> [!NOTE]
> La marca contextOwnsConnection debe establecerse en false cuando se llama en este escenario. Esto es importante a medida que informa Entity Framework que no debería cerrar la conexión cuando se realiza con ella (por ejemplo, vea la línea 4 a continuación):  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

Además, debe iniciar la transacción usted mismo (incluido el valor de IsolationLevel si desea evitar la configuración predeterminada) y dejar que Entity Framework sepa que hay una transacción existente ya iniciada en la conexión (consulte la línea 33 a continuación).  

Después, puede ejecutar operaciones de base de datos directamente en el SqlConnection o en DbContext. Todas estas operaciones se ejecutan dentro de una transacción. Usted asume la responsabilidad de confirmar o revertir la transacción y de llamar a Dispose () en ella, así como para cerrar y eliminar la conexión a la base de datos. Por ejemplo:  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
     class TransactionsExample
     {
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
               {
                   var sqlCommand = new SqlCommand();
                   sqlCommand.Connection = conn;
                   sqlCommand.Transaction = sqlTxn;
                   sqlCommand.CommandText =
                       @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'";
                   sqlCommand.ExecuteNonQuery();

                   using (var context =  
                     new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        context.Database.UseTransaction(sqlTxn);

                        var query =  context.Posts.Where(p => p.Blog.Rating >= 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                       context.SaveChanges();
                    }

                    sqlTxn.Commit();
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a>Borrar la transacción

Puede pasar null a Database. UseTransaction () para borrar el conocimiento de Entity Framework de la transacción actual. Entity Framework no confirmará ni revertirá la transacción existente al hacerlo, por lo que debe usar con cuidado y solo si está seguro de que es lo que desea hacer.  

### <a name="errors-in-usetransaction"></a>Errores en UseTransaction

Verá una excepción de Database. UseTransaction () si pasa una transacción cuando:  
- Entity Framework ya tiene una transacción existente  
- Entity Framework ya está funcionando en TransactionScope  
- El objeto de conexión en la transacción pasada es NULL. Es decir, la transacción no está asociada a una conexión; normalmente es un signo de que ya se ha completado la transacción.  
- El objeto de conexión de la transacción pasada no coincide con la conexión del Entity Framework.  

## <a name="using-transactions-with-other-features"></a>Usar transacciones con otras características  

En esta sección se describe cómo interactúan las transacciones anteriores con:  

- Resistencia de la conexión  
- Métodos asincrónicos  
- Transacciones TransactionScope  

### <a name="connection-resiliency"></a>Resistencia de conexión  

La nueva característica de resistencia de conexión no funciona con las transacciones iniciadas por el usuario. Para obtener más información, consulte [reintento de estrategias de ejecución](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).  

### <a name="asynchronous-programming"></a>Programación asincrónica  

El enfoque descrito en las secciones anteriores no necesita más opciones ni valores de configuración para trabajar con los [métodos de consulta y guardado asíncronos](xref:ef6/fundamentals/async
). Pero tenga en cuenta que, en función de lo que haga dentro de los métodos asincrónicos, esto puede dar lugar a transacciones de ejecución prolongada, lo que a su vez puede provocar interbloqueos o bloqueos que son incorrectos para el rendimiento de la aplicación global.  

### <a name="transactionscope-transactions"></a>Transacciones TransactionScope  

Antes de EF6, la manera recomendada de proporcionar transacciones de ámbito mayor era usar un objeto TransactionScope:  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void UsingTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeOption.Required))
            {
                using (var conn = new SqlConnection("..."))
                {
                    conn.Open();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    sqlCommand.ExecuteNonQuery();

                    using (var context =
                        new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                        context.SaveChanges();
                    }
                }

                scope.Complete();
            }
        }
    }
}
```  

SqlConnection y Entity Framework usarían ambas transacciones ambiente TransactionScope y, por tanto, se confirmarán juntas.  

A partir de .NET 4.5.1 TransactionScope, se ha actualizado para que también funcione con métodos asincrónicos mediante el uso de la enumeración [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) :  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        public static void AsyncTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                using (var conn = new SqlConnection("..."))
                {
                    await conn.OpenAsync();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    await sqlCommand.ExecuteNonQueryAsync();

                    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        await context.SaveChangesAsync();
                    }
                }
            }
        }
    }
}
```  

Todavía existen algunas limitaciones en el enfoque de TransactionScope:  

- Requiere .NET 4.5.1 o superior para trabajar con métodos asincrónicos.  
- No se puede usar en escenarios en la nube a menos que esté seguro de que tiene una sola conexión (los escenarios en la nube no admiten transacciones distribuidas).  
- No se puede combinar con el enfoque Database. UseTransaction () de las secciones anteriores.  
- Se producirán excepciones si se emite cualquier DDL y no se han habilitado las transacciones distribuidas a través del servicio MSDTC.  

Ventajas del enfoque de TransactionScope:  

- Actualizará automáticamente una transacción local a una transacción distribuida si realiza más de una conexión a una base de datos determinada o combina una conexión con una base de datos con una conexión a una base de datos diferente en la misma transacción (Nota: debe tener el servicio MSDTC configurado para permitir que las transacciones distribuidas funcionen).  
- Facilidad de codificación. Si prefiere que la transacción sea ambiente y se trate implícitamente en segundo plano en lugar de hacerlo explícitamente bajo el control, el enfoque de TransactionScope puede adaptarse mejor.  

En Resumen, con las nuevas API Database. BeginTransaction () y Database. UseTransaction () anteriores, el enfoque de TransactionScope ya no es necesario para la mayoría de los usuarios. Si sigue usando TransactionScope, tenga en cuenta las limitaciones anteriores. Se recomienda usar el enfoque descrito en las secciones anteriores, siempre que sea posible.  
