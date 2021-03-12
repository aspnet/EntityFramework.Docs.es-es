---
title: 'Eventos de .NET: EF Core'
description: Eventos .NET definidos por EF Core
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: c26032d19d7bc05d30d4576534c7425da4472072
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024243"
---
# <a name="net-events-in-ef-core"></a><span data-ttu-id="67843-103">Eventos .NET en EF Core</span><span class="sxs-lookup"><span data-stu-id="67843-103">.NET Events in EF Core</span></span>

> [!TIP]
> <span data-ttu-id="67843-104">Puede [descargar el ejemplo de eventos](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) de github.</span><span class="sxs-lookup"><span data-stu-id="67843-104">You can [download the events sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) from GitHub.</span></span>

<span data-ttu-id="67843-105">Entity Framework Core (EF Core) expone [eventos .net](/dotnet/standard/events/) para que actúen como devoluciones de llamada cuando se producen ciertas cosas en el código EF Core.</span><span class="sxs-lookup"><span data-stu-id="67843-105">Entity Framework Core (EF Core) exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="67843-106">Los eventos son más sencillos que los [interceptores](xref:core/logging-events-diagnostics/interceptors) y permiten un registro más flexible.</span><span class="sxs-lookup"><span data-stu-id="67843-106">Events are simpler than [interceptors](xref:core/logging-events-diagnostics/interceptors) and allow more flexible registration.</span></span> <span data-ttu-id="67843-107">Sin embargo, solo son sincrónicos y, por tanto, no pueden realizar operaciones de E/S asincrónicas sin bloqueo.</span><span class="sxs-lookup"><span data-stu-id="67843-107">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="67843-108">Los eventos se registran por `DbContext` instancia.</span><span class="sxs-lookup"><span data-stu-id="67843-108">Events are registered per `DbContext` instance.</span></span> <span data-ttu-id="67843-109">Use una [escucha de diagnóstico](xref:core/logging-events-diagnostics/diagnostic-listeners) para obtener la misma información, pero para todas las instancias de DbContext del proceso.</span><span class="sxs-lookup"><span data-stu-id="67843-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="events-raised-by-ef-core"></a><span data-ttu-id="67843-110">Eventos generados por EF Core</span><span class="sxs-lookup"><span data-stu-id="67843-110">Events raised by EF Core</span></span>

<span data-ttu-id="67843-111">Los eventos siguientes los genera EF Core:</span><span class="sxs-lookup"><span data-stu-id="67843-111">The following events are raised by EF Core:</span></span>

| <span data-ttu-id="67843-112">Evento</span><span class="sxs-lookup"><span data-stu-id="67843-112">Event</span></span> | <span data-ttu-id="67843-113">Versión introducida</span><span class="sxs-lookup"><span data-stu-id="67843-113">Version introduced</span></span> | <span data-ttu-id="67843-114">Cuando se produce</span><span class="sxs-lookup"><span data-stu-id="67843-114">When raised</span></span>
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | <span data-ttu-id="67843-115">5.0</span><span class="sxs-lookup"><span data-stu-id="67843-115">5.0</span></span> | <span data-ttu-id="67843-116">Al principio de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="67843-116">At the start of <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | <span data-ttu-id="67843-117">5.0</span><span class="sxs-lookup"><span data-stu-id="67843-117">5.0</span></span> | <span data-ttu-id="67843-118">Al final de una operación correcta <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="67843-118">At the end of a successful <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | <span data-ttu-id="67843-119">5.0</span><span class="sxs-lookup"><span data-stu-id="67843-119">5.0</span></span> | <span data-ttu-id="67843-120">Al final de un error <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="67843-120">At the end of a failed <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | <span data-ttu-id="67843-121">2.1</span><span class="sxs-lookup"><span data-stu-id="67843-121">2.1</span></span> | <span data-ttu-id="67843-122">Cuando el contexto realiza un seguimiento de una entidad</span><span class="sxs-lookup"><span data-stu-id="67843-122">When an entity is tracked by the context</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | <span data-ttu-id="67843-123">2.1</span><span class="sxs-lookup"><span data-stu-id="67843-123">2.1</span></span> | <span data-ttu-id="67843-124">Cuando una entidad de la que se realiza un seguimiento cambia su estado</span><span class="sxs-lookup"><span data-stu-id="67843-124">When a tracked entity changes its state</span></span>

