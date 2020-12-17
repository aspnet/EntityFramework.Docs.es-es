---
title: 'Eventos de .NET: EF Core'
description: Eventos .NET definidos por EF Core
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 51c0bba5cf25e1d9ddd1fd9aebea50b9a03481a3
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635697"
---
# <a name="net-events-in-ef-core"></a>Eventos .NET en EF Core

> [!TIP]  
> Puede [descargar el ejemplo de eventos](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) de github.

Entity Framework Core (EF Core) expone [eventos .net](/dotnet/standard/events/) para que actúen como devoluciones de llamada cuando se producen ciertas cosas en el código EF Core. Los eventos son más sencillos que los [interceptores](xref:core/logging-events-diagnostics/interceptors) y permiten un registro más flexible. Sin embargo, solo son sincrónicos y, por tanto, no pueden realizar operaciones de E/S asincrónicas sin bloqueo.

Los eventos se registran por `DbContext` instancia. Use una [escucha de diagnóstico](xref:core/logging-events-diagnostics/diagnostic-listeners) para obtener la misma información, pero para todas las instancias de DbContext del proceso.

## <a name="events-raised-by-ef-core"></a>Eventos generados por EF Core

Los eventos siguientes los genera EF Core:

| Evento | Versión introducida | Cuando se produce
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | 5.0 | Al principio de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | 5.0 | Al final de una operación correcta <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | 5.0 | Al final de un error <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | 2.1 | Cuando el contexto realiza un seguimiento de una entidad
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | 2.1 | Cuando una entidad de la que se realiza un seguimiento cambia su estado

### <a name="example-timestamp-state-changes"></a>Ejemplo: cambios de estado de marca de tiempo

Cada entidad de la que un DbContext realiza un seguimiento tiene un <xref:Microsoft.EntityFrameworkCore.EntityState> . Por ejemplo, el `Added` Estado indica que la entidad se insertará en la base de datos.

En este ejemplo se usan los <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> eventos y para detectar cuándo cambia el estado de una entidad. A continuación, marca la entidad con la hora actual que indica el momento en que se produjo este cambio. Esto da como resultado marcas de tiempo que indican cuándo se insertó, eliminó o actualizó por última vez la entidad.

Los tipos de entidad de este ejemplo implementan una interfaz que define las propiedades de marca de tiempo:

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

Un método en DbContext de la aplicación puede establecer marcas de tiempo para cualquier entidad que implemente esta interfaz:

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

Este método tiene la firma adecuada que se va a usar como controlador de eventos para los `Tracked` `StateChanged` eventos y. El controlador se registra para ambos eventos en el constructor DbContext. Tenga en cuenta que los eventos se pueden adjuntar a DbContext en cualquier momento; no es necesario que esto suceda en el constructor de contexto.

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

Ambos eventos son necesarios porque las nuevas entidades desencadenan `Tracked` eventos cuando se realiza el seguimiento por primera vez. `StateChanged` los eventos solo se activan para las entidades que cambian de estado mientras _ya_ se están realizando el seguimiento.

El [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) de este ejemplo contiene una aplicación de consola simple que realiza cambios en la base de datos de blogs:

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

La salida de este código muestra los cambios de estado que se producen y las marcas de tiempo que se aplican:

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
