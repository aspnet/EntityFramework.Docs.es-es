---
title: 'Resistencia de conexión: EF Core'
description: Uso de la resistencia de conexión para reintentar automáticamente los comandos con Entity Framework Core
author: AndriySvyryd
ms.date: 11/15/2016
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: db0666a49cbd41ef3eacf447eaeed1fb54ffcbf4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061923"
---
# <a name="connection-resiliency"></a>Resistencia de conexión

La resistencia de conexión reintenta automáticamente los comandos de base de datos con errores. La característica se puede usar con cualquier base de datos proporcionando una "estrategia de ejecución", que encapsula la lógica necesaria para detectar errores y volver a ejecutar los comandos. EF Core proveedores pueden proporcionar estrategias de ejecución adaptadas a condiciones de error de base de datos específicas y directivas de reintentos óptimas.

Como ejemplo, el proveedor de SQL Server incluye una estrategia de ejecución que se adapta específicamente a SQL Server (incluido SQL Azure). Es consciente de los tipos de excepción que se pueden reintentar y tienen valores predeterminados razonables para el número máximo de reintentos, el retraso entre reintentos, etc.

Cuando se configuran las opciones para el contexto, se especifica una estrategia de ejecución. Normalmente, se encuentra en el `OnConfiguring` método del contexto derivado:

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

o en `Startup.cs` para una aplicación ASP.net Core:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a>Estrategia de ejecución personalizada

Existe un mecanismo para registrar una estrategia de ejecución personalizada propia si desea cambiar cualquiera de los valores predeterminados.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>Estrategias de ejecución y transacciones

Una estrategia de ejecución que reintenta automáticamente los errores debe ser capaz de reproducir cada operación en un bloque de reintentos en el que se produce un error. Cuando se habilitan los reintentos, cada operación que se realiza a través de EF Core se convierte en su propia operación admite reintentos. Es decir, cada consulta y cada llamada a `SaveChanges()` se reintentará como una unidad si se produce un error transitorio.

Sin embargo, si el código inicia una transacción mediante `BeginTransaction()` la definición de su propio grupo de operaciones que deben tratarse como una unidad, y es necesario reproducir todo el contenido dentro de la transacción, se producirá un error. Recibirá una excepción similar a la siguiente si intenta hacerlo al usar una estrategia de ejecución:

> InvalidOperationException: la estrategia de ejecución configurada ' SqlServerRetryingExecutionStrategy ' no admite transacciones iniciadas por el usuario. Use la estrategia de ejecución que devuelve "DbContext.Database.CreateExecutionStrategy()" para ejecutar todas las operaciones en la transacción como una unidad que se puede reintentar.

La solución consiste en invocar manualmente la estrategia de ejecución con un delegado que representa todo lo que se debe ejecutar. Si se produce un error transitorio, la estrategia de ejecución vuelve a invocar al delegado.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

Este enfoque también se puede usar con transacciones de ambiente.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>Error de confirmación de la transacción y el problema Idempotencia

En general, cuando se produce un error de conexión, se revierte la transacción actual. Sin embargo, si se quita la conexión mientras se confirma la transacción, se desconoce el estado resultante de la transacción.

De forma predeterminada, la estrategia de ejecución reintentará la operación como si la transacción se revirtió, pero si no es así, se producirá una excepción si el nuevo estado de la base de datos es incompatible o podría provocar daños en los **datos** si la operación no se basa en un estado determinado, por ejemplo, al insertar una nueva fila con valores de clave generados automáticamente.

Hay varias maneras de solucionar este error.

### <a name="option-1---do-almost-nothing"></a>Opción 1: hacer (casi) nada

La probabilidad de que se produzca un error de conexión durante la confirmación de la transacción es baja, por lo que puede ser aceptable que la aplicación no funcione correctamente si esta condición se produce realmente.

Sin embargo, debe evitar el uso de claves generadas por el almacén para asegurarse de que se produce una excepción en lugar de agregar una fila duplicada. Considere la posibilidad de usar un valor GUID generado por el cliente o un generador de valores del lado cliente.

### <a name="option-2---rebuild-application-state"></a>Opción 2: recompilar el estado de la aplicación

1. Descartar el actual `DbContext` .
2. Cree un nuevo `DbContext` y restaure el estado de la aplicación desde la base de datos.
3. Informe al usuario de que es posible que la última operación no se haya completado correctamente.

### <a name="option-3---add-state-verification"></a>Opción 3: agregar comprobación de estado

Para la mayoría de las operaciones que cambian el estado de la base de datos es posible agregar código que comprueba si se ha realizado correctamente. EF proporciona un método de extensión para facilitar esta tarea `IExecutionStrategy.ExecuteInTransaction` .

Este método inicia y confirma una transacción y también acepta una función en el `verifySucceeded` parámetro que se invoca cuando se produce un error transitorio durante la confirmación de la transacción.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> Aquí `SaveChanges` se invoca con `acceptAllChangesOnSuccess` establecido en `false` para evitar cambiar el estado de la `Blog` entidad a si se `Unchanged` `SaveChanges` realiza correctamente. Esto permite volver a intentar la misma operación si se produce un error en la confirmación y se revierte la transacción.

### <a name="option-4---manually-track-the-transaction"></a>Opción 4: realizar un seguimiento manual de la transacción

Si necesita usar claves generadas por el almacén o necesita una manera genérica de controlar los errores de confirmación que no dependen de la operación realizada, se podría asignar a cada transacción un identificador que se comprueba cuando se produce un error en la confirmación.

1. Agregue una tabla a la base de datos utilizada para realizar el seguimiento del estado de las transacciones.
2. Inserte una fila en la tabla al principio de cada transacción.
3. Si se produce un error en la conexión durante la confirmación, Compruebe la presencia de la fila correspondiente en la base de datos.
4. Si la confirmación se realiza correctamente, elimine la fila correspondiente para evitar el crecimiento de la tabla.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> Asegúrese de que el contexto utilizado para la comprobación tenga definida una estrategia de ejecución, ya que es probable que se produzca un error en la conexión durante la comprobación si se produjo un error durante la confirmación de la transacción.

## <a name="additional-resources"></a>Recursos adicionales

* [Solución de problemas de errores de conexión transitorios en Azure SQL Database y SQL Instancia administrada](/azure/azure-sql/database/troubleshoot-common-connectivity-issues)
