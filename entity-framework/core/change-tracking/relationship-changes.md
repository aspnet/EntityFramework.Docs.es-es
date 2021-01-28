---
title: 'Cambiar las claves externas y las navegaciones: EF Core'
description: Cómo cambiar las relaciones entre entidades manipulando las claves externas y las navegaciones
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: b1ebe77ed29291beeef3708b603db026c38bbbec
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983617"
---
# <a name="changing-foreign-keys-and-navigations"></a>Cambiar las claves externas y las navegaciones

## <a name="overview-of-foreign-keys-and-navigations"></a>Información general sobre las claves externas y las navegaciones

Las relaciones en un modelo de Entity Framework Core (EF Core) se representan mediante claves externas (claves externas). Un FK consta de una o más propiedades en la entidad dependiente o secundaria de la relación. Esta entidad dependiente/secundaria se asocia a una entidad de entidad de seguridad o primaria determinada cuando los valores de las propiedades de clave externa del objeto dependiente o secundario coinciden con los valores de las propiedades de clave principal o alternativa (PK) de la entidad de seguridad.

Las claves externas son una buena manera de almacenar y manipular relaciones en la base de datos, pero no son muy fáciles de usar cuando se trabaja con varias entidades relacionadas en el código de la aplicación. Por lo tanto, la mayoría de los modelos de EF Core también exponen "navegaciones" sobre la representación de FK. Las navegaciones forman referencias de C#/.NET entre instancias de la entidad que reflejan las asociaciones encontradas mediante la coincidencia de valores de clave externa con valores de clave principales o alternativos.

Las navegaciones se pueden usar en ambos lados de la relación, solo en un lado o, no solo en la propiedad FK. La propiedad FK se puede ocultar convirtiéndola en una [propiedad Shadow](xref:core/modeling/shadow-properties). Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre las relaciones de modelado.

> [!TIP]
> En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core. Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).

### <a name="example-model"></a>Modelo de ejemplo

El siguiente modelo contiene cuatro tipos de entidad con relaciones entre ellos. Los comentarios en el código indican qué propiedades son las claves externas, las claves principales y las navegaciones.

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
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
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

Las tres relaciones de este modelo son:

- Cada blog puede tener muchas publicaciones (de uno a varios):
  - `Blog` es la entidad de seguridad principal.
  - `Post` es el dependiente o el elemento secundario. Contiene la propiedad FK `Post.BlogId` , cuyo valor debe coincidir con el `Blog.Id` valor PK del blog relacionado.
  - `Post.Blog` es una navegación de referencia desde una publicación al blog asociado. `Post.Blog` es la navegación inversa para `Blog.Posts` .
  - `Blog.Posts` es una navegación de colección de un blog a todas las publicaciones asociadas. `Blog.Posts` es la navegación inversa para `Post.Blog` .
- Cada blog puede tener un recurso (uno a uno):
  - `Blog` es la entidad de seguridad principal.
  - `BlogAssets` es el dependiente o el elemento secundario. Contiene la propiedad FK `BlogAssets.BlogId` , cuyo valor debe coincidir con el `Blog.Id` valor PK del blog relacionado.
  - `BlogAssets.Blog` es una navegación de referencia entre los activos y el blog asociado. `BlogAssets.Blog` es la navegación inversa para `Blog.Assets` .
  - `Blog.Assets` es una navegación de referencia desde el blog hasta los recursos asociados. `Blog.Assets` es la navegación inversa para `BlogAssets.Blog` .
- Cada publicación puede tener muchas etiquetas y cada etiqueta puede tener muchas entradas (varios a varios):
  - Las relaciones de varios a varios son una capa adicional sobre las relaciones de 2 1 a muchos. Las relaciones de varios a varios se describen más adelante en este documento.
  - `Post.Tags` es una navegación de colección de una entrada a todas las etiquetas asociadas. `Post.Tags` es la navegación inversa para `Tag.Posts` .
  - `Tag.Posts` es una navegación de colección de una etiqueta a todas las publicaciones asociadas. `Tag.Posts` es la navegación inversa para `Post.Tags` .

Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre cómo modelar y configurar relaciones.

