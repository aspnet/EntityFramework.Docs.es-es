---
title: Detección y notificaciones de cambios-EF Core
description: Detección de cambios de propiedad y relación mediante DetectChanges o notificaciones
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129640"
---
# <a name="change-detection-and-notifications"></a><span data-ttu-id="33c33-103">Detección y notificaciones de cambios</span><span class="sxs-lookup"><span data-stu-id="33c33-103">Change Detection and Notifications</span></span>

<span data-ttu-id="33c33-104">Cada <xref:Microsoft.EntityFrameworkCore.DbContext> instancia realiza un seguimiento de los cambios realizados en las entidades.</span><span class="sxs-lookup"><span data-stu-id="33c33-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="33c33-105">Estas entidades de las que se realiza un seguimiento convierten los cambios en la base de datos cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a.</span><span class="sxs-lookup"><span data-stu-id="33c33-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="33c33-106">Esto se trata en [Change Tracking en EF Core](xref:core/change-tracking/index), y en este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de Entity Framework Core (EF Core).</span><span class="sxs-lookup"><span data-stu-id="33c33-106">This is covered in [Change Tracking in EF Core](xref:core/change-tracking/index), and this document assumes that entity states and the basics of Entity Framework Core (EF Core) change tracking are understood.</span></span>

<span data-ttu-id="33c33-107">El seguimiento de los cambios de propiedades y relaciones requiere que DbContext pueda detectar estos cambios.</span><span class="sxs-lookup"><span data-stu-id="33c33-107">Tracking property and relationship changes requires that the DbContext is able to detect these changes.</span></span> <span data-ttu-id="33c33-108">En este documento se explica cómo se produce esta detección y cómo usar las notificaciones de propiedad o los proxies de seguimiento de cambios para forzar la detección inmediata de los cambios.</span><span class="sxs-lookup"><span data-stu-id="33c33-108">This document covers how this detection happens, as well as how to use property notifications or change-tracking proxies to force immediate detection of changes.</span></span>

> [!TIP]
> <span data-ttu-id="33c33-109">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo desde GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span><span class="sxs-lookup"><span data-stu-id="33c33-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span></span>

## <a name="snapshot-change-tracking"></a><span data-ttu-id="33c33-110">Seguimiento de cambios de instantánea</span><span class="sxs-lookup"><span data-stu-id="33c33-110">Snapshot change tracking</span></span>

<span data-ttu-id="33c33-111">De forma predeterminada, EF Core crea una instantánea de los valores de propiedad de cada entidad cuando se realiza un seguimiento por primera instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="33c33-111">By default, EF Core creates a snapshot of every entity's property values when it is first tracked by a DbContext instance.</span></span> <span data-ttu-id="33c33-112">Los valores almacenados en esta instantánea se comparan con los valores actuales de la entidad para determinar qué valores de propiedad han cambiado.</span><span class="sxs-lookup"><span data-stu-id="33c33-112">The values stored in this snapshot are then compared against the current values of the entity in order to determine which property values have changed.</span></span>

<span data-ttu-id="33c33-113">Esta detección de cambios se produce cuando se llama a SaveChanges para asegurarse de que se detectan todos los valores cambiados antes de enviar actualizaciones a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="33c33-113">This detection of changes happens when SaveChanges is called to ensure all changed values are detected before sending updates to the database.</span></span> <span data-ttu-id="33c33-114">Sin embargo, la detección de cambios también se produce en otros momentos para asegurarse de que la aplicación funciona con información de seguimiento actualizada.</span><span class="sxs-lookup"><span data-stu-id="33c33-114">However, the detection of changes also happens at other times to ensure the application is working with up-to-date tracking information.</span></span> <span data-ttu-id="33c33-115">La detección de cambios se puede forzar en cualquier momento mediante una llamada a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="33c33-115">Detection of changes can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="when-change-detection-is-needed"></a><span data-ttu-id="33c33-116">Cuando se necesita la detección de cambios</span><span class="sxs-lookup"><span data-stu-id="33c33-116">When change detection is needed</span></span>

<span data-ttu-id="33c33-117">La detección de cambios es necesaria cuando se ha cambiado una propiedad o navegación _sin utilizar EF Core para realizar este cambio_.</span><span class="sxs-lookup"><span data-stu-id="33c33-117">Detection of changes is needed when a property or navigation has been changed _without using EF Core to make this change_.</span></span> <span data-ttu-id="33c33-118">Por ejemplo, considere la posibilidad de cargar blogs y entradas y, a continuación, realizar cambios en estas entidades:</span><span class="sxs-lookup"><span data-stu-id="33c33-118">For example, consider loading blogs and posts and then making changes to these entities:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