### <a name="example-timestamp-state-changes"></a><span data-ttu-id="67843-125">Ejemplo: cambios de estado de marca de tiempo</span><span class="sxs-lookup"><span data-stu-id="67843-125">Example: Timestamp state changes</span></span>

<span data-ttu-id="67843-126">Cada entidad de la que un DbContext realiza un seguimiento tiene un <xref:Microsoft.EntityFrameworkCore.EntityState> .</span><span class="sxs-lookup"><span data-stu-id="67843-126">Each entity tracked by a DbContext has an <xref:Microsoft.EntityFrameworkCore.EntityState>.</span></span> <span data-ttu-id="67843-127">Por ejemplo, el `Added` Estado indica que la entidad se insertará en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="67843-127">For example, the `Added` state indicates that the entity will be inserted into the database.</span></span>

<span data-ttu-id="67843-128">En este ejemplo se usan los <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> eventos y para detectar cuándo cambia el estado de una entidad.</span><span class="sxs-lookup"><span data-stu-id="67843-128">This example uses the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> events to detect when an entity changes state.</span></span> <span data-ttu-id="67843-129">A continuación, marca la entidad con la hora actual que indica el momento en que se produjo este cambio.</span><span class="sxs-lookup"><span data-stu-id="67843-129">It then stamps the entity with the current time indicating when this change happened.</span></span> <span data-ttu-id="67843-130">Esto da como resultado marcas de tiempo que indican cuándo se insertó, eliminó o actualizó por última vez la entidad.</span><span class="sxs-lookup"><span data-stu-id="67843-130">This results in timestamps indicating when the entity was inserted, deleted, and/or last updated.</span></span>

<span data-ttu-id="67843-131">Los tipos de entidad de este ejemplo implementan una interfaz que define las propiedades de marca de tiempo:</span><span class="sxs-lookup"><span data-stu-id="67843-131">The entity types in this example implement an interface that defines the timestamp properties:</span></span>

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

<span data-ttu-id="67843-132">Un método en DbContext de la aplicación puede establecer marcas de tiempo para cualquier entidad que implemente esta interfaz:</span><span class="sxs-lookup"><span data-stu-id="67843-132">A method on the application's DbContext can then set timestamps for any entity that implements this interface:</span></span>

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

<span data-ttu-id="67843-133">Este método tiene la firma adecuada que se va a usar como controlador de eventos para los `Tracked` `StateChanged` eventos y.</span><span class="sxs-lookup"><span data-stu-id="67843-133">This method has the appropriate signature to use as an event handler for both the `Tracked` and `StateChanged` events.</span></span> <span data-ttu-id="67843-134">El controlador se registra para ambos eventos en el constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="67843-134">The handler is registered for both events in the DbContext constructor.</span></span> <span data-ttu-id="67843-135">Tenga en cuenta que los eventos se pueden adjuntar a DbContext en cualquier momento; no es necesario que esto suceda en el constructor de contexto.</span><span class="sxs-lookup"><span data-stu-id="67843-135">Note that events can be attached to a DbContext at any time; it is not required that this happen in the context constructor.</span></span>

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

<span data-ttu-id="67843-136">Ambos eventos son necesarios porque las nuevas entidades desencadenan `Tracked` eventos cuando se realiza el seguimiento por primera vez.</span><span class="sxs-lookup"><span data-stu-id="67843-136">Both events are needed because new entities fire `Tracked` events when they are first tracked.</span></span> <span data-ttu-id="67843-137">`StateChanged` los eventos solo se activan para las entidades que cambian de estado mientras _ya_ se están realizando el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="67843-137">`StateChanged` events are only fired for entities that change state while they are _already_ being tracked.</span></span>

<span data-ttu-id="67843-138">El [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) de este ejemplo contiene una aplicación de consola simple que realiza cambios en la base de datos de blogs:</span><span class="sxs-lookup"><span data-stu-id="67843-138">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) for this example contains a simple console application that makes changes to the blogging database:</span></span>

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

<span data-ttu-id="67843-139">La salida de este código muestra los cambios de estado que se producen y las marcas de tiempo que se aplican:</span><span class="sxs-lookup"><span data-stu-id="67843-139">The output from this code shows the state changes happening and the timestamps being applied:</span></span>

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