## <a name="relationship-fixup"></a>Corrección de relación

EF Core mantiene las navegaciones en la alineación con valores de clave externa y viceversa. Es decir, si un valor de clave externa cambia de modo que ahora hace referencia a otra entidad principal/primaria, se actualizan las navegaciones para reflejar este cambio. Del mismo modo, si se cambia una navegación, los valores de clave externa de las entidades implicadas se actualizan para reflejar este cambio. Esto se denomina "corrección de relación".

### <a name="fixup-by-query"></a>Corrección por consulta

La corrección se produce primero cuando se consultan las entidades desde la base de datos. La base de datos solo tiene valores de clave externa, por lo que cuando EF Core crea una instancia de entidad a partir de la base de datos, usa los valores de clave externa para establecer las navegaciones de referencia y agregar entidades a las navegaciones de la colección según corresponda. Por ejemplo, considere una consulta para los blogs y sus entradas y recursos asociados:

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

En cada blog, EF Core creará una instancia en primer lugar `Blog` . Después, a medida que cada publicación se carga desde la base de datos, su `Post.Blog` navegación de referencia se establece para que apunte al blog asociado. Del mismo modo, la publicación se agrega a la navegación de la `Blog.Posts` colección. Lo mismo sucede con `BlogAssets` , salvo que en este caso ambas son referencias. La `Blog.Assets` navegación se establece para que apunte a la instancia de activos y la `BlogAsserts.Blog` navegación se establece para que apunte a la instancia de blog.

Si se examina la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) después de que esta consulta muestre dos blogs, cada uno con un recurso y dos publicaciones en las que se realiza el seguimiento:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

La vista de depuración muestra tanto los valores de clave como las navegaciones. Las navegaciones se muestran mediante los valores de clave principal de las entidades relacionadas. Por ejemplo, `Posts: [{Id: 1}, {Id: 2}]` en la salida anterior indica que la `Blog.Posts` navegación de la colección contiene dos entradas relacionadas con las claves principales 1 y 2, respectivamente. Del mismo modo, para cada publicación asociada con el primer blog, la `Blog: {Id: 1}` línea indica que la `Post.Blog` navegación hace referencia al blog con la clave principal 1.

### <a name="fixup-to-locally-tracked-entities"></a>Corrección para entidades de las que se realiza un seguimiento local

La corrección de la relación también se produce entre las entidades devueltas desde una consulta de seguimiento y las entidades a las que el DbContext ya realiza un seguimiento. Por ejemplo, considere la posibilidad de ejecutar tres consultas independientes para blogs, publicaciones y recursos:

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_2)]
Al mirar de nuevo las vistas de depuración, después de la primera consulta, solo se realiza el seguimiento de los dos blogs:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

Las `Blog.Assets` navegaciones de referencia son NULL y las `Blog.Posts` navegaciones de la colección están vacías porque el contexto no está realizando actualmente un seguimiento de entidades asociadas.

Después de la segunda consulta, `Blogs.Assets` se han corregido las navegaciones de referencia para que apunten a las instancias recién controladas `BlogAsset` . Del mismo modo, las `BlogAssets.Blog` navegaciones de referencia se establecen para que apunten a la instancia de ya sometida a seguimiento adecuada `Blog` .

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

Por último, después de la tercera consulta, las `Blog.Posts` navegaciones de la colección contienen ahora todos los envíos relacionados y las `Post.Blog` referencias apuntan a la `Blog` instancia adecuada:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

Este es el mismo estado final que se ha logrado con la consulta única original, ya que EF Core las navegaciones fijas a medida que se realiza el seguimiento de las entidades, incluso cuando proceden de varias consultas diferentes.

> [!NOTE]
> La corrección nunca hace que se devuelvan más datos de la base de datos. Solo se conectan las entidades que ya devuelve la consulta o a las que el DbContext ya realiza un seguimiento. Vea [resolución de identidades en EF Core](xref:core/change-tracking/identity-resolution) para obtener información sobre cómo administrar duplicados al serializar entidades.

