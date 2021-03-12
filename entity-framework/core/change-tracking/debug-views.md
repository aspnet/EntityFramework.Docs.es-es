---
title: 'Depuración de Change Tracker: EF Core'
description: Uso de ChangeTracker DebugView y mensajes de registro para depurar EF Core el seguimiento de cambios
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: cda81728beb5ffbc8604c191e84b3553a7343ec0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023177"
---
# <a name="change-tracker-debugging"></a><span data-ttu-id="34c9b-103">Depuración de Change Tracker</span><span class="sxs-lookup"><span data-stu-id="34c9b-103">Change Tracker Debugging</span></span>

<span data-ttu-id="34c9b-104">El seguimiento de cambios de Entity Framework Core (EF Core) genera dos tipos de resultados para ayudar con la depuración:</span><span class="sxs-lookup"><span data-stu-id="34c9b-104">The Entity Framework Core (EF Core) change tracker generates two kinds of output to help with debugging:</span></span>

- <span data-ttu-id="34c9b-105"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType>Proporciona una vista legible de todas las entidades de las que se realiza un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="34c9b-105">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> provides a human-readable view of all entities being tracked</span></span>
- <span data-ttu-id="34c9b-106">Los mensajes de registro de nivel de depuración se generan cuando el seguimiento de cambios detecta el estado y corrige las relaciones</span><span class="sxs-lookup"><span data-stu-id="34c9b-106">Debug-level log messages are generated when the change tracker detects state and fixes up relationships</span></span>

> [!TIP]
> <span data-ttu-id="34c9b-107">En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="34c9b-107">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="34c9b-108">Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.</span><span class="sxs-lookup"><span data-stu-id="34c9b-108">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="34c9b-109">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackerDebugging).</span><span class="sxs-lookup"><span data-stu-id="34c9b-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackerDebugging).</span></span>

## <a name="change-tracker-debug-view"></a><span data-ttu-id="34c9b-110">Vista de depuración de Change Tracker</span><span class="sxs-lookup"><span data-stu-id="34c9b-110">Change tracker debug view</span></span>

<span data-ttu-id="34c9b-111">Se puede tener acceso a la vista de depuración Change Tracker en el depurador del IDE.</span><span class="sxs-lookup"><span data-stu-id="34c9b-111">The change tracker debug view can be accessed in the debugger of your IDE.</span></span> <span data-ttu-id="34c9b-112">Por ejemplo, con Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="34c9b-112">For example, with Visual Studio:</span></span>

![Obtener acceso a la vista de depuración del seguimiento de cambios desde el depurador de Visual Studio](_static/debug-view.png)

<span data-ttu-id="34c9b-114">También se puede acceder a él directamente desde el código, por ejemplo, para enviar la vista de depuración a la consola:</span><span class="sxs-lookup"><span data-stu-id="34c9b-114">It can also be accessed directly from code, for example to send the debug view to the console:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

<span data-ttu-id="34c9b-115">La vista de depuración tiene una forma abreviada y un formato largo.</span><span class="sxs-lookup"><span data-stu-id="34c9b-115">The debug view has a short form and a long form.</span></span> <span data-ttu-id="34c9b-116">La forma abreviada muestra las entidades sometidas a seguimiento, su estado y sus valores de clave.</span><span class="sxs-lookup"><span data-stu-id="34c9b-116">The short form shows tracked entities, their state, and key values.</span></span> <span data-ttu-id="34c9b-117">El formato largo también incluye todos los valores de propiedad y navegación y el estado.</span><span class="sxs-lookup"><span data-stu-id="34c9b-117">The long form also includes all property and navigation values and state.</span></span>

### <a name="the-short-view"></a><span data-ttu-id="34c9b-118">Vista corta</span><span class="sxs-lookup"><span data-stu-id="34c9b-118">The short view</span></span>

<span data-ttu-id="34c9b-119">Echemos un vistazo a un ejemplo de vista de depuración con el modelo que se muestra al final de este documento.</span><span class="sxs-lookup"><span data-stu-id="34c9b-119">Let's look at a debug view example using the model shown at the end of this document.</span></span> <span data-ttu-id="34c9b-120">En primer lugar, realizaremos un seguimiento de algunas entidades y las colocaremos en algunos Estados diferentes, por lo que tenemos buenos datos de seguimiento de cambios para ver:</span><span class="sxs-lookup"><span data-stu-id="34c9b-120">First, we will track some entities and put them in some different states, just so we have good change tracking data to view:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