<span data-ttu-id="33c33-119">Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) antes de llamar a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> , se muestra que los cambios realizados no se han detectado y, por lo tanto, no se reflejan en los Estados de la entidad y en los datos de propiedad modificados</span><span class="sxs-lookup"><span data-stu-id="33c33-119">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> shows that the changes made have not been detected and hence are not reflected in the entity states and modified property data:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="33c33-120">En concreto, el estado de la entrada de blog sigue siendo `Unchanged` y la nueva publicación no aparece como entidad sometida a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="33c33-120">Specifically, the state of the blog entry is still `Unchanged`, and the new post does not appear as a tracked entity.</span></span> <span data-ttu-id="33c33-121">(El astutos observará que las propiedades notifican sus nuevos valores, aunque EF Core no hayan detectado todavía estos cambios.</span><span class="sxs-lookup"><span data-stu-id="33c33-121">(The astute will notice properties report their new values, even though these changes have not yet been detected by EF Core.</span></span> <span data-ttu-id="33c33-122">Esto se debe a que la vista de depuración Lee los valores actuales directamente de la instancia de la entidad).</span><span class="sxs-lookup"><span data-stu-id="33c33-122">This is because the debug view is reading current values directly from the entity instance.)</span></span>

<span data-ttu-id="33c33-123">Compare esto con la vista de depuración después de llamar a DetectChanges:</span><span class="sxs-lookup"><span data-stu-id="33c33-123">Contrast this with the debug view after calling DetectChanges:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="33c33-124">Ahora el blog está marcado correctamente como `Modified` y se ha detectado la nueva publicación y se realiza su seguimiento como `Added` .</span><span class="sxs-lookup"><span data-stu-id="33c33-124">Now the blog is correctly marked as `Modified` and the new post has been detected and is tracked as `Added`.</span></span>

<span data-ttu-id="33c33-125">Al principio de esta sección, se indica que se necesita la detección de cambios cuando no se usa _EF Core para realizar el cambio_.</span><span class="sxs-lookup"><span data-stu-id="33c33-125">At the start of this section we stated that detecting changes is needed when not using _using EF Core to make the change_.</span></span> <span data-ttu-id="33c33-126">Esto es lo que sucede en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="33c33-126">This is what is happening in the code above.</span></span> <span data-ttu-id="33c33-127">Es decir, los cambios en la propiedad y la navegación se realizan _directamente en las instancias de la entidad_ y no mediante métodos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="33c33-127">That is, the changes to the property and navigation are made _directly on the entity instances_, and not by using any EF Core methods.</span></span>

<span data-ttu-id="33c33-128">Compare esto con el siguiente código que modifica las entidades de la misma manera, pero esta vez mediante métodos EF Core:</span><span class="sxs-lookup"><span data-stu-id="33c33-128">Contrast this to the following code which modifies the entities in the same way, but this time using EF Core methods:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

<span data-ttu-id="33c33-129">En este caso, la vista de depuración del seguimiento de cambios muestra que se conocen todos los Estados de las entidades y las modificaciones de propiedades, aunque no se ha producido la detección de cambios.</span><span class="sxs-lookup"><span data-stu-id="33c33-129">In this case the change tracker debug view shows that all entity states and property modifications are known, even though detection of changes has not happened.</span></span> <span data-ttu-id="33c33-130">Esto se debe a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> que es un método EF Core, lo que significa que EF Core conoce inmediatamente el cambio realizado por este método.</span><span class="sxs-lookup"><span data-stu-id="33c33-130">This is because <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> is an EF Core method, which means that EF Core immediately knows about the change made by this method.</span></span> <span data-ttu-id="33c33-131">Del mismo modo, la llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> permite a EF Core saber de inmediato la nueva entidad y realizar su seguimiento de forma adecuada.</span><span class="sxs-lookup"><span data-stu-id="33c33-131">Likewise, calling <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> allows EF Core to immediately know about the new entity and track it appropriately.</span></span>