## <a name="changing-relationships-using-navigations"></a>Cambiar relaciones mediante navegación

La forma más fácil de cambiar la relación entre dos entidades es mediante la manipulación de una navegación, a la vez que se mantiene EF Core para corregir la navegación inversa y los valores de FK adecuadamente. Esto se puede hacer de la forma siguiente:

- Adición o eliminación de una entidad de una navegación de colección.
- Cambiar una navegación de referencia para apuntar a otra entidad o establecerla en NULL.

### <a name="adding-or-removing-from-collection-navigations"></a>Agregar o quitar de las navegaciones de la colección

Por ejemplo, vamos a migrar una de las entradas del blog de Visual Studio al blog de .NET. Esto requiere cargar primero los blogs y entradas y, a continuación, mover la entrada desde la colección de navegación de un blog a la colección de navegación del otro blog:

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>Aquí se necesita una llamada a porque el acceso a la vista de depuración no provoca la [detección automática de los cambios](xref:core/change-tracking/change-detection).

Esta es la vista de depuración impresa después de ejecutar el código anterior:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

La `Blog.Posts` navegación en el blog de .net tiene ahora tres publicaciones ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ). Del mismo modo, la `Blog.Posts` navegación en el blog de Visual Studio solo tiene una publicación ( `Posts: [{Id: 4}]` ). Esto se debe esperar, ya que el código cambió explícitamente estas colecciones.

Más Curiosamente, aunque el código no cambiara explícitamente la `Post.Blog` navegación, se ha corregido para que apunte al blog de Visual Studio ( `Blog: {Id: 1}` ). Además, el `Post.BlogId` valor de clave externa se ha actualizado para que coincida con el valor de clave principal del blog de .net. Este cambio en el valor de FK en se conserva en la base de datos cuando se llama a SaveChanges:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a>Cambiar las navegaciones de referencia

En el ejemplo anterior, una publicación se ha desplazado de un blog a otro manipulando la navegación por la colección de publicaciones en cada blog. Lo mismo se puede lograr si se cambia la navegación de `Post.Blog` referencia para que apunte al nuevo blog. Por ejemplo:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

La vista de depuración después de este cambio es _exactamente la misma_ que en el ejemplo anterior. Esto se debe a que EF Core detectó el cambio de navegación de referencia y, a continuación, corrigió las navegaciones de la colección y el valor de FK para que coincidan.

## <a name="changing-relationships-using-foreign-key-values"></a>Cambiar relaciones mediante valores de clave externa

En la sección anterior, las relaciones se manipulaban mediante navegación y los valores de clave externa se actualizaban automáticamente. Esta es la manera recomendada de manipular relaciones en EF Core. Sin embargo, también es posible manipular los valores de FK directamente. Por ejemplo, podemos trasladar una entrada de un blog a otro cambiando el valor de la `Post.BlogId` clave externa:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

Observe cómo esto es muy similar al cambio de la navegación de referencia, tal como se muestra en el ejemplo anterior.

La vista de depuración después de este cambio vuelve a ser _exactamente_ igual que en el caso de los dos ejemplos anteriores. Esto se debe a que EF Core detectó el cambio de valor de FK y, a continuación, corrigió las navegaciones de referencia y de colección para que coincidan.

> [!TIP]
> No Escriba código para manipular todos los valores de navegación y FK cada vez que cambie una relación. Este código es más complicado y debe garantizar cambios coherentes en las claves externas y navegaciones en todos los casos. Si es posible, basta con manipular una sola navegación, o quizás ambas navegaciones. Si es necesario, basta con manipular los valores de FK. Evite manipular los valores de navegación y de FK.

## <a name="fixup-for-added-or-deleted-entities"></a>Corrección para entidades agregadas o eliminadas

### <a name="adding-to-a-collection-navigation"></a>Agregar a una navegación de colección

EF Core realiza las siguientes acciones cuando [detecta](xref:core/change-tracking/change-detection) que se ha agregado una nueva entidad dependiente o secundaria a una navegación de colección:

