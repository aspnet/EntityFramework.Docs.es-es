---
title: 'Depuración de Change Tracker: EF Core'
description: Uso de ChangeTracker DebugView y mensajes de registro para depurar EF Core el seguimiento de cambios
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: 76108120cf7f532d085fef12121bb639b065add0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129639"
---
# <a name="change-tracker-debugging"></a>Depuración de Change Tracker

El seguimiento de cambios de Entity Framework Core (EF Core) genera dos tipos de resultados para ayudar con la depuración:

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType>Proporciona una vista legible de todas las entidades de las que se realiza un seguimiento.
- Los mensajes de registro de nivel de depuración se generan cuando el seguimiento de cambios detecta el estado y corrige las relaciones

> [!TIP]
> En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core. Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo desde GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging).

## <a name="change-tracker-debug-view"></a>Vista de depuración de Change Tracker

Se puede tener acceso a la vista de depuración Change Tracker en el depurador del IDE. Por ejemplo, con Visual Studio:

![Obtener acceso a la vista de depuración del seguimiento de cambios desde el depurador de Visual Studio](_static/debug-view.png)

También se puede acceder a él directamente desde el código, por ejemplo, para enviar la vista de depuración a la consola:

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

La vista de depuración tiene una forma abreviada y un formato largo. La forma abreviada muestra las entidades sometidas a seguimiento, su estado y sus valores de clave. El formato largo también incluye todos los valores de propiedad y navegación y el estado.

### <a name="the-short-view"></a>Vista corta

Echemos un vistazo a un ejemplo de vista de depuración con el modelo que se muestra al final de este documento. En primer lugar, realizaremos un seguimiento de algunas entidades y las colocaremos en algunos Estados diferentes, por lo que tenemos buenos datos de seguimiento de cambios para ver:

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

La impresión de la vista corta en este punto, como se muestra arriba, da como resultado el siguiente resultado:

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

Aviso:

- Cada entidad de la que se realiza un seguimiento se muestra con su valor de clave principal (PK). Por ejemplo, `Blog {Id: 1}`.
- Si la entidad es un tipo de entidad de tipo compartido, también se muestra el tipo CLR. Por ejemplo, `PostTag (Dictionary<string, object>)`.
- <xref:Microsoft.EntityFrameworkCore.EntityState>Se muestra a continuación. Será uno de los de `Unchanged` , `Added` , `Modified` o `Deleted` .
- A continuación se muestran los valores de cualquier clave alternativa (AKs). Por ejemplo, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.
- Por último, se muestran los valores de cualquier clave externa (claves externas). Por ejemplo, `FK {PostsId: 4} FK {TagsId: 2}`.

### <a name="the-long-view"></a>Vista larga

La vista larga se puede enviar a la consola de la misma manera que la vista corta:

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

La salida para el mismo estado que la vista abreviada anterior es:

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

Cada entidad de la que se realiza el seguimiento y su estado se muestra como antes. Sin embargo, la vista larga también muestra los valores de propiedad y de navegación.

#### <a name="property-values"></a>Valores de propiedad

Para cada propiedad, la vista larga muestra si la propiedad forma parte de una clave principal (PK), una clave alternativa (AK) o una clave externa (FK). Por ejemplo:

- `Blog.Id` es una propiedad de clave principal: `Id: 1 PK`
- `Blog.AssetsId` es una propiedad de clave alternativa: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`
- `Post.BlogId` es una propiedad de clave externa: `BlogId: 2 FK`
- `BlogAssets.Id` es tanto una clave principal como una propiedad de clave externa: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`

Los valores de propiedad que se han modificado se marcan como tales y también se muestra el valor original de la propiedad. Por ejemplo, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.

Por último, `Added` las entidades con valores de clave temporales indican que el valor es temporal. Por ejemplo, `Id: -2147482643 PK Temporary`.

#### <a name="navigation-values"></a>Valores de navegación

Los valores de navegación se muestran mediante los valores de clave principal de las entidades a las que hace referencia la navegación. Por ejemplo, en la salida anterior, post 3 está relacionado con el blog 2. Esto significa que la `Post.Blog` navegación señala a la `Blog` instancia de con el identificador 2. Esto se muestra como `Blog: {Id: 2}` .

Lo mismo sucede con las navegaciones de la colección, salvo que en este caso puede haber varias entidades relacionadas. Por ejemplo, la navegación de colección `Blog.Posts` contiene tres entidades, con los valores de clave 1, 2 y-2147482643, respectivamente. Esto se muestra como `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` .

## <a name="change-tracker-logging"></a>Registro de seguimiento de cambios

El seguimiento de cambios registra los mensajes en el `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> cuando [detecta cambios de propiedad o navegación](xref:core/change-tracking/debug-views). Por ejemplo, cuando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> se llama a en el código en la parte superior de este documento y el [registro de depuración está habilitado](xref:core/logging-events-diagnostics/index), se generan los registros siguientes:

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

En la tabla siguiente se resumen los mensajes de registro de seguimiento de cambios:

| Id. de evento                                                                                                               | Descripción
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> se está iniciando
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> ha finalizado
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | Ha cambiado un valor de propiedad normal
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | Ha cambiado un valor de propiedad de clave externa
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | Se ha agregado o quitado entidades relacionadas con una navegación de colección que no omite.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | Una navegación de referencia se ha cambiado para apuntar a otra entidad o debe establecerse en NULL.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | EF Core inició el seguimiento de una entidad
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | La <xref:Microsoft.EntityFrameworkCore.EntityState> de una entidad ha cambiado
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | Se generó un valor para una propiedad
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | Se ha agregado o quitado entidades relacionadas con la navegación por omitir una colección

## <a name="the-model"></a>El modelo

El modelo que se usa para los ejemplos anteriores contiene los siguientes tipos de entidad:

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

El modelo se configura principalmente por Convención, con solo unas pocas líneas en OnModelCreating:

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