> [!TIP]
> <span data-ttu-id="33c33-132">No intente evitar la detección de cambios mediante el uso de métodos EF Core para realizar cambios en la entidad.</span><span class="sxs-lookup"><span data-stu-id="33c33-132">Don't attempt to avoid detecting changes by always using EF Core methods to make entity changes.</span></span> <span data-ttu-id="33c33-133">Esto suele ser más complicado y se comporta menos bien que realizar cambios en las entidades de la manera normal.</span><span class="sxs-lookup"><span data-stu-id="33c33-133">Doing so is often more cumbersome and performs less well than making changes to entities in the normal way.</span></span> <span data-ttu-id="33c33-134">La intención de este documento es informar de Cuándo se necesita detectar cambios y cuándo no lo está.</span><span class="sxs-lookup"><span data-stu-id="33c33-134">The intention of this document is to inform as to when detecting changes is needed and when it is not.</span></span> <span data-ttu-id="33c33-135">La intención es no fomentar la prevención de la detección de cambios.</span><span class="sxs-lookup"><span data-stu-id="33c33-135">The intention is not to encourage avoidance of change detection.</span></span>

### <a name="methods-that-automatically-detect-changes"></a><span data-ttu-id="33c33-136">Métodos que detectan automáticamente los cambios</span><span class="sxs-lookup"><span data-stu-id="33c33-136">Methods that automatically detect changes</span></span>

<span data-ttu-id="33c33-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> se llama automáticamente a los métodos en los que es probable que esto afecte a los resultados.</span><span class="sxs-lookup"><span data-stu-id="33c33-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is called automatically by methods where doing so is likely to impact the results.</span></span> <span data-ttu-id="33c33-138">Estos métodos son:</span><span class="sxs-lookup"><span data-stu-id="33c33-138">These methods are:</span></span>

- <span data-ttu-id="33c33-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> , para asegurarse de que se detectan todos los cambios antes de actualizar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="33c33-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>, to ensure that all changes are detected before updating the database.</span></span>
- <span data-ttu-id="33c33-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> , para asegurarse de que los Estados de entidad y las propiedades modificadas están actualizados.</span><span class="sxs-lookup"><span data-stu-id="33c33-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>, to ensure entity states and modified properties are up-to-date.</span></span>
- <span data-ttu-id="33c33-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, para asegurarse de que el resultado es preciso.</span><span class="sxs-lookup"><span data-stu-id="33c33-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, to ensure that the result is accurate.</span></span>
- <span data-ttu-id="33c33-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, para garantizar los Estados de entidad correctos para las entidades principal y primaria antes de la cascada.</span><span class="sxs-lookup"><span data-stu-id="33c33-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, to ensure correct entity states for principal/parent entities before cascading.</span></span>
- <span data-ttu-id="33c33-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, para asegurarse de que el gráfico sometido a seguimiento esté actualizado.</span><span class="sxs-lookup"><span data-stu-id="33c33-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, to ensure that the tracked graph is up-to-date.</span></span>

<span data-ttu-id="33c33-144">También hay algunos lugares donde se produce la detección de cambios solo en una única instancia de entidad, en lugar de en el gráfico completo de entidades de las que se ha realizado un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="33c33-144">There are also some places where detection of changes happens on only a single entity instance, rather than on the entire graph of tracked entities.</span></span> <span data-ttu-id="33c33-145">Estos lugares son:</span><span class="sxs-lookup"><span data-stu-id="33c33-145">These places are:</span></span>

- <span data-ttu-id="33c33-146">Al utilizar <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> , para asegurarse de que el estado y las propiedades modificadas de la entidad están actualizados.</span><span class="sxs-lookup"><span data-stu-id="33c33-146">When using <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>, to ensure that the entity's state and modified properties are up-to-date.</span></span>
- <span data-ttu-id="33c33-147">Cuando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> se usan métodos como `Property` , `Collection` `Reference` o `Member` para garantizar que las modificaciones de propiedades, los valores actuales, etc. están actualizados.</span><span class="sxs-lookup"><span data-stu-id="33c33-147">When using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> methods such as `Property`, `Collection`, `Reference` or `Member` to ensure property modifications, current values, etc. are up-to-date.</span></span>
- <span data-ttu-id="33c33-148">Cuando una entidad dependiente/secundaria se va a eliminar porque se ha roto una relación requerida.</span><span class="sxs-lookup"><span data-stu-id="33c33-148">When a dependent/child entity is going to be deleted because a required relationship has been severed.</span></span> <span data-ttu-id="33c33-149">Esto detecta cuándo no se debe eliminar una entidad porque se ha vuelto a crear un elemento primario.</span><span class="sxs-lookup"><span data-stu-id="33c33-149">This detects when an entity should not be deleted because it has been re-parented.</span></span>

<span data-ttu-id="33c33-150">La detección local de los cambios de una sola entidad se puede desencadenar explícitamente mediante una llamada a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="33c33-150">Local detection of changes for a single entity can be triggered explicitly by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType>.</span></span>