- Si no se realiza el seguimiento de la entidad, se realiza el seguimiento. (La entidad normalmente estará en el `Added` Estado. Sin embargo, si el tipo de entidad está configurado para usar claves generadas y se establece el valor de clave principal, se realiza un seguimiento de la entidad en el `Unchanged` Estado.)
- Si la entidad está asociada a otra entidad de seguridad o primaria, se rompe la relación.
- La entidad se asocia con el principal/primario que posee la navegación de la colección.
- Las navegaciones y los valores de clave externa se corrigen para todas las entidades implicadas.

En función de esto, podemos ver que para trasladar una entrada de un blog a otro, realmente no es necesario quitarla de la antigua navegación de la colección antes de agregarla al nuevo. Por lo tanto, el código del ejemplo anterior se puede cambiar de:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

Por:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

EF Core observa que la publicación se ha agregado a un nuevo blog y la quita automáticamente de la colección en el primer blog.

### <a name="removing-from-a-collection-navigation"></a>Quitar de una navegación de colección

Al quitar una entidad dependiente o secundaria de la navegación por la colección de la entidad de seguridad o primaria, se produce el control de la relación con esa entidad de seguridad o primaria. Lo que sucede después depende de si la relación es opcional u obligatoria.

#### <a name="optional-relationships"></a>Relaciones opcionales

De forma predeterminada, para las relaciones opcionales, el valor de clave externa se establece en NULL. Esto significa que el dependiente o el elemento secundario ya no está asociado con _ninguna_ entidad de seguridad o elemento primario. Por ejemplo, vamos a cargar un blog y a publicar y, después, quitar una de las entradas de la navegación por la `Blog.Posts` colección:

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

Al examinar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de este cambio, se muestra lo siguiente:

- El valor de FK se ha `Post.BlogId` establecido en null ( `BlogId: <null> FK Modified Originally 1` )
- La `Post.Blog` navegación de referencia se ha establecido en null ( `Blog: <null>` )
- La publicación se ha quitado de la `Blog.Posts` navegación de colección ( `Posts: [{Id: 1}]` )

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

Observe que la publicación _no_ está marcada como `Deleted` . Está marcado como `Modified` para que el valor de FK de la base de datos se establezca en NULL cuando se llame a SaveChanges.

#### <a name="required-relationships"></a>Relaciones obligatorias

No se permite establecer el valor de FK en null (y normalmente no es posible) para las relaciones necesarias. Por lo tanto, el servidor de una relación necesaria significa que la entidad dependiente o secundaria debe volver a ser primaria para una nueva entidad de seguridad principal o quitarse de la base de datos cuando se llama a SaveChanges para evitar una infracción de la restricción referencial. Esto se conoce como "eliminación de huérfanos" y es el comportamiento predeterminado en EF Core para las relaciones necesarias.

Por ejemplo, vamos a cambiar la relación entre blog y publicaciones para que sea necesaria y, a continuación, ejecutar el mismo código que en el ejemplo anterior:

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

Al examinar la vista de depuración después de este cambio, se muestra lo siguiente:

- La publicación se ha marcado como tal `Deleted` que se eliminará de la base de datos cuando se llame a SaveChanges.
- La `Post.Blog` navegación de referencia se ha establecido en null ( `Blog: <null>` ).
- La publicación se ha quitado de la `Blog.Posts` navegación de colección ( `Posts: [{Id: 1}]` ).

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

Tenga en cuenta que el `Post.BlogId` permanece sin cambios desde que para una relación requerida no se puede establecer en NULL.

La llamada a SaveChanges da como resultado la eliminación de la publicación huérfana:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a>Eliminar el control de tiempo y volver a crear un elemento huérfano

