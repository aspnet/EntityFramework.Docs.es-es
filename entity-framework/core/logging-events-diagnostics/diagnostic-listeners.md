---
title: 'Uso de agentes de escucha de diagnóstico: EF Core'
description: Uso de DiagnosticListener para el consumo global de diagnósticos de EF Core
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: 7035acaa6d306e73a0a2c071532ece0d8e9a0a1d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024256"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a>Uso de agentes de escucha de diagnóstico en EF Core

> [!TIP]
> Puede [descargar el ejemplo de este artículo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) en github.

Las escuchas de diagnóstico permiten escuchar cualquier evento de EF Core que se produzca en el proceso de .NET actual. La <xref:System.Diagnostics.DiagnosticListener> clase forma parte de un [mecanismo común en .net](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para obtener información de diagnóstico de aplicaciones en ejecución.

Las escuchas de diagnóstico no son adecuadas para obtener eventos de una sola instancia de DbContext. Los [interceptores](xref:core/logging-events-diagnostics/interceptors) de EF Core proporcionan acceso a los mismos eventos con registro por contexto.

Las escuchas de diagnóstico no están diseñadas para el registro. Considere la posibilidad de usar un [registro simple](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) para el registro.

## <a name="example-observing-diagnostic-events"></a>Ejemplo: observación de eventos de diagnóstico

La resolución de eventos EF Core es un proceso de dos pasos. En primer lugar [](/dotnet/standard/events/observer-design-pattern) , `DiagnosticListener` se debe crear un observador para sí mismo:

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

El `OnNext` método busca el DiagnosticListener que procede de EF Core. Este agente de escucha tiene el nombre "Microsoft. EntityFrameworkCore", que se puede obtener de la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> clase tal y como se muestra.

Este observador debe registrarse en todo el mundo, por ejemplo, en el método de la aplicación `Main` :

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

En segundo lugar, una vez que se encuentra el EF Core DiagnosticListener, se crea un nuevo observador de clave-valor para suscribirse a los eventos de EF Core reales. Por ejemplo:

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

El `OnNext` método es esta hora llamada con un par clave-valor para cada evento de EF Core. La clave es el nombre del evento, que se puede obtener de uno de los siguientes:

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> para eventos comunes a todos los proveedores de bases de datos de EF Core
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> para eventos comunes a todos los proveedores de bases de datos relacionales
* Clase similar para los eventos específicos del proveedor de base de datos actual. Por ejemplo, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> para el proveedor de SQL Server.

El valor del par clave-valor es un tipo de carga específico para el evento. El tipo de carga que se espera se documenta en cada evento definido en estas clases de eventos.

Por ejemplo, el código anterior controla los <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> eventos y <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> . Para el primero de ellos, la carga es <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> . En el segundo caso, es <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .

> [!TIP]
> ToString se invalida en cada EF Core clase de datos de evento para generar el mensaje de registro equivalente para el evento. Por ejemplo, al llamar a se `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0 inicializado ' BlogsContext ' con el proveedor ' Microsoft. EntityFrameworkCore. SQLite ' con Options: None".

El [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) contiene una aplicación de consola simple que realiza cambios en la base de datos de blogs e imprime los eventos de diagnóstico encontrados.

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion

        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

La salida de este código muestra los eventos detectados:

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