> [!NOTE]
> <span data-ttu-id="33c33-151">Los cambios en la detección local pueden pasar por alto algunos cambios que encontraría una detección completa.</span><span class="sxs-lookup"><span data-stu-id="33c33-151">Local detect changes can miss some changes that a full detection would find.</span></span> <span data-ttu-id="33c33-152">Esto sucede cuando las acciones en cascada resultantes de cambios no detectados en otras entidades afectan a la entidad en cuestión.</span><span class="sxs-lookup"><span data-stu-id="33c33-152">This happens when cascading actions resulting from undetected changes to other entities have an impact on the entity in question.</span></span> <span data-ttu-id="33c33-153">En tales casos, la aplicación puede necesitar forzar un examen completo de todas las entidades mediante una llamada explícita a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="33c33-153">In such situations the application may need to force a full scan of all entities by explicitly calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="disabling-automatic-change-detection"></a><span data-ttu-id="33c33-154">Deshabilitar la detección automática de cambios</span><span class="sxs-lookup"><span data-stu-id="33c33-154">Disabling automatic change detection</span></span>

<span data-ttu-id="33c33-155">El rendimiento de la detección de cambios no es un cuello de botella para la mayoría de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="33c33-155">The performance of detecting changes is not a bottleneck for most applications.</span></span> <span data-ttu-id="33c33-156">Sin embargo, la detección de cambios puede suponer un problema de rendimiento para algunas aplicaciones que realizan el seguimiento de miles de entidades.</span><span class="sxs-lookup"><span data-stu-id="33c33-156">However, detecting changes can become a performance problem for some applications that track thousands of entities.</span></span> <span data-ttu-id="33c33-157">(El número exacto dependerá de muchas cosas, como el número de propiedades de la entidad). Por este motivo, se puede deshabilitar la detección automática de cambios mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="33c33-157">(The exact number will dependent on many things, such as the number of properties in the entity.) For this reason the automatic detection of changes can be disabled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType>.</span></span> <span data-ttu-id="33c33-158">Por ejemplo, considere el procesamiento de entidades de combinación en una relación de varios a varios con cargas:</span><span class="sxs-lookup"><span data-stu-id="33c33-158">For example, consider processing join entities in a many-to-many relationship with payloads:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

<span data-ttu-id="33c33-159">Como sabemos de la sección anterior, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> detectan automáticamente los cambios.</span><span class="sxs-lookup"><span data-stu-id="33c33-159">As we know from the previous section, both <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatically detect changes.</span></span> <span data-ttu-id="33c33-160">Sin embargo, después de llamar a las entradas, el código no realiza ningún cambio en el estado de la entidad o propiedad.</span><span class="sxs-lookup"><span data-stu-id="33c33-160">However, after calling Entries, the code does not then make any entity or property state changes.</span></span> <span data-ttu-id="33c33-161">(Establecer valores de propiedades normales en entidades agregadas no produce cambios de estado). Por lo tanto, el código deshabilita la detección de cambios automática innecesaria al llamar al método SaveChanges de base.</span><span class="sxs-lookup"><span data-stu-id="33c33-161">(Setting normal property values on Added entities does not cause any state changes.) The code therefore disables unnecessary automatic change detection when calling down into the base SaveChanges method.</span></span> <span data-ttu-id="33c33-162">El código también usa un bloque try/finally para asegurarse de que se restaure la configuración predeterminada incluso si se produce un error en SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="33c33-162">The code also makes use of a try/finally block to ensure that the default setting is restored even if SaveChanges fails.</span></span>

> [!TIP]
> <span data-ttu-id="33c33-163">No asuma que el código debe deshabilitar la detección automática de cambios para que funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="33c33-163">Do not assume that your code must disable automatic change detection to to perform well.</span></span> <span data-ttu-id="33c33-164">Esto solo es necesario cuando la generación de perfiles de una aplicación que realiza un seguimiento de muchas entidades indica que el rendimiento de la detección de cambios es un problema.</span><span class="sxs-lookup"><span data-stu-id="33c33-164">This is only needed when profiling an application tracking many entities indicates that performance of change detection is an issue.</span></span>

### <a name="detecting-changes-and-value-conversions"></a><span data-ttu-id="33c33-165">Detección de cambios y conversiones de valores</span><span class="sxs-lookup"><span data-stu-id="33c33-165">Detecting changes and value conversions</span></span>

<span data-ttu-id="33c33-166">Para usar el seguimiento de cambios de instantánea con un tipo de entidad, EF Core debe ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="33c33-166">To use snapshot change tracking with an entity type, EF Core must be able to:</span></span>