De forma predeterminada, marcar huérfanos como `Deleted` sucede en cuanto se [detecta](xref:core/change-tracking/change-detection)el cambio de relación. Sin embargo, este proceso se puede retrasar hasta que se llame a SaveChanges. Esto puede ser útil para evitar la creación de huérfanos de entidades que se han quitado de una entidad de seguridad o un elemento primario, pero que se volverán a crear con una nueva entidad de seguridad o primaria antes de que se llame a SaveChanges. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> se usa para establecer este tiempo. Por ejemplo:

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

Después de quitar la entrada de la primera colección, el objeto no está marcado como `Deleted` en el ejemplo anterior. En su lugar, EF Core está realizando un seguimiento de que la relación se rompe, _aunque se trata de una relación requerida_. (El valor de FK se considera null en EF Core aunque no sea realmente nulo porque el tipo no admite valores NULL. Esto se conoce como "null conceptual").

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

La llamada a SaveChanges en este momento daría lugar a la eliminación de la publicación huérfana. Sin embargo, si, como en el ejemplo anterior, post está asociado a un nuevo blog antes de que se llame a SaveChanges, se corregirá correctamente en ese nuevo blog y ya no se considerará huérfano:

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

El SaveChanges llamado en este punto actualizará la publicación en la base de datos en lugar de eliminarla.

También es posible desactivar la eliminación automática de huérfanos. Esto producirá una excepción si se llama a SaveChanges mientras se realiza el seguimiento de un huérfano. Por ejemplo, este código:

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

Producirá esta excepción:

> System. InvalidOperationException: se ha interrumpido la asociación entre las entidades ' blog ' y ' post ' con el valor de clave ' {BlogId: 1} ', pero la relación está marcada como requerida o es implícitamente necesaria porque la clave externa no admite valores NULL. Si se debe eliminar la entidad dependiente o secundaria cuando se interrumpe una relación requerida, configure la relación para utilizar las eliminaciones en cascada.

La eliminación de huérfanos, así como las eliminaciones en cascada, se puede forzar en cualquier momento llamando a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . La combinación de esto con el establecimiento del tiempo de eliminación de huérfanos en `Never` garantizará que los huérfanos nunca se eliminan a menos que se indique explícitamente EF Core.

### <a name="changing-a-reference-navigation"></a>Cambiar una navegación de referencia

Cambiar la navegación de referencia de una relación de uno a varios tiene el mismo efecto que cambiar la navegación por la colección en el otro extremo de la relación. Establecer la navegación de referencia de dependent/Child a NULL equivale a quitar la entidad de la navegación de la colección de la entidad de seguridad principal. Todos los cambios de corrección y base de datos se realizan como se describe en la sección anterior, lo que incluye convertir la entidad en huérfana si se requiere la relación.

#### <a name="optional-one-to-one-relationships"></a>Relaciones de uno a uno opcionales

En el caso de las relaciones uno a uno, el cambio de una navegación de referencia hace que cualquier relación anterior se haga más grave. En el caso de las relaciones opcionales, esto significa que el valor de FK del dependiente o elemento secundario relacionado anteriormente se establece en NULL. Por ejemplo:

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

La vista de depuración antes de llamar a SaveChanges muestra que los recursos nuevos han reemplazado a los activos existentes, que ahora está marcado como `Modified` con un `BlogAssets.BlogId` valor de FK nulo:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

Esto da como resultado una actualización e inserción cuando se llama a SaveChanges:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a>Relaciones uno a uno necesarios

Si se ejecuta el mismo código que en el ejemplo anterior, pero esta vez con una relación uno a uno necesaria, se muestra que el objeto asociado anteriormente `BlogAssets` ahora está marcado como `Deleted` , ya que se convierte en huérfano cuando el nuevo `BlogAssets` ocupa su lugar:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

Esto da como resultado una eliminación de e Insert cuando se llama a SaveChanges:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

La temporización de marcar huérfanos como eliminados se puede cambiar de la misma manera que se muestra para las navegaciones de la colección y tiene los mismos efectos.

### <a name="deleting-an-entity"></a>Eliminar una entidad

#### <a name="optional-relationships"></a>Relaciones opcionales