<span data-ttu-id="34c9b-121">La impresión de la vista corta en este punto, como se muestra arriba, da como resultado el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="34c9b-121">Printing the short view at this point, as shown above, results in the following output:</span></span>

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

<span data-ttu-id="34c9b-122">Aviso:</span><span class="sxs-lookup"><span data-stu-id="34c9b-122">Notice:</span></span>

- <span data-ttu-id="34c9b-123">Cada entidad de la que se realiza un seguimiento se muestra con su valor de clave principal (PK).</span><span class="sxs-lookup"><span data-stu-id="34c9b-123">Each tracked entity is listed with its primary key (PK) value.</span></span> <span data-ttu-id="34c9b-124">Por ejemplo, `Blog {Id: 1}`.</span><span class="sxs-lookup"><span data-stu-id="34c9b-124">For example, `Blog {Id: 1}`.</span></span>
- <span data-ttu-id="34c9b-125">Si la entidad es un tipo de entidad de tipo compartido, también se muestra el tipo CLR.</span><span class="sxs-lookup"><span data-stu-id="34c9b-125">If the entity is a shared-type entity type, then it's CLR type is also shown.</span></span> <span data-ttu-id="34c9b-126">Por ejemplo, `PostTag (Dictionary<string, object>)`.</span><span class="sxs-lookup"><span data-stu-id="34c9b-126">For example, `PostTag (Dictionary<string, object>)`.</span></span>
- <span data-ttu-id="34c9b-127"><xref:Microsoft.EntityFrameworkCore.EntityState>Se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="34c9b-127">The <xref:Microsoft.EntityFrameworkCore.EntityState> is shown next.</span></span> <span data-ttu-id="34c9b-128">Será uno de los de `Unchanged` , `Added` , `Modified` o `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="34c9b-128">This will be one of `Unchanged`, `Added`, `Modified`, or `Deleted`.</span></span>
- <span data-ttu-id="34c9b-129">A continuación se muestran los valores de cualquier clave alternativa (AKs).</span><span class="sxs-lookup"><span data-stu-id="34c9b-129">Values for any alternate keys (AKs) are shown next.</span></span> <span data-ttu-id="34c9b-130">Por ejemplo, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span><span class="sxs-lookup"><span data-stu-id="34c9b-130">For example, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span></span>
- <span data-ttu-id="34c9b-131">Por último, se muestran los valores de cualquier clave externa (claves externas).</span><span class="sxs-lookup"><span data-stu-id="34c9b-131">Finally, values for any foreign keys (FKs) are shown.</span></span> <span data-ttu-id="34c9b-132">Por ejemplo, `FK {PostsId: 4} FK {TagsId: 2}`.</span><span class="sxs-lookup"><span data-stu-id="34c9b-132">For example, `FK {PostsId: 4} FK {TagsId: 2}`.</span></span>

### <a name="the-long-view"></a><span data-ttu-id="34c9b-133">Vista larga</span><span class="sxs-lookup"><span data-stu-id="34c9b-133">The long view</span></span>

<span data-ttu-id="34c9b-134">La vista larga se puede enviar a la consola de la misma manera que la vista corta:</span><span class="sxs-lookup"><span data-stu-id="34c9b-134">The long view can be sent to the console in the same way as the short view:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

<span data-ttu-id="34c9b-135">La salida para el mismo estado que la vista abreviada anterior es:</span><span class="sxs-lookup"><span data-stu-id="34c9b-135">The output for the same state as the short view above is:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

<span data-ttu-id="34c9b-136">Cada entidad de la que se realiza el seguimiento y su estado se muestra como antes.</span><span class="sxs-lookup"><span data-stu-id="34c9b-136">Each tracked entity and its state is shown as before.</span></span> <span data-ttu-id="34c9b-137">Sin embargo, la vista larga también muestra los valores de propiedad y de navegación.</span><span class="sxs-lookup"><span data-stu-id="34c9b-137">However, the long view also shows property and navigation values.</span></span>

#### <a name="property-values"></a><span data-ttu-id="34c9b-138">Valores de propiedad</span><span class="sxs-lookup"><span data-stu-id="34c9b-138">Property values</span></span>

<span data-ttu-id="34c9b-139">Para cada propiedad, la vista larga muestra si la propiedad forma parte de una clave principal (PK), una clave alternativa (AK) o una clave externa (FK).</span><span class="sxs-lookup"><span data-stu-id="34c9b-139">For each property, the long view shows whether or not the property is part of a primary key (PK), alternate key (AK), or foreign key (FK).</span></span> <span data-ttu-id="34c9b-140">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="34c9b-140">For example:</span></span>

- <span data-ttu-id="34c9b-141">`Blog.Id` es una propiedad de clave principal: `Id: 1 PK`</span><span class="sxs-lookup"><span data-stu-id="34c9b-141">`Blog.Id` is a primary key property: `Id: 1 PK`</span></span>
- <span data-ttu-id="34c9b-142">`Blog.AssetsId` es una propiedad de clave alternativa: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span><span class="sxs-lookup"><span data-stu-id="34c9b-142">`Blog.AssetsId` is an alternate key property: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span></span>
- <span data-ttu-id="34c9b-143">`Post.BlogId` es una propiedad de clave externa: `BlogId: 2 FK`</span><span class="sxs-lookup"><span data-stu-id="34c9b-143">`Post.BlogId` is a foreign key property: `BlogId: 2 FK`</span></span>
- <span data-ttu-id="34c9b-144">`BlogAssets.Id` es tanto una clave principal como una propiedad de clave externa: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span><span class="sxs-lookup"><span data-stu-id="34c9b-144">`BlogAssets.Id` is both a primary key and a foreign key property: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span></span>

<span data-ttu-id="34c9b-145">Los valores de propiedad que se han modificado se marcan como tales y también se muestra el valor original de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="34c9b-145">Property values that have been modified are marked as such, and the original value of the property is also shown.</span></span> <span data-ttu-id="34c9b-146">Por ejemplo, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span><span class="sxs-lookup"><span data-stu-id="34c9b-146">For example, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span></span>

<span data-ttu-id="34c9b-147">Por último, `Added` las entidades con valores de clave temporales indican que el valor es temporal.</span><span class="sxs-lookup"><span data-stu-id="34c9b-147">Finally, `Added` entities with temporary key values indicate that the value is temporary.</span></span> <span data-ttu-id="34c9b-148">Por ejemplo, `Id: -2147482643 PK Temporary`.</span><span class="sxs-lookup"><span data-stu-id="34c9b-148">For example, `Id: -2147482643 PK Temporary`.</span></span>

#### <a name="navigation-values"></a><span data-ttu-id="34c9b-149">Valores de navegación</span><span class="sxs-lookup"><span data-stu-id="34c9b-149">Navigation values</span></span>

<span data-ttu-id="34c9b-150">Los valores de navegación se muestran mediante los valores de clave principal de las entidades a las que hace referencia la navegación.</span><span class="sxs-lookup"><span data-stu-id="34c9b-150">Navigation values are displayed using the primary key values of the entities that the navigations reference.</span></span> <span data-ttu-id="34c9b-151">Por ejemplo, en la salida anterior, post 3 está relacionado con el blog 2.</span><span class="sxs-lookup"><span data-stu-id="34c9b-151">For example, in the output above, post 3 is related to blog 2.</span></span> <span data-ttu-id="34c9b-152">Esto significa que la `Post.Blog` navegación señala a la `Blog` instancia de con el identificador 2.</span><span class="sxs-lookup"><span data-stu-id="34c9b-152">This means that the `Post.Blog` navigation points to the `Blog` instance with ID 2.</span></span> <span data-ttu-id="34c9b-153">Esto se muestra como `Blog: {Id: 2}` .</span><span class="sxs-lookup"><span data-stu-id="34c9b-153">This is shown as `Blog: {Id: 2}`.</span></span>

<span data-ttu-id="34c9b-154">Lo mismo sucede con las navegaciones de la colección, salvo que en este caso puede haber varias entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="34c9b-154">The same thing happens for collection navigations, except that in this case there can be multiple related entities.</span></span> <span data-ttu-id="34c9b-155">Por ejemplo, la navegación de colección `Blog.Posts` contiene tres entidades, con los valores de clave 1, 2 y-2147482643, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="34c9b-155">For example, the collection navigation `Blog.Posts` contains three entities, with key values 1, 2, and -2147482643 respectively.</span></span> <span data-ttu-id="34c9b-156">Esto se muestra como `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` .</span><span class="sxs-lookup"><span data-stu-id="34c9b-156">This is shown as `[{Id: 1}, {Id: 2}, {Id: -2147482643}]`.</span></span>

## <a name="change-tracker-logging"></a><span data-ttu-id="34c9b-157">Registro de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="34c9b-157">Change tracker logging</span></span>

<span data-ttu-id="34c9b-158">El seguimiento de cambios registra los mensajes en el `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> cuando [detecta cambios de propiedad o navegación](xref:core/change-tracking/debug-views).</span><span class="sxs-lookup"><span data-stu-id="34c9b-158">The change tracker logs messages at the `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> whenever it [detects property or navigation changes](xref:core/change-tracking/debug-views).</span></span> <span data-ttu-id="34c9b-159">Por ejemplo, cuando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> se llama a en el código en la parte superior de este documento y el [registro de depuración está habilitado](xref:core/logging-events-diagnostics/index), se generan los registros siguientes:</span><span class="sxs-lookup"><span data-stu-id="34c9b-159">For example, when <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is called in the code at the top of this document and [debug logging is enabled](xref:core/logging-events-diagnostics/index), then the following logs are generated:</span></span>

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

<span data-ttu-id="34c9b-160">En la tabla siguiente se resumen los mensajes de registro de seguimiento de cambios:</span><span class="sxs-lookup"><span data-stu-id="34c9b-160">The following table summaries the change tracker logging messages:</span></span>

| <span data-ttu-id="34c9b-161">Id. de evento</span><span class="sxs-lookup"><span data-stu-id="34c9b-161">Event ID</span></span>                                                                                                               | <span data-ttu-id="34c9b-162">Descripción</span><span class="sxs-lookup"><span data-stu-id="34c9b-162">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <span data-ttu-id="34c9b-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> se está iniciando</span><span class="sxs-lookup"><span data-stu-id="34c9b-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is starting</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <span data-ttu-id="34c9b-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> ha finalizado</span><span class="sxs-lookup"><span data-stu-id="34c9b-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> has completed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | <span data-ttu-id="34c9b-165">Ha cambiado un valor de propiedad normal</span><span class="sxs-lookup"><span data-stu-id="34c9b-165">A normal property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="34c9b-166">Ha cambiado un valor de propiedad de clave externa</span><span class="sxs-lookup"><span data-stu-id="34c9b-166">A foreign key property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="34c9b-167">Se ha agregado o quitado entidades relacionadas con una navegación de colección que no omite.</span><span class="sxs-lookup"><span data-stu-id="34c9b-167">A non-skip collection navigation has had related entities added or removed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | <span data-ttu-id="34c9b-168">Una navegación de referencia se ha cambiado para apuntar a otra entidad o debe establecerse en NULL.</span><span class="sxs-lookup"><span data-stu-id="34c9b-168">A reference navigation has been changed to point to another entity, or set to null</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | <span data-ttu-id="34c9b-169">EF Core inició el seguimiento de una entidad</span><span class="sxs-lookup"><span data-stu-id="34c9b-169">EF Core started tracking an entity</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | <span data-ttu-id="34c9b-170">La <xref:Microsoft.EntityFrameworkCore.EntityState> de una entidad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="34c9b-170">The <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | <span data-ttu-id="34c9b-171">Se generó un valor para una propiedad</span><span class="sxs-lookup"><span data-stu-id="34c9b-171">A value was generated for a property</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | <span data-ttu-id="34c9b-172">Se ha agregado o quitado entidades relacionadas con la navegación por omitir una colección</span><span class="sxs-lookup"><span data-stu-id="34c9b-172">A skip collection navigation has had related entities added or removed</span></span>

## <a name="the-model"></a><span data-ttu-id="34c9b-173">El modelo</span><span class="sxs-lookup"><span data-stu-id="34c9b-173">The model</span></span>

<span data-ttu-id="34c9b-174">El modelo que se usa para los ejemplos anteriores contiene los siguientes tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="34c9b-174">The model used for the examples above contains the following entity types:</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

<span data-ttu-id="34c9b-175">El modelo se configura principalmente por Convención, con solo unas pocas líneas en OnModelCreating:</span><span class="sxs-lookup"><span data-stu-id="34c9b-175">The model is mostly configured by convention, with just a few lines in OnModelCreating:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