- <span data-ttu-id="33c33-167">Crear una instantánea de cada valor de propiedad cuando se realiza el seguimiento de la entidad</span><span class="sxs-lookup"><span data-stu-id="33c33-167">Make a snapshot of each property value when the entity is tracked</span></span>
- <span data-ttu-id="33c33-168">Compara este valor con el valor actual de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="33c33-168">Compare this value to the current value of the property</span></span>
- <span data-ttu-id="33c33-169">Generar un código hash para el valor</span><span class="sxs-lookup"><span data-stu-id="33c33-169">Generate a hash code for the value</span></span>

<span data-ttu-id="33c33-170">Esto lo controla automáticamente EF Core para los tipos que se pueden asignar directamente a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="33c33-170">This is handled automatically by EF Core for types that can be directly mapped to the database.</span></span> <span data-ttu-id="33c33-171">Sin embargo, cuando [se utiliza un convertidor de valores para asignar una propiedad](xref:core/modeling/value-conversions), dicho convertidor debe especificar cómo realizar estas acciones.</span><span class="sxs-lookup"><span data-stu-id="33c33-171">However, when a [value converter is used to map a property](xref:core/modeling/value-conversions), then that converter must specify how to perform these actions.</span></span> <span data-ttu-id="33c33-172">Esto se logra con un comparador de valores y se describe en detalle en la documentación de los [comparadores de valores](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="33c33-172">This is achieved with a value comparer, and is described in detail in the [Value Comparers](xref:core/modeling/value-comparers) documentation.</span></span>

## <a name="notification-entities"></a><span data-ttu-id="33c33-173">Entidades de notificación</span><span class="sxs-lookup"><span data-stu-id="33c33-173">Notification entities</span></span>

<span data-ttu-id="33c33-174">El seguimiento de cambios de instantánea se recomienda para la mayoría de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="33c33-174">Snapshot change tracking is recommended for most applications.</span></span> <span data-ttu-id="33c33-175">Sin embargo, las aplicaciones que realizan el seguimiento de muchas entidades o realizan muchos cambios en esas entidades pueden beneficiarse de la implementación de entidades que notifican automáticamente EF Core cuando cambian sus valores de propiedad y navegación.</span><span class="sxs-lookup"><span data-stu-id="33c33-175">However, applications that track many entities and/or make many changes to those entities may benefit from implementing entities that automatically notify EF Core when their property and navigation values change.</span></span> <span data-ttu-id="33c33-176">Estos se conocen como "entidades de notificación".</span><span class="sxs-lookup"><span data-stu-id="33c33-176">These are known as "notification entities".</span></span>

### <a name="implementing-notification-entities"></a><span data-ttu-id="33c33-177">Implementación de entidades de notificación</span><span class="sxs-lookup"><span data-stu-id="33c33-177">Implementing notification entities</span></span>

<span data-ttu-id="33c33-178">Las entidades de notificación hacen uso de las <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> interfaces e, que forman parte de la biblioteca de clases base (BCL) de .net.</span><span class="sxs-lookup"><span data-stu-id="33c33-178">Notification entities make use of the <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged> interfaces, which are part of the .NET base class library (BCL).</span></span> <span data-ttu-id="33c33-179">Estas interfaces definen eventos que se deben desencadenar antes y después de cambiar un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="33c33-179">These interfaces define events that must be fired before and after changing a property value.</span></span> <span data-ttu-id="33c33-180">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="33c33-180">For example:</span></span>

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

<span data-ttu-id="33c33-181">Además, las navegaciones de las colecciones deben implementar `INotifyCollectionChanged` ; en el ejemplo anterior, esto se cumple mediante el uso <xref:System.Collections.ObjectModel.ObservableCollection%601> de un de posts.</span><span class="sxs-lookup"><span data-stu-id="33c33-181">In addition, any collection navigations must implement `INotifyCollectionChanged`; in the example above this satisfied by using an <xref:System.Collections.ObjectModel.ObservableCollection%601> of posts.</span></span> <span data-ttu-id="33c33-182">EF Core también se suministra con una <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementación que tiene búsquedas más eficaces a costa de una ordenación estable.</span><span class="sxs-lookup"><span data-stu-id="33c33-182">EF Core also ships with an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementation that has more efficient lookups at the expense of stable ordering.</span></span>

<span data-ttu-id="33c33-183">Normalmente, la mayor parte de este código de notificación se mueve a una clase base no asignada.</span><span class="sxs-lookup"><span data-stu-id="33c33-183">Most of this notification code is typically moved into an unmapped base class.</span></span> <span data-ttu-id="33c33-184">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="33c33-184">For example:</span></span>

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a><span data-ttu-id="33c33-185">Configuración de entidades de notificación</span><span class="sxs-lookup"><span data-stu-id="33c33-185">Configuring notification entities</span></span>

<span data-ttu-id="33c33-186">No hay ninguna manera de que EF Core valide que `INotifyPropertyChanging` o `INotifyPropertyChanged` estén totalmente implementados para su uso con EF Core.</span><span class="sxs-lookup"><span data-stu-id="33c33-186">There is no way for EF Core to validate that `INotifyPropertyChanging` or `INotifyPropertyChanged` are fully implemented for use with EF Core.</span></span> <span data-ttu-id="33c33-187">En concreto, algunos usos de estas interfaces lo hacen con las notificaciones solo en determinadas propiedades, en lugar de en todas las propiedades (incluidas las navegaciones) según lo requiera EF Core.</span><span class="sxs-lookup"><span data-stu-id="33c33-187">In particular, some uses of these interfaces do so with notifications only on certain properties, rather than on all properties (including navigations) as required by EF Core.</span></span> <span data-ttu-id="33c33-188">Por esta razón, EF Core no se enlaza automáticamente a estos eventos.</span><span class="sxs-lookup"><span data-stu-id="33c33-188">For this reason, EF Core does not automatically hook into these events.</span></span>

<span data-ttu-id="33c33-189">En su lugar, EF Core debe estar configurado para usar estas entidades de notificación.</span><span class="sxs-lookup"><span data-stu-id="33c33-189">Instead, EF Core must be configured to use these notification entities.</span></span> <span data-ttu-id="33c33-190">Normalmente, esto se hace para todos los tipos de entidad mediante una llamada a <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="33c33-190">This is usually done for all entity types by calling <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="33c33-191">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="33c33-191">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

<span data-ttu-id="33c33-192">(La estrategia también se puede establecer de manera diferente para los distintos tipos de entidad mediante, pero suele ser un valor de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> contraproducción, ya que DetectChanges sigue siendo necesario para los tipos que no son entidades de notificación).</span><span class="sxs-lookup"><span data-stu-id="33c33-192">(The strategy can also be set differently for different entity types using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>, but this is usually counterproductive since DetectChanges is still required for those types that are not notification entities.)</span></span>

<span data-ttu-id="33c33-193">El seguimiento de cambios de notificación completo requiere que tanto `INotifyPropertyChanging` como `INotifyPropertyChanged` estén implementados.</span><span class="sxs-lookup"><span data-stu-id="33c33-193">Full notification change tracking requires that both `INotifyPropertyChanging` and `INotifyPropertyChanged` are implemented.</span></span> <span data-ttu-id="33c33-194">Esto permite guardar los valores originales justo antes de cambiar el valor de la propiedad, evitando la necesidad de EF Core para crear una instantánea al realizar el seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="33c33-194">This allows original values to be saved just before the property value is changed, avoiding the need for EF Core to create a snapshot when tracking the entity.</span></span> <span data-ttu-id="33c33-195">Los tipos de entidad que implementan solo `INotifyPropertyChanged` se pueden usar con EF Core.</span><span class="sxs-lookup"><span data-stu-id="33c33-195">Entity types that implement only `INotifyPropertyChanged` can also be used with EF Core.</span></span> <span data-ttu-id="33c33-196">En este caso, EF todavía crea una instantánea al realizar el seguimiento de una entidad para realizar un seguimiento de los valores originales, pero usa las notificaciones para detectar los cambios inmediatamente, en lugar de tener que llamar a DetectChanges.</span><span class="sxs-lookup"><span data-stu-id="33c33-196">In this case, EF still creates a snapshot when tracking an entity to keep track of original values, but then uses the notifications to detect changes immediately, rather than needing DetectChanges to be called.</span></span>

<span data-ttu-id="33c33-197">Los diferentes <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> valores se resumen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="33c33-197">The different <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> values are summarized in the the following table.</span></span>

| <span data-ttu-id="33c33-198">ChangeTrackingStrategy</span><span class="sxs-lookup"><span data-stu-id="33c33-198">ChangeTrackingStrategy</span></span>                              | <span data-ttu-id="33c33-199">Interfaces necesarias</span><span class="sxs-lookup"><span data-stu-id="33c33-199">Interfaces needed</span></span>                                      | <span data-ttu-id="33c33-200">Necesita DetectChanges</span><span class="sxs-lookup"><span data-stu-id="33c33-200">Needs DetectChanges</span></span> | <span data-ttu-id="33c33-201">Valores originales de instantáneas</span><span class="sxs-lookup"><span data-stu-id="33c33-201">Snapshots original values</span></span>
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| <span data-ttu-id="33c33-202">Instantánea</span><span class="sxs-lookup"><span data-stu-id="33c33-202">Snapshot</span></span>                                            | <span data-ttu-id="33c33-203">None</span><span class="sxs-lookup"><span data-stu-id="33c33-203">None</span></span>                                                   | <span data-ttu-id="33c33-204">Sí</span><span class="sxs-lookup"><span data-stu-id="33c33-204">Yes</span></span>                 | <span data-ttu-id="33c33-205">Sí</span><span class="sxs-lookup"><span data-stu-id="33c33-205">Yes</span></span>
| <span data-ttu-id="33c33-206">ChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="33c33-206">ChangedNotifications</span></span>                                | <span data-ttu-id="33c33-207">INotifyPropertyChanged</span><span class="sxs-lookup"><span data-stu-id="33c33-207">INotifyPropertyChanged</span></span>                                 | <span data-ttu-id="33c33-208">No</span><span class="sxs-lookup"><span data-stu-id="33c33-208">No</span></span>                  | <span data-ttu-id="33c33-209">Sí</span><span class="sxs-lookup"><span data-stu-id="33c33-209">Yes</span></span>
| <span data-ttu-id="33c33-210">ChangingAndChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="33c33-210">ChangingAndChangedNotifications</span></span>                     | <span data-ttu-id="33c33-211">INotifyPropertyChanged y INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="33c33-211">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="33c33-212">No</span><span class="sxs-lookup"><span data-stu-id="33c33-212">No</span></span>                  | <span data-ttu-id="33c33-213">No</span><span class="sxs-lookup"><span data-stu-id="33c33-213">No</span></span>
| <span data-ttu-id="33c33-214">ChangingAndChangedNotificationsWithOriginalValues</span><span class="sxs-lookup"><span data-stu-id="33c33-214">ChangingAndChangedNotificationsWithOriginalValues</span></span>   | <span data-ttu-id="33c33-215">INotifyPropertyChanged y INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="33c33-215">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="33c33-216">No</span><span class="sxs-lookup"><span data-stu-id="33c33-216">No</span></span>                  | <span data-ttu-id="33c33-217">Sí</span><span class="sxs-lookup"><span data-stu-id="33c33-217">Yes</span></span>

### <a name="using-notification-entities"></a><span data-ttu-id="33c33-218">Usar entidades de notificación</span><span class="sxs-lookup"><span data-stu-id="33c33-218">Using notification entities</span></span>

<span data-ttu-id="33c33-219">Las entidades de notificación se comportan como cualquier otra entidad, salvo que realizar cambios en las instancias de la entidad no requiere una llamada a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> para detectar estos cambios.</span><span class="sxs-lookup"><span data-stu-id="33c33-219">Notification entities behave like any other entities, except that making changes to the entity instances do not require a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> to detect these changes.</span></span> <span data-ttu-id="33c33-220">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="33c33-220">For example:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

<span data-ttu-id="33c33-221">Con las entidades normales, la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) mostró que estos cambios no se detectaron hasta que se llamó a DetectChanges.</span><span class="sxs-lookup"><span data-stu-id="33c33-221">With normal entities, the [change tracker debug view](xref:core/change-tracking/debug-views) showed that these changes were not detected until DetectChanges was called.</span></span> <span data-ttu-id="33c33-222">Al examinar la vista de depuración cuando se usan las entidades de notificación, se muestra que estos cambios se han detectado inmediatamente:</span><span class="sxs-lookup"><span data-stu-id="33c33-222">Looking at the debug view when notification entities are used shows that these changes have been detected immediately:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a><span data-ttu-id="33c33-223">Proxies de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="33c33-223">Change-tracking proxies</span></span>