Cuando una entidad se marca como `Deleted` , por ejemplo llamando a <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , se quitan las referencias a la entidad eliminada de las navegaciones de otras entidades. En el caso de las relaciones opcionales, los valores de FK de entidades dependientes se establecen en NULL.

Por ejemplo, vamos a marcar el blog de Visual Studio como `Deleted` :

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) antes de llamar a SaveChanges, se muestra:

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

Tenga en lo siguiente:

- El blog se marca como `Deleted`.
- Los recursos relacionados con el blog eliminado tienen un valor de FK null ( `BlogId: <null> FK Modified Originally 2` ) y una navegación de referencia nula ( `Blog: <null>` )
- Cada publicación relacionada con el blog eliminado tiene un valor de FK null ( `BlogId: <null> FK Modified Originally 2` ) y una navegación de referencia nula ( `Blog: <null>` )

#### <a name="required-relationships"></a>Relaciones obligatorias

El comportamiento de corrección para las relaciones necesarias es el mismo que para las relaciones opcionales, salvo que las entidades dependientes o secundarias se marcan como `Deleted` porque no pueden existir sin una entidad de seguridad o un elemento primario y se deben quitar de la base de datos cuando se llama a SaveChanges para evitar una excepción de restricción referencial. Esto se conoce como "eliminación en cascada" y es el comportamiento predeterminado en EF Core para las relaciones necesarias. Por ejemplo, si se ejecuta el mismo código que en el ejemplo anterior, pero con una relación requerida, se obtiene la siguiente vista de depuración antes de que se llame a SaveChanges:

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

Como se esperaba, los elementos secundarios o dependientes se marcan ahora como `Deleted` . Sin embargo, tenga en cuenta que las navegaciones en las entidades eliminadas _no_ han cambiado. Esto puede parecer extraño, pero evita la fragmentación completa de un gráfico de entidades eliminado borrando todas las navegaciones. Es decir, el blog, el recurso y las entradas siguen conformando un gráfico de entidades incluso después de haberse eliminado. Esto hace que sea mucho más fácil anular la eliminación de un gráfico de entidades que en el caso de EF6 en el que se ha purgado el gráfico.

#### <a name="cascade-delete-timing-and-re-parenting"></a>Temporización de eliminación en cascada y Realquiler

De forma predeterminada, la eliminación en cascada se produce tan pronto como la entidad de seguridad principal se marca como `Deleted` . Esto es lo mismo que para eliminar huérfanos, como se ha descrito anteriormente. Al igual que con la eliminación de huérfanos, este proceso se puede retrasar hasta que se llame a SaveChanges, o incluso deshabilitado por completo, estableciendo la configuración <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> apropiada. Esto resulta útil de la misma manera que para eliminar huérfanos, lo que incluye la reutilización de los elementos secundarios o dependientes de la eliminación de una entidad de seguridad principal.

Las eliminaciones en cascada, así como la eliminación de huérfanos, se pueden forzar en cualquier momento llamando a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . Al combinarlo con el establecimiento del control de tiempo de eliminación en cascada en `Never` se garantizará que las eliminaciones en cascada no se produzcan a menos que se indique explícitamente EF Core.

> [!TIP]
> La eliminación en cascada y la eliminación de entidades huérfanas están estrechamente relacionadas. Ambas dan como resultado la eliminación de entidades dependientes o secundarias cuando se interrumpe su relación con la entidad de seguridad o primaria requerida. En el caso de la eliminación en cascada, esta interrupción de la relación tiene lugar porque se elimina la propia entidad de seguridad o primaria. En el caso de las entidades huérfanas, la entidad de seguridad o primaria sigue existiendo, pero ya no está relacionada con las entidades dependientes o secundarias.

## <a name="many-to-many-relationships"></a>Relaciones de varios a varios

Las relaciones de varios a varios en EF Core se implementan mediante una entidad join. Cada lado, la relación de varios a varios está relacionada con esta entidad de combinación con una relación de uno a varios. Antes de EF Core 5,0, esta entidad join tenía que definirse y asignarse explícitamente. A partir de EF Core 5,0, se puede crear de forma implícita y oculta. Sin embargo, en ambos casos, el comportamiento subyacente es el mismo. En primer lugar, veremos este comportamiento subyacente para comprender cómo funciona el seguimiento de relaciones de varios a varios.

