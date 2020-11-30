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
# <a name="using-transactions"></a>Usar transacciones

Las transacciones permiten procesar varias operaciones de base de datos de manera atómica. Si se confirma la transacción, todas las operaciones se aplicaron correctamente a la base de datos. Si se revierte la transacción, ninguna de las operaciones se aplicó a la base de datos.

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) de este artículo en GitHub.

## <a name="default-transaction-behavior"></a>Comportamiento predeterminado de las transacciones

De manera predeterminada, si el proveedor de base de datos admite las transacciones, todos los cambios de una llamada sencilla a `SaveChanges` se aplican a una transacción. Si cualquiera de los cambios presenta un error, la transacción se revertirá y no se aplicará ninguno de los cambios a la base de datos. Esto significa que se garantiza que `SaveChanges` se complete correctamente o deje sin modificaciones la base de datos si se produce un error.

Este comportamiento predeterminado es suficiente para la mayoría de las aplicaciones. Solo debe controlar manualmente las transacciones si los requisitos de la aplicación lo consideran necesario.

## <a name="controlling-transactions"></a>Control de las transacciones

Puede usar la API `DbContext.Database` para iniciar, confirmar y revertir las transacciones. En el ejemplo siguiente se muestran dos operaciones `SaveChanges` y una consulta LINQ que se ejecuta en una sola transacción:

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

Si bien todos los proveedores de bases de datos relacionales admiten transacciones, otros tipos de proveedores pueden generar errores o no operar cuando se llama a las API de transacciones.

## <a name="savepoints"></a>Puntos de retorno

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 5.0.

Cuando se invoca a `SaveChanges` y ya hay una transacción en curso en el contexto, EF crea automáticamente un *punto de retorno* antes de guardar los datos. Los puntos de retorno son puntos dentro de una transacción de base de datos a los que se puede revertir más tarde en caso de que ocurra un error o por cualquier otro motivo. Si `SaveChanges` encuentra algún error, revierte automáticamente la transacción al punto de retorno, y la transacción se mantiene en el mismo estado que si nunca se hubiera iniciado. Esto le permite posiblemente corregir problemas y volver a intentar guardar, en particular cuando ocurren problemas de [simultaneidad optimista](xref:core/saving/concurrency).

> [!WARNING]
> Los puntos de retorno son incompatibles con los conjuntos de resultados activos múltiples de SQL Server y no se usan. Si se produce un error durante `SaveChanges`, es posible que la transacción se quede en un estado desconocido.

También es posible administrar los puntos de retorno de forma manual, del mismo modo que con las transacciones. En el ejemplo siguiente se crea un punto de retorno dentro de una transacción y se revierte cuando se produce un error:

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a>Transacción entre contextos

También puede compartir una transacción en varias instancias de contexto. Esta funcionalidad solo está disponible cuando se usa un proveedor de base de datos relacionales porque requiere el uso de `DbTransaction` y `DbConnection`, específicos para las bases de datos relacionales.

Para compartir una transacción, los contextos deben compartir tanto `DbConnection` como `DbTransaction`.

### <a name="allow-connection-to-be-externally-provided"></a>Permitir conexiones proporcionadas externamente

Compartir una `DbConnection` requiere la capacidad de pasar una conexión a un contexto cuando se construya.

La manera más sencilla de permitir que `DbConnection` se proporcione de manera externa es dejar de usar el método `DbContext.OnConfiguring` para configurar el contexto y crear externamente `DbContextOptions` y pasarlas al constructor del contexto.

> [!TIP]
> `DbContextOptionsBuilder` es la API que usó en `DbContext.OnConfiguring` para configurar el contexto y ahora va a usarla para crear externamente `DbContextOptions`.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

Una alternativa es seguir usando `DbContext.OnConfiguring`, pero aceptar una `DbConnection` que se guarda y luego se usa en `DbContext.OnConfiguring`.

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

### <a name="share-connection-and-transaction"></a>Compartir conexión y transacción

Ahora puede crear varias instancias de contexto que comparten la misma conexión. Luego use la API `DbContext.Database.UseTransaction(DbTransaction)` para inscribir ambos contextos en la misma transacción.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Uso de DbTransactions externas (solo bases de datos relacionales)

Si usa varias tecnologías de acceso a datos para acceder a una base de datos relacional, es posible que quiera compartir una transacción entre las operaciones que estas distintas tecnologías realizan.

En el ejemplo siguiente se muestra cómo realizar una operación SqlClient de ADO.NET y una operación de Entity Framework Core en la misma transacción.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a>Utilizar System.Transactions

Es posible usar transacciones de ambiente si necesita coordinar en un ámbito mayor.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

También es posible inscribir en una transacción explícita.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a>Limitaciones de System.Transactions

1. EF Core se basa en los proveedores de base de datos para implementar la compatibilidad con System.Transactions. Si un proveedor no implementa la compatibilidad con System.Transactions, es posible que las llamadas a estas API se omitan completamente. SqlClient lo admite.

   > [!IMPORTANT]
   > Se recomienda probar que la API se comporte correctamente con el proveedor antes de usarla para administrar las transacciones. Si no es así, recomendamos que se ponga en contacto con el mantenedor del proveedor de base de datos.

2. A partir de .NET Core 2.1, la implementación de System.Transactions en no incluye compatibilidad con transacciones distribuidas, por lo que no puede usar`TransactionScope` ni `CommittableTransaction` para coordinar las transacciones entre varios administradores de recursos.