> [!NOTE]
> <span data-ttu-id="33c33-224">Los proxies de seguimiento de cambios se introdujeron en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="33c33-224">Change-tracking proxies were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="33c33-225">EF Core pueden generar dinámicamente tipos de proxy que implementan <xref:System.ComponentModel.INotifyPropertyChanging> y <xref:System.ComponentModel.INotifyPropertyChanged> .</span><span class="sxs-lookup"><span data-stu-id="33c33-225">EF Core can dynamically generate proxy types that implement <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged>.</span></span> <span data-ttu-id="33c33-226">Esto requiere la instalación del paquete NuGet [Microsoft. EntityFrameworkCore. Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) y la habilitación de los proxies de seguimiento de cambios con, <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="33c33-226">This requires installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package, and enabling change-tracking proxies with <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

<span data-ttu-id="33c33-227">La creación de un proxy dinámico implica la creación de un nuevo tipo .net dinámico (mediante la implementación de los proxies de [. Core](https://www.nuget.org/packages/Castle.Core/) ), que hereda del tipo de entidad y, a continuación, invalida todos los establecedores de propiedad.</span><span class="sxs-lookup"><span data-stu-id="33c33-227">Creating a dynamic proxy involves creating a new, dynamic .NET type (using the [Castle.Core](https://www.nuget.org/packages/Castle.Core/) proxies implementation), which inherits from the entity type and then overrides all property setters.</span></span> <span data-ttu-id="33c33-228">Por tanto, los tipos de entidad de los servidores proxy deben ser tipos que se pueden heredar de y deben tener propiedades que se puedan invalidar.</span><span class="sxs-lookup"><span data-stu-id="33c33-228">Entity types for proxies must therefore be types that can be inherited from and must have properties that can be overridden.</span></span> <span data-ttu-id="33c33-229">Además, las navegaciones de la colección creadas explícitamente deben implementar, <xref:System.Collections.Specialized.INotifyCollectionChanged> por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="33c33-229">Also, collection navigations created explicitly must implement <xref:System.Collections.Specialized.INotifyCollectionChanged> For example:</span></span>

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

<span data-ttu-id="33c33-230">Una desventaja importante de los proxies de seguimiento de cambios es que EF Core siempre debe realizar un seguimiento de las instancias del proxy, nunca instancias del tipo de entidad subyacente.</span><span class="sxs-lookup"><span data-stu-id="33c33-230">One significant downside to change-tracking proxies is that EF Core must always track instances of the proxy, never instances of the underlying entity type.</span></span> <span data-ttu-id="33c33-231">Esto se debe a que las instancias del tipo de entidad subyacente no generarán notificaciones, lo que significa que se omitirán los cambios realizados en estas entidades.</span><span class="sxs-lookup"><span data-stu-id="33c33-231">This is because instances of the underlying entity type will not generate notifications, which means changes made to these entities will be missed.</span></span>

<span data-ttu-id="33c33-232">EF Core crea instancias de proxy automáticamente al consultar la base de datos, por lo que generalmente se limita a realizar el seguimiento de nuevas instancias de entidad.</span><span class="sxs-lookup"><span data-stu-id="33c33-232">EF Core creates proxy instances automatically when querying the database, so this downside is generally limited to tracking new entity instances.</span></span> <span data-ttu-id="33c33-233">Estas instancias deben crearse mediante los <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> métodos de extensión y **no** de la manera normal mediante `new` .</span><span class="sxs-lookup"><span data-stu-id="33c33-233">These instances must be created using the <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> extension methods, and **not** in the normal way using `new`.</span></span> <span data-ttu-id="33c33-234">Esto significa que el código de los ejemplos anteriores ahora debe hacer uso de `CreateProxy` :</span><span class="sxs-lookup"><span data-stu-id="33c33-234">This means the code from the previous examples must now make use of `CreateProxy`:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a><span data-ttu-id="33c33-235">Eventos de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="33c33-235">Change tracking events</span></span>

<span data-ttu-id="33c33-236">EF Core activa el <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> evento cuando se realiza un seguimiento de una entidad por primera vez.</span><span class="sxs-lookup"><span data-stu-id="33c33-236">EF Core fires the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> event when an entity is tracked for the first time.</span></span> <span data-ttu-id="33c33-237">Los cambios futuros de estado de la entidad producen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> eventos.</span><span class="sxs-lookup"><span data-stu-id="33c33-237">Future entity state changes result in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> events.</span></span> <span data-ttu-id="33c33-238">Consulte [Eventos de .NET en EF Core](xref:core/logging-events-diagnostics/events) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="33c33-238">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="33c33-239">El `StateChanged` evento no se desencadena cuando se realiza el seguimiento de una entidad por primera vez, aunque el Estado haya cambiado de `Detached` a uno de los otros Estados.</span><span class="sxs-lookup"><span data-stu-id="33c33-239">The `StateChanged` event is not fired when an entity is first tracked, even though the state has changed from `Detached` to one of the other states.</span></span> <span data-ttu-id="33c33-240">Asegúrese de escuchar los `StateChanged` `Tracked` eventos y para obtener todas las notificaciones pertinentes.</span><span class="sxs-lookup"><span data-stu-id="33c33-240">Make sure to listen for both `StateChanged` and `Tracked` events to get all relevant notifications.</span></span>