### <a name="how-many-to-many-relationships-work"></a>Cómo funcionan las relaciones varios a varios

Tenga en cuenta este modelo de EF Core que crea una relación de varios a varios entre entradas y etiquetas mediante un tipo de entidad de combinación definido explícitamente:

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

Observe que el `PostTag` tipo de entidad de combinación contiene dos propiedades de clave externa. En este modelo, para que una publicación esté relacionada con una etiqueta, debe haber una entidad de combinación PostTag en la que el `PostTag.PostId` valor de clave externa coincida con el `Post.Id` valor de clave principal y donde el `PostTag.TagId` valor de clave externa coincida con el `Tag.Id` valor de clave principal. Por ejemplo:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) después de ejecutar este código, se muestra que la entrada y la etiqueta están relacionadas con la nueva `PostTag` entidad de combinación:

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

Observe que las navegaciones de la colección en `Post` y `Tag` se han corregido, al igual que las navegaciones de referencia en `PostTag` . Estas relaciones se pueden manipular mediante navegaciones en lugar de valores de FK, al igual que en todos los ejemplos anteriores. Por ejemplo, el código anterior se puede modificar para agregar la relación estableciendo las navegaciones de referencia en la entidad de combinación:

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

Esto produce exactamente el mismo cambio en claves externas y en las navegaciones que en el ejemplo anterior.

### <a name="skip-navigations"></a>Omitir navegaciones

> [!NOTE]
> Omitir las navegaciones se introdujeron en EF Core 5,0.

Manipular la tabla de combinación manualmente puede resultar complicado. A partir de EF Core 5,0, las relaciones varios a varios se pueden manipular directamente mediante las navegaciones de colección especiales que "omiten" la entidad de combinación. Por ejemplo, se pueden agregar dos navegaciones de omisión al modelo anterior; uno de las etiquetas post a y el otro de la etiqueta a posts:

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

Esta relación de varios a varios requiere la siguiente configuración para garantizar que las navegaciones de omisión y las navegaciones normales se usan para la misma relación de varios a varios:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre la asignación de relaciones varios a varios.

Omitir las navegaciones tienen el aspecto y se comportan como navegación de colección normal. Sin embargo, el modo en que trabajan con valores de clave externa es diferente. Vamos a asociar una publicación con una etiqueta, pero esta vez mediante una omisión de navegación:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

Tenga en cuenta que este código no usa la entidad join. En su lugar, simplemente agrega una entidad a una colección de navegación de la misma manera que si se tratara de una relación de uno a varios. La vista de depuración resultante es esencialmente la misma que antes:

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

Observe que `PostTag` se ha creado automáticamente una instancia de la entidad de combinación con los valores de FK establecidos en los valores PK de la etiqueta y la publicación que ahora están asociadas. Todas las referencias normales y las navegaciones de colección se han corregido para que coincidan con estos valores de FK. Además, dado que este modelo contiene saltos de navegación, también se han corregido. En concreto, aunque agregamos la etiqueta a la `Post.Tags` navegación de omitir, la `Tag.Posts` navegación inversa omitir en el otro lado de esta relación también se ha corregido para que contenga la publicación asociada.

Merece la pena tener en cuenta que las relaciones de varios a varios subyacentes todavía se pueden manipular directamente aunque se hayan superpuesto en la parte superior las navegaciones de omitir. Por ejemplo, la etiqueta y la publicación pueden asociarse como hicimos antes de introducir omitir navegación:

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

O usando valores de FK:

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

Esto hará que las navegaciones de omisión se solucionen correctamente, lo que da lugar a la misma salida de la vista de depuración que en el ejemplo anterior.

### <a name="skip-navigations-only"></a>Omitir solo navegación

