---
title: 'Transacciones: EF Core'
description: Administración de transacciones para obtener atomicidad al guardar datos con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
uid: core/saving/transactions
ms.openlocfilehash: 2674fdc59cb08e5faf2302270629c446abc2670c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618921"
---
# <a name="using-transactions"></a>Usar transacciones

Las transacciones permiten procesar varias operaciones de base de datos de manera atómica. Si se confirma la transacción, todas las operaciones se aplicaron correctamente a la base de datos. Si se revierte la transacción, ninguna de las operaciones se aplicó a la base de datos.

> [!TIP]  
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) de este artículo en GitHub.

## <a name="default-transaction-behavior"></a>Comportamiento predeterminado de las transacciones

De manera predeterminada, si el proveedor de base de datos admite las transacciones, todos los cambios de una llamada sencilla a `SaveChanges()` se aplican a una transacción. Si cualquiera de los cambios presenta un error, la transacción se revertirá y no se aplicará ninguno de los cambios a la base de datos. Esto significa que se garantiza que `SaveChanges()` se complete correctamente o deje sin modificaciones la base de datos si se produce un error.

Este comportamiento predeterminado es suficiente para la mayoría de las aplicaciones. Solo debe controlar manualmente las transacciones si los requisitos de la aplicación lo consideran necesario.

## <a name="controlling-transactions"></a>Control de las transacciones

Puede usar la API `DbContext.Database` para iniciar, confirmar y revertir las transacciones. En el ejemplo siguiente se muestran dos operaciones `SaveChanges()` y una consulta LINQ que se ejecuta en una sola transacción.

No todos los proveedores de base de datos admiten transacciones. Algunos proveedores pueden generar una excepción o una operación no efectiva cuando se llaman a las API de transacción.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a>Transacción de contexto cruzado (solo bases de datos relacionales)

También puede compartir una transacción en varias instancias de contexto. Esta funcionalidad solo está disponible cuando se usa un proveedor de base de datos relacionales porque requiere el uso de `DbTransaction` y `DbConnection`, específicos para las bases de datos relacionales.

Para compartir una transacción, los contextos deben compartir tanto `DbConnection` como `DbTransaction`.

### <a name="allow-connection-to-be-externally-provided"></a>Permitir conexiones proporcionadas externamente

Compartir una `DbConnection` requiere la capacidad de pasar una conexión a un contexto cuando se construya.

La manera más sencilla de permitir que `DbConnection` se proporcione de manera externa es dejar de usar el método `DbContext.OnConfiguring` para configurar el contexto y crear externamente `DbContextOptions` y pasarlas al constructor del contexto.

> [!TIP]  
> `DbContextOptionsBuilder` es la API que usó en `DbContext.OnConfiguring` para configurar el contexto y ahora va a usarla para crear externamente `DbContextOptions`.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

Una alternativa es seguir usando `DbContext.OnConfiguring`, pero aceptar una `DbConnection` que se guarda y luego se usa en `DbContext.OnConfiguring`.

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

### <a name="share-connection-and-transaction"></a>Compartir conexión y transacción

Ahora puede crear varias instancias de contexto que comparten la misma conexión. Luego use la API `DbContext.Database.UseTransaction(DbTransaction)` para inscribir ambos contextos en la misma transacción.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Uso de DbTransactions externas (solo bases de datos relacionales)

Si usa varias tecnologías de acceso a datos para acceder a una base de datos relacional, es posible que quiera compartir una transacción entre las operaciones que estas distintas tecnologías realizan.

En el ejemplo siguiente se muestra cómo realizar una operación SqlClient de ADO.NET y una operación de Entity Framework Core en la misma transacción.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a>Utilizar System.Transactions

> [!NOTE]  
> Esta característica es nueva en EF Core 2.1.

Es posible usar transacciones de ambiente si necesita coordinar en un ámbito mayor.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

También es posible inscribir en una transacción explícita.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a>Limitaciones de System.Transactions  

1. EF Core se basa en los proveedores de base de datos para implementar la compatibilidad con System.Transactions. Si bien la compatibilidad es bastante común entre los proveedores de ADO.NET para .NET Framework, la API solo se agregó recientemente a .NET Core y, por tanto, la compatibilidad no siempre está tan extendida. Si un proveedor no implementa la compatibilidad con System.Transactions, es posible que las llamadas a estas API se omitan completamente. SqlClient para .NET no lo admite desde la versión 2.1 en adelante. SqlClient para .NET Core 2.0 generará una excepción si intent usar la característica.

   > [!IMPORTANT]  
   > Se recomienda probar que la API se comporte correctamente con el proveedor antes de usarla para administrar las transacciones. Si no es así, recomendamos que se ponga en contacto con el mantenedor del proveedor de base de datos.

2. A partir de la versión 2.1, la implementación de System.Transactions en .NET Core no incluye compatibilidad con transacciones distribuidas, por lo que no puede usar`TransactionScope` ni `CommittableTransaction` para coordinar las transacciones entre varios administradores de recursos.