En la sección anterior hemos agregado omitir las navegaciones, _además_ de definir totalmente las dos relaciones uno a varios subyacentes. Esto resulta útil para ilustrar lo que sucede con los valores de FK, pero a menudo no es necesario. En su lugar, se puede definir la relación de varios a varios usando _solo las navegaciones de omisión_. Así es como se define la relación de varios a varios en el modelo en la parte superior de este documento. Con este modelo, se puede volver a asociar una publicación y una etiqueta agregando un comentario a la `Tag.Posts` navegación por omitir (o, como alternativa, agregando una etiqueta a la `Post.Tags` navegación omitida):

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

Al examinar la vista de depuración después de hacer este cambio, se revela que EF Core ha creado una instancia de `Dictionary<string, object>` para representar la entidad de combinación. Esta entidad join contiene `PostsId` `TagsId` las propiedades de clave externa y que se han establecido para que coincidan con los valores PK de la entrada y la etiqueta que están asociados.

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre las entidades de combinación implícitas y el uso de `Dictionary<string, object>` tipos de entidad.

> [!IMPORTANT]
> El tipo CLR que se usa para los tipos de entidad de combinación por Convención puede cambiar en futuras versiones para mejorar el rendimiento. No dependa del tipo de combinación a menos que se haya `Dictionary<string, object>` configurado explícitamente.

### <a name="join-entities-with-payloads"></a>Unir entidades con cargas

Hasta ahora todos los ejemplos han utilizado un tipo de entidad de combinación (ya sea explícito o implícito) que solo contiene las dos propiedades de clave externa necesarias para la relación de varios a varios. Ninguno de estos valores de FK debe establecerse explícitamente por la aplicación al manipular las relaciones, ya que sus valores proceden de las propiedades de clave principal de las entidades relacionadas. Esto permite a EF Core crear instancias de la entidad de combinación sin datos que faltan.

#### <a name="payloads-with-generated-values"></a>Cargas con valores generados

EF Core admite la adición de propiedades adicionales al tipo de entidad de combinación. Esto se conoce como la asignación de una "carga" a la entidad de combinación. Por ejemplo, vamos a agregar `TaggedOn` la propiedad a la `PostTag` entidad de combinación:

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

Esta propiedad de carga no se establecerá cuando EF Core crea una instancia de entidad de combinación. La manera más común de tratar esto es usar propiedades de carga con valores generados automáticamente. Por ejemplo, la `TaggedOn` propiedad se puede configurar para usar una marca de tiempo generada por el almacén cuando se inserta cada nueva entidad:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

Ahora se puede etiquetar una publicación de la misma manera que antes:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) después de llamar a SaveChanges, se muestra que la propiedad payload se ha establecido correctamente:

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a>Establecer explícitamente los valores de carga

Después de en el ejemplo anterior, vamos a agregar una propiedad de carga que no usa un valor generado automáticamente:

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

Ahora se puede etiquetar una publicación de la misma manera que antes, y la entidad de combinación se creará automáticamente. A continuación, se puede tener acceso a esta entidad mediante uno de los mecanismos descritos en acceso a entidades de las que se ha [realizado un seguimiento](xref:core/change-tracking/entity-entries). Por ejemplo, el código siguiente utiliza <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> para tener acceso a la instancia de la entidad de combinación:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

Una vez que se encuentra la entidad de combinación, se puede manipular de la manera normal; en este ejemplo, para establecer la `TaggedBy` propiedad de carga antes de llamar a SaveChanges.

> [!NOTE]
> Tenga en cuenta que <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> aquí se requiere una llamada a para proporcionar a EF Core una oportunidad de detectar el cambio de propiedad de navegación y crear la instancia de la entidad de combinación antes de que `Find` se use. Consulte [detección y notificaciones de cambios](xref:core/change-tracking/change-detection) para obtener más información.

Como alternativa, la entidad de combinación se puede crear explícitamente para asociar una publicación con una etiqueta. Por ejemplo:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

Por último, otra manera de establecer los datos de carga es reemplazar <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o usar el <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> evento para procesar entidades antes de actualizar la base de datos. Por ejemplo:

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
