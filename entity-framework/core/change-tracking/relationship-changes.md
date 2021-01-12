---
title: 'Cambiar las claves externas y las navegaciones: EF Core'
description: Cómo cambiar las relaciones entre entidades manipulando las claves externas y las navegaciones
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: ac2110509b6748e85411dbb14989522465925ecf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129648"
---
# <a name="changing-foreign-keys-and-navigations"></a><span data-ttu-id="31529-103">Cambiar las claves externas y las navegaciones</span><span class="sxs-lookup"><span data-stu-id="31529-103">Changing Foreign Keys and Navigations</span></span>

## <a name="overview-of-foreign-keys-and-navigations"></a><span data-ttu-id="31529-104">Información general sobre las claves externas y las navegaciones</span><span class="sxs-lookup"><span data-stu-id="31529-104">Overview of foreign keys and navigations</span></span>

<span data-ttu-id="31529-105">Las relaciones en un modelo de Entity Framework Core (EF Core) se representan mediante claves externas (claves externas).</span><span class="sxs-lookup"><span data-stu-id="31529-105">Relationships in an Entity Framework Core (EF Core) model are represented using foreign keys (FKs).</span></span> <span data-ttu-id="31529-106">Un FK consta de una o más propiedades en la entidad dependiente o secundaria de la relación.</span><span class="sxs-lookup"><span data-stu-id="31529-106">An FK consists of one or more properties on the dependent or child entity in the relationship.</span></span> <span data-ttu-id="31529-107">Esta entidad dependiente/secundaria se asocia a una entidad de entidad de seguridad o primaria determinada cuando los valores de las propiedades de clave externa del objeto dependiente o secundario coinciden con los valores de las propiedades de clave principal o alternativa (PK) de la entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="31529-107">This dependent/child entity is associated with a given principal/parent entity when the values of the foreign key properties on the dependent/child match the values of the alternate or primary key (PK) properties on the principal/parent.</span></span>

<span data-ttu-id="31529-108">Las claves externas son una buena manera de almacenar y manipular relaciones en la base de datos, pero no son muy fáciles de usar cuando se trabaja con varias entidades relacionadas en el código de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31529-108">Foreign keys are a good way to store and manipulate relationships in the database, but are not very friendly when working with multiple related entities in application code.</span></span> <span data-ttu-id="31529-109">Por lo tanto, la mayoría de los modelos de EF Core también exponen "navegaciones" sobre la representación de FK.</span><span class="sxs-lookup"><span data-stu-id="31529-109">Therefore, most EF Core models also layer "navigations" over the FK representation.</span></span> <span data-ttu-id="31529-110">Las navegaciones forman referencias de C#/.NET entre instancias de la entidad que reflejan las asociaciones encontradas mediante la coincidencia de valores de clave externa con valores de clave principales o alternativos.</span><span class="sxs-lookup"><span data-stu-id="31529-110">Navigations form C#/.NET references between entity instances that reflect the associations found by matching foreign key values to primary or alternate key values.</span></span>

<span data-ttu-id="31529-111">Las navegaciones se pueden usar en ambos lados de la relación, solo en un lado o, no solo en la propiedad FK.</span><span class="sxs-lookup"><span data-stu-id="31529-111">Navigations can be used on both sides of the relationship, on one side only, or not at all, leaving only the FK property.</span></span> <span data-ttu-id="31529-112">La propiedad FK se puede ocultar convirtiéndola en una [propiedad Shadow](xref:core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="31529-112">The FK property can be hidden by making it a [shadow property](xref:core/modeling/shadow-properties).</span></span> <span data-ttu-id="31529-113">Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre las relaciones de modelado.</span><span class="sxs-lookup"><span data-stu-id="31529-113">See [Relationships](xref:core/modeling/relationships) for more information on modelling relationships.</span></span>

> [!TIP]
> <span data-ttu-id="31529-114">En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="31529-114">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="31529-115">Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.</span><span class="sxs-lookup"><span data-stu-id="31529-115">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="31529-116">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo desde GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span><span class="sxs-lookup"><span data-stu-id="31529-116">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span></span>

### <a name="example-model"></a><span data-ttu-id="31529-117">Modelo de ejemplo</span><span class="sxs-lookup"><span data-stu-id="31529-117">Example model</span></span>

<span data-ttu-id="31529-118">El siguiente modelo contiene cuatro tipos de entidad con relaciones entre ellos.</span><span class="sxs-lookup"><span data-stu-id="31529-118">The following model contains four entity types with relationships between them.</span></span> <span data-ttu-id="31529-119">Los comentarios en el código indican qué propiedades son las claves externas, las claves principales y las navegaciones.</span><span class="sxs-lookup"><span data-stu-id="31529-119">The comments in the code indicate which properties are foreign keys, primary keys, and navigations.</span></span>

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

<span data-ttu-id="31529-120">Las tres relaciones de este modelo son:</span><span class="sxs-lookup"><span data-stu-id="31529-120">The three relationships in this model are:</span></span>

- <span data-ttu-id="31529-121">Cada blog puede tener muchas publicaciones (de uno a varios):</span><span class="sxs-lookup"><span data-stu-id="31529-121">Each blog can have many posts (one-to-many):</span></span>
  - <span data-ttu-id="31529-122">`Blog` es la entidad de seguridad principal.</span><span class="sxs-lookup"><span data-stu-id="31529-122">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="31529-123">`Post` es el dependiente o el elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="31529-123">`Post` is the dependent/child.</span></span> <span data-ttu-id="31529-124">Contiene la propiedad FK `Post.BlogId` , cuyo valor debe coincidir con el `Blog.Id` valor PK del blog relacionado.</span><span class="sxs-lookup"><span data-stu-id="31529-124">It contains the FK property `Post.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="31529-125">`Post.Blog` es una navegación de referencia desde una publicación al blog asociado.</span><span class="sxs-lookup"><span data-stu-id="31529-125">`Post.Blog` is a reference navigation from a post to the associated blog.</span></span> <span data-ttu-id="31529-126">`Post.Blog` es la navegación inversa para `Blog.Posts` .</span><span class="sxs-lookup"><span data-stu-id="31529-126">`Post.Blog` is the inverse navigation for `Blog.Posts`.</span></span>
  - <span data-ttu-id="31529-127">`Blog.Posts` es una navegación de colección de un blog a todas las publicaciones asociadas.</span><span class="sxs-lookup"><span data-stu-id="31529-127">`Blog.Posts` is a collection navigation from a blog to all the associated posts.</span></span> <span data-ttu-id="31529-128">`Blog.Posts` es la navegación inversa para `Post.Blog` .</span><span class="sxs-lookup"><span data-stu-id="31529-128">`Blog.Posts` is the inverse navigation for `Post.Blog`.</span></span>
- <span data-ttu-id="31529-129">Cada blog puede tener un recurso (uno a uno):</span><span class="sxs-lookup"><span data-stu-id="31529-129">Each blog can have one assets (one-to-one):</span></span>
  - <span data-ttu-id="31529-130">`Blog` es la entidad de seguridad principal.</span><span class="sxs-lookup"><span data-stu-id="31529-130">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="31529-131">`BlogAssets` es el dependiente o el elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="31529-131">`BlogAssets` is the dependent/child.</span></span> <span data-ttu-id="31529-132">Contiene la propiedad FK `BlogAssets.BlogId` , cuyo valor debe coincidir con el `Blog.Id` valor PK del blog relacionado.</span><span class="sxs-lookup"><span data-stu-id="31529-132">It contains the FK property `BlogAssets.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="31529-133">`BlogAssets.Blog` es una navegación de referencia entre los activos y el blog asociado.</span><span class="sxs-lookup"><span data-stu-id="31529-133">`BlogAssets.Blog` is a reference navigation from the assets to the associated blog.</span></span> <span data-ttu-id="31529-134">`BlogAssets.Blog` es la navegación inversa para `Blog.Assets` .</span><span class="sxs-lookup"><span data-stu-id="31529-134">`BlogAssets.Blog` is the inverse navigation for `Blog.Assets`.</span></span>
  - <span data-ttu-id="31529-135">`Blog.Assets` es una navegación de referencia desde el blog hasta los recursos asociados.</span><span class="sxs-lookup"><span data-stu-id="31529-135">`Blog.Assets` is a reference navigation from the blog to the associated assets.</span></span> <span data-ttu-id="31529-136">`Blog.Assets` es la navegación inversa para `BlogAssets.Blog` .</span><span class="sxs-lookup"><span data-stu-id="31529-136">`Blog.Assets` is the inverse navigation for `BlogAssets.Blog`.</span></span>
- <span data-ttu-id="31529-137">Cada publicación puede tener muchas etiquetas y cada etiqueta puede tener muchas entradas (varios a varios):</span><span class="sxs-lookup"><span data-stu-id="31529-137">Each post can have many tags and each tag can have many posts (many-to-many):</span></span>
  - <span data-ttu-id="31529-138">Las relaciones de varios a varios son una capa adicional sobre las relaciones de 2 1 a muchos.</span><span class="sxs-lookup"><span data-stu-id="31529-138">Many-to-many relationships are a further layer over two one-to-many relationships.</span></span> <span data-ttu-id="31529-139">Las relaciones de varios a varios se describen más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="31529-139">Many-to-many relationships are covered later in this document.</span></span>
  - <span data-ttu-id="31529-140">`Post.Tags` es una navegación de colección de una entrada a todas las etiquetas asociadas.</span><span class="sxs-lookup"><span data-stu-id="31529-140">`Post.Tags` is a collection navigation from a post to all the associated tags.</span></span> <span data-ttu-id="31529-141">`Post.Tags` es la navegación inversa para `Tag.Posts` .</span><span class="sxs-lookup"><span data-stu-id="31529-141">`Post.Tags` is the inverse navigation for `Tag.Posts`.</span></span>
  - <span data-ttu-id="31529-142">`Tag.Posts` es una navegación de colección de una etiqueta a todas las publicaciones asociadas.</span><span class="sxs-lookup"><span data-stu-id="31529-142">`Tag.Posts` is a collection navigation from a tag to all the associated posts.</span></span> <span data-ttu-id="31529-143">`Tag.Posts` es la navegación inversa para `Post.Tags` .</span><span class="sxs-lookup"><span data-stu-id="31529-143">`Tag.Posts` is the inverse navigation for `Post.Tags`.</span></span>

<span data-ttu-id="31529-144">Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre cómo modelar y configurar relaciones.</span><span class="sxs-lookup"><span data-stu-id="31529-144">See [Relationships](xref:core/modeling/relationships) for more information on how to model and configure relationships.</span></span>

## <a name="relationship-fixup"></a><span data-ttu-id="31529-145">Corrección de relación</span><span class="sxs-lookup"><span data-stu-id="31529-145">Relationship fixup</span></span>

<span data-ttu-id="31529-146">EF Core mantiene las navegaciones en la alineación con valores de clave externa y viceversa.</span><span class="sxs-lookup"><span data-stu-id="31529-146">EF Core keeps navigations in alignment with foreign key values and vice versa.</span></span> <span data-ttu-id="31529-147">Es decir, si un valor de clave externa cambia de modo que ahora hace referencia a otra entidad principal/primaria, se actualizan las navegaciones para reflejar este cambio.</span><span class="sxs-lookup"><span data-stu-id="31529-147">That is, if a foreign key value changes such that it now refers to a different principal/parent entity, then the navigations are updated to reflect this change.</span></span> <span data-ttu-id="31529-148">Del mismo modo, si se cambia una navegación, los valores de clave externa de las entidades implicadas se actualizan para reflejar este cambio.</span><span class="sxs-lookup"><span data-stu-id="31529-148">Likewise, if a navigation is changed, then the foreign key values of the entities involved are updated to reflect this change.</span></span> <span data-ttu-id="31529-149">Esto se denomina "corrección de relación".</span><span class="sxs-lookup"><span data-stu-id="31529-149">This is called "relationship fixup".</span></span>

### <a name="fixup-by-query"></a><span data-ttu-id="31529-150">Corrección por consulta</span><span class="sxs-lookup"><span data-stu-id="31529-150">Fixup by query</span></span>

<span data-ttu-id="31529-151">La corrección se produce primero cuando se consultan las entidades desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="31529-151">Fixup first occurs when entities are queried from the database.</span></span> <span data-ttu-id="31529-152">La base de datos solo tiene valores de clave externa, por lo que cuando EF Core crea una instancia de entidad a partir de la base de datos, usa los valores de clave externa para establecer las navegaciones de referencia y agregar entidades a las navegaciones de la colección según corresponda.</span><span class="sxs-lookup"><span data-stu-id="31529-152">The database has only foreign key values, so when EF Core creates an entity instance from the database it uses the foreign key values to set reference navigations and add entities to collection navigations as appropriate.</span></span> <span data-ttu-id="31529-153">Por ejemplo, considere una consulta para los blogs y sus entradas y recursos asociados:</span><span class="sxs-lookup"><span data-stu-id="31529-153">For example, consider a query for blogs and its associated posts and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

<span data-ttu-id="31529-154">En cada blog, EF Core creará una instancia en primer lugar `Blog` .</span><span class="sxs-lookup"><span data-stu-id="31529-154">For each blog, EF Core will first create a `Blog` instance.</span></span> <span data-ttu-id="31529-155">Después, a medida que cada publicación se carga desde la base de datos, su `Post.Blog` navegación de referencia se establece para que apunte al blog asociado.</span><span class="sxs-lookup"><span data-stu-id="31529-155">Then, as each post is loaded from the database its `Post.Blog` reference navigation is set to point to the associated blog.</span></span> <span data-ttu-id="31529-156">Del mismo modo, la publicación se agrega a la navegación de la `Blog.Posts` colección.</span><span class="sxs-lookup"><span data-stu-id="31529-156">Likewise, the post is added to the `Blog.Posts` collection navigation.</span></span> <span data-ttu-id="31529-157">Lo mismo sucede con `BlogAssets` , salvo que en este caso ambas son referencias.</span><span class="sxs-lookup"><span data-stu-id="31529-157">The same thing happens with `BlogAssets`, except in this case both navigations are references.</span></span> <span data-ttu-id="31529-158">La `Blog.Assets` navegación se establece para que apunte a la instancia de activos y la `BlogAsserts.Blog` navegación se establece para que apunte a la instancia de blog.</span><span class="sxs-lookup"><span data-stu-id="31529-158">The `Blog.Assets` navigation is set to point to the assets instance, and the `BlogAsserts.Blog` navigation is set to point to the blog instance.</span></span>

<span data-ttu-id="31529-159">Si se examina la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) después de que esta consulta muestre dos blogs, cada uno con un recurso y dos publicaciones en las que se realiza el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="31529-159">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after this query shows two blogs, each with one assets and two posts being tracked:</span></span>

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

<span data-ttu-id="31529-160">La vista de depuración muestra tanto los valores de clave como las navegaciones.</span><span class="sxs-lookup"><span data-stu-id="31529-160">The debug view shows both key values and navigations.</span></span> <span data-ttu-id="31529-161">Las navegaciones se muestran mediante los valores de clave principal de las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="31529-161">Navigations are shown using the primary key values of the related entities.</span></span> <span data-ttu-id="31529-162">Por ejemplo, `Posts: [{Id: 1}, {Id: 2}]` en la salida anterior indica que la `Blog.Posts` navegación de la colección contiene dos entradas relacionadas con las claves principales 1 y 2, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="31529-162">For example, `Posts: [{Id: 1}, {Id: 2}]` in the output above indicates that the `Blog.Posts` collection navigation contains two related posts with primary keys 1 and 2 respectively.</span></span> <span data-ttu-id="31529-163">Del mismo modo, para cada publicación asociada con el primer blog, la `Blog: {Id: 1}` línea indica que la `Post.Blog` navegación hace referencia al blog con la clave principal 1.</span><span class="sxs-lookup"><span data-stu-id="31529-163">Similarly, for each post associated with the first blog, the `Blog: {Id: 1}` line indicates that the `Post.Blog` navigation references the Blog with primary key 1.</span></span>

### <a name="fixup-to-locally-tracked-entities"></a><span data-ttu-id="31529-164">Corrección para entidades de las que se realiza un seguimiento local</span><span class="sxs-lookup"><span data-stu-id="31529-164">Fixup to locally tracked entities</span></span>

<span data-ttu-id="31529-165">La corrección de la relación también se produce entre las entidades devueltas desde una consulta de seguimiento y las entidades a las que el DbContext ya realiza un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="31529-165">Relationship fixup also happens between entities returned from a tracking query and entities already tracked by the DbContext.</span></span> <span data-ttu-id="31529-166">Por ejemplo, considere la posibilidad de ejecutar tres consultas independientes para blogs, publicaciones y recursos:</span><span class="sxs-lookup"><span data-stu-id="31529-166">For example, consider executing three separate queries for blogs, posts, and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
<span data-ttu-id="31529-167">[! code-CSharp[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ] Al mirar de nuevo las vistas de depuración, después de la primera consulta, solo se realiza el seguimiento de los dos blogs:</span><span class="sxs-lookup"><span data-stu-id="31529-167">[!code-csharp[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ] Looking again at the debug views, after the first query only the two blogs are tracked:</span></span>

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

<span data-ttu-id="31529-168">Las `Blog.Assets` navegaciones de referencia son NULL y las `Blog.Posts` navegaciones de la colección están vacías porque el contexto no está realizando actualmente un seguimiento de entidades asociadas.</span><span class="sxs-lookup"><span data-stu-id="31529-168">The `Blog.Assets` reference navigations are null, and the `Blog.Posts` collection navigations are empty because no associated entities are currently being tracked by the context.</span></span>

<span data-ttu-id="31529-169">Después de la segunda consulta, `Blogs.Assets` se han corregido las navegaciones de referencia para que apunten a las instancias recién controladas `BlogAsset` .</span><span class="sxs-lookup"><span data-stu-id="31529-169">After the second query, the `Blogs.Assets` reference navigations have been fixed up to point to the newly tracked `BlogAsset` instances.</span></span> <span data-ttu-id="31529-170">Del mismo modo, las `BlogAssets.Blog` navegaciones de referencia se establecen para que apunten a la instancia de ya sometida a seguimiento adecuada `Blog` .</span><span class="sxs-lookup"><span data-stu-id="31529-170">Likewise, the `BlogAssets.Blog` reference navigations are set to point to the appropriate already tracked `Blog` instance.</span></span>

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

<span data-ttu-id="31529-171">Por último, después de la tercera consulta, las `Blog.Posts` navegaciones de la colección contienen ahora todos los envíos relacionados y las `Post.Blog` referencias apuntan a la `Blog` instancia adecuada:</span><span class="sxs-lookup"><span data-stu-id="31529-171">Finally, after the third query, the `Blog.Posts` collection navigations now contain all related posts, and the `Post.Blog` references point to the appropriate `Blog` instance:</span></span>

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

<span data-ttu-id="31529-172">Este es el mismo estado final que se ha logrado con la consulta única original, ya que EF Core las navegaciones fijas a medida que se realiza el seguimiento de las entidades, incluso cuando proceden de varias consultas diferentes.</span><span class="sxs-lookup"><span data-stu-id="31529-172">This is the same end-state as was achieved with the original single query, since EF Core fixed up navigations as entities were tracked, even when coming from multiple different queries.</span></span>

> [!NOTE]
> <span data-ttu-id="31529-173">La corrección nunca hace que se devuelvan más datos de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="31529-173">Fixup never causes more data to be returned from the database.</span></span> <span data-ttu-id="31529-174">Solo se conectan las entidades que ya devuelve la consulta o a las que el DbContext ya realiza un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="31529-174">It only connects entities that are already returned by the query or already tracked by the DbContext.</span></span> <span data-ttu-id="31529-175">Vea [resolución de identidades en EF Core](xref:core/change-tracking/identity-resolution) para obtener información sobre cómo administrar duplicados al serializar entidades.</span><span class="sxs-lookup"><span data-stu-id="31529-175">See [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution) for information about handling duplicates when serializing entities.</span></span>

## <a name="changing-relationships-using-navigations"></a><span data-ttu-id="31529-176">Cambiar relaciones mediante navegación</span><span class="sxs-lookup"><span data-stu-id="31529-176">Changing relationships using navigations</span></span>

<span data-ttu-id="31529-177">La forma más fácil de cambiar la relación entre dos entidades es mediante la manipulación de una navegación, a la vez que se mantiene EF Core para corregir la navegación inversa y los valores de FK adecuadamente.</span><span class="sxs-lookup"><span data-stu-id="31529-177">The easiest way to change the relationship between two entities is by manipulating a navigation, while leaving EF Core to fixup the inverse navigation and FK values appropriately.</span></span> <span data-ttu-id="31529-178">Esto se puede hacer de la forma siguiente:</span><span class="sxs-lookup"><span data-stu-id="31529-178">This can be done by:</span></span>

- <span data-ttu-id="31529-179">Adición o eliminación de una entidad de una navegación de colección.</span><span class="sxs-lookup"><span data-stu-id="31529-179">Adding or removing an entity from a collection navigation.</span></span>
- <span data-ttu-id="31529-180">Cambiar una navegación de referencia para apuntar a otra entidad o establecerla en NULL.</span><span class="sxs-lookup"><span data-stu-id="31529-180">Changing a reference navigation to point to a different entity, or setting it to null.</span></span>

### <a name="adding-or-removing-from-collection-navigations"></a><span data-ttu-id="31529-181">Agregar o quitar de las navegaciones de la colección</span><span class="sxs-lookup"><span data-stu-id="31529-181">Adding or removing from collection navigations</span></span>

<span data-ttu-id="31529-182">Por ejemplo, vamos a migrar una de las entradas del blog de Visual Studio al blog de .NET.</span><span class="sxs-lookup"><span data-stu-id="31529-182">For example, let's move one of the posts from the Visual Studio blog to the .NET blog.</span></span> <span data-ttu-id="31529-183">Esto requiere cargar primero los blogs y entradas y, a continuación, mover la entrada desde la colección de navegación de un blog a la colección de navegación del otro blog:</span><span class="sxs-lookup"><span data-stu-id="31529-183">This requires first loading the blogs and posts, and then moving the post from the navigation collection on one blog to the navigation collection on the other blog:</span></span>

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
> <span data-ttu-id="31529-184"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>Aquí se necesita una llamada a porque el acceso a la vista de depuración no provoca la [detección automática de los cambios](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="31529-184">A call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is needed here because accessing the debug view does not cause [automatic detection of changes](xref:core/change-tracking/change-detection).</span></span>

<span data-ttu-id="31529-185">Esta es la vista de depuración impresa después de ejecutar el código anterior:</span><span class="sxs-lookup"><span data-stu-id="31529-185">This is the debug view printed after running the code above:</span></span>

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

<span data-ttu-id="31529-186">La `Blog.Posts` navegación en el blog de .net tiene ahora tres publicaciones ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ).</span><span class="sxs-lookup"><span data-stu-id="31529-186">The `Blog.Posts` navigation on the .NET Blog now has three posts (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`).</span></span> <span data-ttu-id="31529-187">Del mismo modo, la `Blog.Posts` navegación en el blog de Visual Studio solo tiene una publicación ( `Posts: [{Id: 4}]` ).</span><span class="sxs-lookup"><span data-stu-id="31529-187">Likewise, the `Blog.Posts` navigation on the Visual Studio blog only has one post (`Posts: [{Id: 4}]`).</span></span> <span data-ttu-id="31529-188">Esto se debe esperar, ya que el código cambió explícitamente estas colecciones.</span><span class="sxs-lookup"><span data-stu-id="31529-188">This is to be expected since the code explicitly changed these collections.</span></span>

<span data-ttu-id="31529-189">Más Curiosamente, aunque el código no cambiara explícitamente la `Post.Blog` navegación, se ha corregido para que apunte al blog de Visual Studio ( `Blog: {Id: 1}` ).</span><span class="sxs-lookup"><span data-stu-id="31529-189">More interestingly, even though the code did not explicitly change the `Post.Blog` navigation, it has been fixed-up to point to the Visual Studio blog (`Blog: {Id: 1}`).</span></span> <span data-ttu-id="31529-190">Además, el `Post.BlogId` valor de clave externa se ha actualizado para que coincida con el valor de clave principal del blog de .net.</span><span class="sxs-lookup"><span data-stu-id="31529-190">Also, the `Post.BlogId` foreign key value has been updated to match the primary key value of the .NET blog.</span></span> <span data-ttu-id="31529-191">Este cambio en el valor de FK en se conserva en la base de datos cuando se llama a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="31529-191">This change to the FK value in then persisted to the database when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a><span data-ttu-id="31529-192">Cambiar las navegaciones de referencia</span><span class="sxs-lookup"><span data-stu-id="31529-192">Changing reference navigations</span></span>

<span data-ttu-id="31529-193">En el ejemplo anterior, una publicación se ha desplazado de un blog a otro manipulando la navegación por la colección de publicaciones en cada blog.</span><span class="sxs-lookup"><span data-stu-id="31529-193">In the previous example, a post was moved from one blog to another by manipulating the collection navigation of posts on each blog.</span></span> <span data-ttu-id="31529-194">Lo mismo se puede lograr si se cambia la navegación de `Post.Blog` referencia para que apunte al nuevo blog.</span><span class="sxs-lookup"><span data-stu-id="31529-194">The same thing can be achieved by instead changing the `Post.Blog` reference navigation to point to the new blog.</span></span> <span data-ttu-id="31529-195">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="31529-195">For example:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

<span data-ttu-id="31529-196">La vista de depuración después de este cambio es _exactamente la misma_ que en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="31529-196">The debug view after this change is _exactly the same_ as it was in the previous example.</span></span> <span data-ttu-id="31529-197">Esto se debe a que EF Core detectó el cambio de navegación de referencia y, a continuación, corrigió las navegaciones de la colección y el valor de FK para que coincidan.</span><span class="sxs-lookup"><span data-stu-id="31529-197">This because EF Core detected the reference navigation change and then fixed up the collection navigations and FK value to match.</span></span>

## <a name="changing-relationships-using-foreign-key-values"></a><span data-ttu-id="31529-198">Cambiar relaciones mediante valores de clave externa</span><span class="sxs-lookup"><span data-stu-id="31529-198">Changing relationships using foreign key values</span></span>

<span data-ttu-id="31529-199">En la sección anterior, las relaciones se manipulaban mediante navegación y los valores de clave externa se actualizaban automáticamente.</span><span class="sxs-lookup"><span data-stu-id="31529-199">In the previous section, relationships were manipulated by navigations leaving foreign key values to be updated automatically.</span></span> <span data-ttu-id="31529-200">Esta es la manera recomendada de manipular relaciones en EF Core.</span><span class="sxs-lookup"><span data-stu-id="31529-200">This is the recommended way to manipulate relationships in EF Core.</span></span> <span data-ttu-id="31529-201">Sin embargo, también es posible manipular los valores de FK directamente.</span><span class="sxs-lookup"><span data-stu-id="31529-201">However, it is also possible to manipulate FK values directly.</span></span> <span data-ttu-id="31529-202">Por ejemplo, podemos trasladar una entrada de un blog a otro cambiando el valor de la `Post.BlogId` clave externa:</span><span class="sxs-lookup"><span data-stu-id="31529-202">For example, we can move a post from one blog to another by changing the `Post.BlogId` foreign key value:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

<span data-ttu-id="31529-203">Observe cómo esto es muy similar al cambio de la navegación de referencia, tal como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="31529-203">Notice how this is very similar to changing the reference navigation, as shown in the previous example.</span></span>

<span data-ttu-id="31529-204">La vista de depuración después de este cambio vuelve a ser _exactamente_ igual que en el caso de los dos ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="31529-204">The debug view after this change is again _exactly the same_ as was the case for the previous two examples.</span></span> <span data-ttu-id="31529-205">Esto se debe a que EF Core detectó el cambio de valor de FK y, a continuación, corrigió las navegaciones de referencia y de colección para que coincidan.</span><span class="sxs-lookup"><span data-stu-id="31529-205">This because EF Core detected the FK value change and then fixed up both the reference and collection navigations to match.</span></span>

> [!TIP]
> <span data-ttu-id="31529-206">No Escriba código para manipular todos los valores de navegación y FK cada vez que cambie una relación.</span><span class="sxs-lookup"><span data-stu-id="31529-206">Do not write code to manipulate all navigations and FK values each time a relationship changes.</span></span> <span data-ttu-id="31529-207">Este código es más complicado y debe garantizar cambios coherentes en las claves externas y navegaciones en todos los casos.</span><span class="sxs-lookup"><span data-stu-id="31529-207">Such code is more complicated and must ensure consistent changes to foreign keys and navigations in every case.</span></span> <span data-ttu-id="31529-208">Si es posible, basta con manipular una sola navegación, o quizás ambas navegaciones.</span><span class="sxs-lookup"><span data-stu-id="31529-208">If possible, just manipulate a single navigation, or maybe both navigations.</span></span> <span data-ttu-id="31529-209">Si es necesario, basta con manipular los valores de FK.</span><span class="sxs-lookup"><span data-stu-id="31529-209">If needed, just manipulate FK values.</span></span> <span data-ttu-id="31529-210">Evite manipular los valores de navegación y de FK.</span><span class="sxs-lookup"><span data-stu-id="31529-210">Avoid manipulating both navigations and FK values.</span></span>

## <a name="fixup-for-added-or-deleted-entities"></a><span data-ttu-id="31529-211">Corrección para entidades agregadas o eliminadas</span><span class="sxs-lookup"><span data-stu-id="31529-211">Fixup for added or deleted entities</span></span>

### <a name="adding-to-a-collection-navigation"></a><span data-ttu-id="31529-212">Agregar a una navegación de colección</span><span class="sxs-lookup"><span data-stu-id="31529-212">Adding to a collection navigation</span></span>

<span data-ttu-id="31529-213">EF Core realiza las siguientes acciones cuando [detecta](xref:core/change-tracking/change-detection) que se ha agregado una nueva entidad dependiente o secundaria a una navegación de colección:</span><span class="sxs-lookup"><span data-stu-id="31529-213">EF Core performs the following actions when it [detects](xref:core/change-tracking/change-detection) that a new dependent/child entity has been added to a collection navigation:</span></span>

- <span data-ttu-id="31529-214">Si no se realiza el seguimiento de la entidad, se realiza el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="31529-214">If the entity is not tracked, then it is tracked.</span></span> <span data-ttu-id="31529-215">(La entidad normalmente estará en el `Added` Estado.</span><span class="sxs-lookup"><span data-stu-id="31529-215">(The entity will usually be in the `Added` state.</span></span> <span data-ttu-id="31529-216">Sin embargo, si el tipo de entidad está configurado para usar claves generadas y se establece el valor de clave principal, se realiza un seguimiento de la entidad en el `Unchanged` Estado.)</span><span class="sxs-lookup"><span data-stu-id="31529-216">However, if the entity type is configured to use generated keys and the primary key value is set, then the entity is tracked in the `Unchanged` state.)</span></span>
- <span data-ttu-id="31529-217">Si la entidad está asociada a otra entidad de seguridad o primaria, se rompe la relación.</span><span class="sxs-lookup"><span data-stu-id="31529-217">If the entity is associated with a different principal/parent, then that relationship is severed.</span></span>
- <span data-ttu-id="31529-218">La entidad se asocia con el principal/primario que posee la navegación de la colección.</span><span class="sxs-lookup"><span data-stu-id="31529-218">The entity becomes associated with the principal/parent that owns the collection navigation.</span></span>
- <span data-ttu-id="31529-219">Las navegaciones y los valores de clave externa se corrigen para todas las entidades implicadas.</span><span class="sxs-lookup"><span data-stu-id="31529-219">Navigations and foreign key values are fixed up for all entities involved.</span></span>

<span data-ttu-id="31529-220">En función de esto, podemos ver que para trasladar una entrada de un blog a otro, realmente no es necesario quitarla de la antigua navegación de la colección antes de agregarla al nuevo.</span><span class="sxs-lookup"><span data-stu-id="31529-220">Based on this we can see that to move a post from one blog to another we don't actually need to remove it from the old collection navigation before adding it to the new.</span></span> <span data-ttu-id="31529-221">Por lo tanto, el código del ejemplo anterior se puede cambiar de:</span><span class="sxs-lookup"><span data-stu-id="31529-221">So the code from the example above can be changed from:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

<span data-ttu-id="31529-222">Por:</span><span class="sxs-lookup"><span data-stu-id="31529-222">To:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

<span data-ttu-id="31529-223">EF Core observa que la publicación se ha agregado a un nuevo blog y la quita automáticamente de la colección en el primer blog.</span><span class="sxs-lookup"><span data-stu-id="31529-223">EF Core sees that the post has been added to a new blog and automatically removes it from the collection on the first blog.</span></span>

### <a name="removing-from-a-collection-navigation"></a><span data-ttu-id="31529-224">Quitar de una navegación de colección</span><span class="sxs-lookup"><span data-stu-id="31529-224">Removing from a collection navigation</span></span>

<span data-ttu-id="31529-225">Al quitar una entidad dependiente o secundaria de la navegación por la colección de la entidad de seguridad o primaria, se produce el control de la relación con esa entidad de seguridad o primaria.</span><span class="sxs-lookup"><span data-stu-id="31529-225">Removing a dependent/child entity from the collection navigation of the principal/parent causes severing of the relationship to that principal/parent.</span></span> <span data-ttu-id="31529-226">Lo que sucede después depende de si la relación es opcional u obligatoria.</span><span class="sxs-lookup"><span data-stu-id="31529-226">What happens next depends on whether the relationship is optional or required.</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="31529-227">Relaciones opcionales</span><span class="sxs-lookup"><span data-stu-id="31529-227">Optional relationships</span></span>

<span data-ttu-id="31529-228">De forma predeterminada, para las relaciones opcionales, el valor de clave externa se establece en NULL.</span><span class="sxs-lookup"><span data-stu-id="31529-228">By default for optional relationships, the foreign key value is set to null.</span></span> <span data-ttu-id="31529-229">Esto significa que el dependiente o el elemento secundario ya no está asociado con _ninguna_ entidad de seguridad o elemento primario.</span><span class="sxs-lookup"><span data-stu-id="31529-229">This means that the dependent/child is no longer associated with _any_ principal/parent.</span></span> <span data-ttu-id="31529-230">Por ejemplo, vamos a cargar un blog y a publicar y, después, quitar una de las entradas de la navegación por la `Blog.Posts` colección:</span><span class="sxs-lookup"><span data-stu-id="31529-230">For example, let's load a blog and posts and then remove one of the posts from the `Blog.Posts` collection navigation:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

<span data-ttu-id="31529-231">Al examinar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de este cambio, se muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31529-231">Looking at the [change tracking debug view](xref:core/change-tracking/debug-views) after this change shows that:</span></span>

- <span data-ttu-id="31529-232">El valor de FK se ha `Post.BlogId` establecido en null ( `BlogId: <null> FK Modified Originally 1` )</span><span class="sxs-lookup"><span data-stu-id="31529-232">The `Post.BlogId` FK has been set to null (`BlogId: <null> FK Modified Originally 1`)</span></span>
- <span data-ttu-id="31529-233">La `Post.Blog` navegación de referencia se ha establecido en null ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="31529-233">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`)</span></span>
- <span data-ttu-id="31529-234">La publicación se ha quitado de la `Blog.Posts` navegación de colección ( `Posts: [{Id: 1}]` )</span><span class="sxs-lookup"><span data-stu-id="31529-234">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`)</span></span>

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

<span data-ttu-id="31529-235">Observe que la publicación _no_ está marcada como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="31529-235">Notice that the post is _not_ marked as `Deleted`.</span></span> <span data-ttu-id="31529-236">Está marcado como `Modified` para que el valor de FK de la base de datos se establezca en NULL cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="31529-236">It is marked as `Modified` so that the FK value in the database will be set to null when SaveChanges is called.</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="31529-237">Relaciones obligatorias</span><span class="sxs-lookup"><span data-stu-id="31529-237">Required relationships</span></span>

<span data-ttu-id="31529-238">No se permite establecer el valor de FK en null (y normalmente no es posible) para las relaciones necesarias.</span><span class="sxs-lookup"><span data-stu-id="31529-238">Setting the FK value to null is not allowed (and is usually not possible) for required relationships.</span></span> <span data-ttu-id="31529-239">Por lo tanto, el servidor de una relación necesaria significa que la entidad dependiente o secundaria debe volver a ser primaria para una nueva entidad de seguridad principal o quitarse de la base de datos cuando se llama a SaveChanges para evitar una infracción de la restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="31529-239">Therefore, severing a required relationship means that the dependent/child entity must be either re-parented to a new principal/parent, or removed from the database when SaveChanges is called to avoid a referential constraint violation.</span></span> <span data-ttu-id="31529-240">Esto se conoce como "eliminación de huérfanos" y es el comportamiento predeterminado en EF Core para las relaciones necesarias.</span><span class="sxs-lookup"><span data-stu-id="31529-240">This is known as "deleting orphans", and is the default behavior in EF Core for required relationships.</span></span>

<span data-ttu-id="31529-241">Por ejemplo, vamos a cambiar la relación entre blog y publicaciones para que sea necesaria y, a continuación, ejecutar el mismo código que en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="31529-241">For example, let's change the relationship between blog and posts to be required and then run the same code as in the previous example:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

<span data-ttu-id="31529-242">Al examinar la vista de depuración después de este cambio, se muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31529-242">Looking at the debug view after this change shows that:</span></span>

- <span data-ttu-id="31529-243">La publicación se ha marcado como tal `Deleted` que se eliminará de la base de datos cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="31529-243">The post has been marked as `Deleted` such that it will be deleted from the database when SaveChanges is called.</span></span>
- <span data-ttu-id="31529-244">La `Post.Blog` navegación de referencia se ha establecido en null ( `Blog: <null>` ).</span><span class="sxs-lookup"><span data-stu-id="31529-244">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`).</span></span>
- <span data-ttu-id="31529-245">La publicación se ha quitado de la `Blog.Posts` navegación de colección ( `Posts: [{Id: 1}]` ).</span><span class="sxs-lookup"><span data-stu-id="31529-245">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`).</span></span>

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

<span data-ttu-id="31529-246">Tenga en cuenta que el `Post.BlogId` permanece sin cambios desde que para una relación requerida no se puede establecer en NULL.</span><span class="sxs-lookup"><span data-stu-id="31529-246">Notice that the `Post.BlogId` remains unchanged since for a required relationship it cannot be set to null.</span></span>

<span data-ttu-id="31529-247">La llamada a SaveChanges da como resultado la eliminación de la publicación huérfana:</span><span class="sxs-lookup"><span data-stu-id="31529-247">Calling SaveChanges results in the orphaned post being deleted:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a><span data-ttu-id="31529-248">Eliminar el control de tiempo y volver a crear un elemento huérfano</span><span class="sxs-lookup"><span data-stu-id="31529-248">Delete orphans timing and re-parenting</span></span>

<span data-ttu-id="31529-249">De forma predeterminada, marcar huérfanos como `Deleted` sucede en cuanto se [detecta](xref:core/change-tracking/change-detection)el cambio de relación.</span><span class="sxs-lookup"><span data-stu-id="31529-249">By default, marking orphans as `Deleted` happens as soon as the relationship change is [detected](xref:core/change-tracking/change-detection).</span></span> <span data-ttu-id="31529-250">Sin embargo, este proceso se puede retrasar hasta que se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="31529-250">However, this process can be delayed until SaveChanges is actually called.</span></span> <span data-ttu-id="31529-251">Esto puede ser útil para evitar la creación de huérfanos de entidades que se han quitado de una entidad de seguridad o un elemento primario, pero que se volverán a crear con una nueva entidad de seguridad o primaria antes de que se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="31529-251">This can be useful to avoid making orphans of entities that have been removed from one principal/parent, but will be re-parented with a new principal/parent before SaveChanges is called.</span></span> <span data-ttu-id="31529-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> se usa para establecer este tiempo.</span><span class="sxs-lookup"><span data-stu-id="31529-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> is used to set this timing.</span></span> <span data-ttu-id="31529-253">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="31529-253">For example:</span></span>

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

<span data-ttu-id="31529-254">Después de quitar la entrada de la primera colección, el objeto no está marcado como `Deleted` en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="31529-254">After removing the post from the first collection the object is not marked as `Deleted` as it was in the previous example.</span></span> <span data-ttu-id="31529-255">En su lugar, EF Core está realizando un seguimiento de que la relación se rompe, _aunque se trata de una relación requerida_.</span><span class="sxs-lookup"><span data-stu-id="31529-255">Instead, EF Core is tracking that the relationship is severed _even though this is a required relationship_.</span></span> <span data-ttu-id="31529-256">(El valor de FK se considera null en EF Core aunque no sea realmente nulo porque el tipo no admite valores NULL.</span><span class="sxs-lookup"><span data-stu-id="31529-256">(The FK value is considered null by EF Core even though it cannot really be null because the type is not nullable.</span></span> <span data-ttu-id="31529-257">Esto se conoce como "null conceptual").</span><span class="sxs-lookup"><span data-stu-id="31529-257">This is known as a "conceptual null".)</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="31529-258">La llamada a SaveChanges en este momento daría lugar a la eliminación de la publicación huérfana.</span><span class="sxs-lookup"><span data-stu-id="31529-258">Calling SaveChanges at this time would result in the orphaned post being deleted.</span></span> <span data-ttu-id="31529-259">Sin embargo, si, como en el ejemplo anterior, post está asociado a un nuevo blog antes de que se llame a SaveChanges, se corregirá correctamente en ese nuevo blog y ya no se considerará huérfano:</span><span class="sxs-lookup"><span data-stu-id="31529-259">However, if as in the example above, post is associated with a new blog before SaveChanges is called, then it will be fixed up appropriately to that new blog and is no longer considered an orphan:</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

<span data-ttu-id="31529-260">El SaveChanges llamado en este punto actualizará la publicación en la base de datos en lugar de eliminarla.</span><span class="sxs-lookup"><span data-stu-id="31529-260">SaveChanges called at this point will update the post in the database rather than deleting it.</span></span>

<span data-ttu-id="31529-261">También es posible desactivar la eliminación automática de huérfanos.</span><span class="sxs-lookup"><span data-stu-id="31529-261">It is also possible to turn off automatic deletion of orphans.</span></span> <span data-ttu-id="31529-262">Esto producirá una excepción si se llama a SaveChanges mientras se realiza el seguimiento de un huérfano.</span><span class="sxs-lookup"><span data-stu-id="31529-262">This will result in an exception if SaveChanges is called while an orphan is being tracked.</span></span> <span data-ttu-id="31529-263">Por ejemplo, este código:</span><span class="sxs-lookup"><span data-stu-id="31529-263">For example, this code:</span></span>

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

<span data-ttu-id="31529-264">Producirá esta excepción:</span><span class="sxs-lookup"><span data-stu-id="31529-264">Will throw this exception:</span></span>

> <span data-ttu-id="31529-265">System. InvalidOperationException: se ha interrumpido la asociación entre las entidades ' blog ' y ' post ' con el valor de clave ' {BlogId: 1} ', pero la relación está marcada como requerida o es implícitamente necesaria porque la clave externa no admite valores NULL.</span><span class="sxs-lookup"><span data-stu-id="31529-265">System.InvalidOperationException: The association between entities 'Blog' and 'Post' with the key value '{BlogId: 1}' has been severed, but the relationship is either marked as required or is implicitly required because the foreign key is not nullable.</span></span> <span data-ttu-id="31529-266">Si se debe eliminar la entidad dependiente o secundaria cuando se interrumpe una relación requerida, configure la relación para utilizar las eliminaciones en cascada.</span><span class="sxs-lookup"><span data-stu-id="31529-266">If the dependent/child entity should be deleted when a required relationship is severed, configure the relationship to use cascade deletes.</span></span>

<span data-ttu-id="31529-267">La eliminación de huérfanos, así como las eliminaciones en cascada, se puede forzar en cualquier momento llamando a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="31529-267">Deletion of orphans, as well as cascade deletes, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="31529-268">La combinación de esto con el establecimiento del tiempo de eliminación de huérfanos en `Never` garantizará que los huérfanos nunca se eliminan a menos que se indique explícitamente EF Core.</span><span class="sxs-lookup"><span data-stu-id="31529-268">Combining this with setting the delete orphan timing to `Never` will ensure orphans are never deleted unless EF Core is explicitly instructed to do so.</span></span>

### <a name="changing-a-reference-navigation"></a><span data-ttu-id="31529-269">Cambiar una navegación de referencia</span><span class="sxs-lookup"><span data-stu-id="31529-269">Changing a reference navigation</span></span>

<span data-ttu-id="31529-270">Cambiar la navegación de referencia de una relación de uno a varios tiene el mismo efecto que cambiar la navegación por la colección en el otro extremo de la relación.</span><span class="sxs-lookup"><span data-stu-id="31529-270">Changing the reference navigation of a one-to-many relationship has the same effect as changing the collection navigation on the other end of the relationship.</span></span> <span data-ttu-id="31529-271">Establecer la navegación de referencia de dependent/Child a NULL equivale a quitar la entidad de la navegación de la colección de la entidad de seguridad principal.</span><span class="sxs-lookup"><span data-stu-id="31529-271">Setting the reference navigation of dependent/child to null is equivalent to removing the entity from the collection navigation of the principal/parent.</span></span> <span data-ttu-id="31529-272">Todos los cambios de corrección y base de datos se realizan como se describe en la sección anterior, lo que incluye convertir la entidad en huérfana si se requiere la relación.</span><span class="sxs-lookup"><span data-stu-id="31529-272">All fixup and database changes happen as described in the previous section, including making the entity an orphan if the relationship is required.</span></span>

#### <a name="optional-one-to-one-relationships"></a><span data-ttu-id="31529-273">Relaciones de uno a uno opcionales</span><span class="sxs-lookup"><span data-stu-id="31529-273">Optional one-to-one relationships</span></span>

<span data-ttu-id="31529-274">En el caso de las relaciones uno a uno, el cambio de una navegación de referencia hace que cualquier relación anterior se haga más grave.</span><span class="sxs-lookup"><span data-stu-id="31529-274">For one-to-one relationships, changing a reference navigation causes any previous relationship to be severed.</span></span> <span data-ttu-id="31529-275">En el caso de las relaciones opcionales, esto significa que el valor de FK del dependiente o elemento secundario relacionado anteriormente se establece en NULL.</span><span class="sxs-lookup"><span data-stu-id="31529-275">For optional relationships, this means that the FK value on the previously related dependent/child is set to null.</span></span> <span data-ttu-id="31529-276">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="31529-276">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

<span data-ttu-id="31529-277">La vista de depuración antes de llamar a SaveChanges muestra que los recursos nuevos han reemplazado a los activos existentes, que ahora está marcado como `Modified` con un `BlogAssets.BlogId` valor de FK nulo:</span><span class="sxs-lookup"><span data-stu-id="31529-277">The debug view before calling SaveChanges shows that the new assets has replaced the existing assets, which is now marked as `Modified` with a null `BlogAssets.BlogId` FK value:</span></span>

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

<span data-ttu-id="31529-278">Esto da como resultado una actualización e inserción cuando se llama a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="31529-278">This results in an update and an insert when SaveChanges is called:</span></span>

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

#### <a name="required-one-to-one-relationships"></a><span data-ttu-id="31529-279">Relaciones uno a uno necesarios</span><span class="sxs-lookup"><span data-stu-id="31529-279">Required one-to-one relationships</span></span>

<span data-ttu-id="31529-280">Si se ejecuta el mismo código que en el ejemplo anterior, pero esta vez con una relación uno a uno necesaria, se muestra que el objeto asociado anteriormente `BlogAssets` ahora está marcado como `Deleted` , ya que se convierte en huérfano cuando el nuevo `BlogAssets` ocupa su lugar:</span><span class="sxs-lookup"><span data-stu-id="31529-280">Running the same code as in the previous example, but this time with a required one-to-one relationship, shows that the previously associated `BlogAssets` is now marked as `Deleted`, since it becomes an orphan when the new `BlogAssets` takes its place:</span></span>

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

<span data-ttu-id="31529-281">Esto da como resultado una eliminación de e Insert cuando se llama a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="31529-281">This then results in an delete an and insert when SaveChanges is called:</span></span>

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

<span data-ttu-id="31529-282">La temporización de marcar huérfanos como eliminados se puede cambiar de la misma manera que se muestra para las navegaciones de la colección y tiene los mismos efectos.</span><span class="sxs-lookup"><span data-stu-id="31529-282">The timing of marking orphans as deleted can be changed in the same way as shown for collection navigations and has the same effects.</span></span>

### <a name="deleting-an-entity"></a><span data-ttu-id="31529-283">Eliminar una entidad</span><span class="sxs-lookup"><span data-stu-id="31529-283">Deleting an entity</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="31529-284">Relaciones opcionales</span><span class="sxs-lookup"><span data-stu-id="31529-284">Optional relationships</span></span>

<span data-ttu-id="31529-285">Cuando una entidad se marca como `Deleted` , por ejemplo llamando a <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , se quitan las referencias a la entidad eliminada de las navegaciones de otras entidades.</span><span class="sxs-lookup"><span data-stu-id="31529-285">When an entity is marked as `Deleted`, for example by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, then references to the deleted entity are removed from the navigations of other entities.</span></span> <span data-ttu-id="31529-286">En el caso de las relaciones opcionales, los valores de FK de entidades dependientes se establecen en NULL.</span><span class="sxs-lookup"><span data-stu-id="31529-286">For optional relationships, the FK values in dependent entities are set to null.</span></span>

<span data-ttu-id="31529-287">Por ejemplo, vamos a marcar el blog de Visual Studio como `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="31529-287">For example, let's mark the Visual Studio blog as `Deleted`:</span></span>

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

<span data-ttu-id="31529-288">Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) antes de llamar a SaveChanges, se muestra:</span><span class="sxs-lookup"><span data-stu-id="31529-288">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows:</span></span>

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

<span data-ttu-id="31529-289">Tenga en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31529-289">Notice that:</span></span>

- <span data-ttu-id="31529-290">El blog se marca como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="31529-290">The blog is marked as `Deleted`.</span></span>
- <span data-ttu-id="31529-291">Los recursos relacionados con el blog eliminado tienen un valor de FK null ( `BlogId: <null> FK Modified Originally 2` ) y una navegación de referencia nula ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="31529-291">The assets related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>
- <span data-ttu-id="31529-292">Cada publicación relacionada con el blog eliminado tiene un valor de FK null ( `BlogId: <null> FK Modified Originally 2` ) y una navegación de referencia nula ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="31529-292">Each post related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="31529-293">Relaciones obligatorias</span><span class="sxs-lookup"><span data-stu-id="31529-293">Required relationships</span></span>

<span data-ttu-id="31529-294">El comportamiento de corrección para las relaciones necesarias es el mismo que para las relaciones opcionales, salvo que las entidades dependientes o secundarias se marcan como `Deleted` porque no pueden existir sin una entidad de seguridad o un elemento primario y se deben quitar de la base de datos cuando se llama a SaveChanges para evitar una excepción de restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="31529-294">The fixup behavior for required relationships is the same as for optional relationships except that the dependent/child entities are marked as `Deleted` since they cannot exist without a principal/parent and must be removed from the database when SaveChanges is called to avoid a referential constraint exception.</span></span> <span data-ttu-id="31529-295">Esto se conoce como "eliminación en cascada" y es el comportamiento predeterminado en EF Core para las relaciones necesarias.</span><span class="sxs-lookup"><span data-stu-id="31529-295">This is known as "cascade delete", and is the default behavior in EF Core for required relationships.</span></span> <span data-ttu-id="31529-296">Por ejemplo, si se ejecuta el mismo código que en el ejemplo anterior, pero con una relación requerida, se obtiene la siguiente vista de depuración antes de que se llame a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="31529-296">For example, running the same code as in the previous example but with a required relationship results in the following debug view before SaveChanges is called:</span></span>

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

<span data-ttu-id="31529-297">Como se esperaba, los elementos secundarios o dependientes se marcan ahora como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="31529-297">As expected, the dependents/children are now marked as `Deleted`.</span></span> <span data-ttu-id="31529-298">Sin embargo, tenga en cuenta que las navegaciones en las entidades eliminadas _no_ han cambiado.</span><span class="sxs-lookup"><span data-stu-id="31529-298">However, notice that the navigations on the deleted entities have _not_ changed.</span></span> <span data-ttu-id="31529-299">Esto puede parecer extraño, pero evita la fragmentación completa de un gráfico de entidades eliminado borrando todas las navegaciones.</span><span class="sxs-lookup"><span data-stu-id="31529-299">This may seem strange, but it avoids completely shredding a deleted graph of entities by clearing all navigations.</span></span> <span data-ttu-id="31529-300">Es decir, el blog, el recurso y las entradas siguen conformando un gráfico de entidades incluso después de haberse eliminado.</span><span class="sxs-lookup"><span data-stu-id="31529-300">That is, the blog, asset, and posts still form a graph of entities even after having been deleted.</span></span> <span data-ttu-id="31529-301">Esto hace que sea mucho más fácil anular la eliminación de un gráfico de entidades que en el caso de EF6 en el que se ha purgado el gráfico.</span><span class="sxs-lookup"><span data-stu-id="31529-301">This makes it much easier to un-delete a graph of entities than was the case in EF6 where the graph was shredded.</span></span>

#### <a name="cascade-delete-timing-and-re-parenting"></a><span data-ttu-id="31529-302">Temporización de eliminación en cascada y Realquiler</span><span class="sxs-lookup"><span data-stu-id="31529-302">Cascade delete timing and re-parenting</span></span>

<span data-ttu-id="31529-303">De forma predeterminada, la eliminación en cascada se produce tan pronto como la entidad de seguridad principal se marca como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="31529-303">By default, cascade delete happens as soon as the parent/principal is marked as `Deleted`.</span></span> <span data-ttu-id="31529-304">Esto es lo mismo que para eliminar huérfanos, como se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="31529-304">This is the same as for deleting orphans, as described previously.</span></span> <span data-ttu-id="31529-305">Al igual que con la eliminación de huérfanos, este proceso se puede retrasar hasta que se llame a SaveChanges, o incluso deshabilitado por completo, estableciendo la configuración <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> apropiada.</span><span class="sxs-lookup"><span data-stu-id="31529-305">As with deleting orphans, this process can be delayed until SaveChanges is called, or even disabled entirely, by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> appropriately.</span></span> <span data-ttu-id="31529-306">Esto resulta útil de la misma manera que para eliminar huérfanos, lo que incluye la reutilización de los elementos secundarios o dependientes de la eliminación de una entidad de seguridad principal.</span><span class="sxs-lookup"><span data-stu-id="31529-306">This is useful in the same way as it is for deleting orphans, including for re-parenting children/dependents after deletion of a principal/parent.</span></span>

<span data-ttu-id="31529-307">Las eliminaciones en cascada, así como la eliminación de huérfanos, se pueden forzar en cualquier momento llamando a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="31529-307">Cascade deletes, as well as deleting orphans, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="31529-308">Al combinarlo con el establecimiento del control de tiempo de eliminación en cascada en `Never` se garantizará que las eliminaciones en cascada no se produzcan a menos que se indique explícitamente EF Core.</span><span class="sxs-lookup"><span data-stu-id="31529-308">Combining this with setting the cascade delete timing to `Never` will ensure cascade deletes never happen unless EF Core is explicitly instructed to do so.</span></span>

> [!TIP]
> <span data-ttu-id="31529-309">La eliminación en cascada y la eliminación de huérfanos están estrechamente relacionadas.</span><span class="sxs-lookup"><span data-stu-id="31529-309">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="31529-310">Ambos dan como resultado la eliminación de entidades dependientes o secundarias cuando se interrumpe la relación con su entidad de seguridad o principal requerida.</span><span class="sxs-lookup"><span data-stu-id="31529-310">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="31529-311">En el caso de la eliminación en cascada, este servidor tiene lugar porque el principal o el primario se eliminan.</span><span class="sxs-lookup"><span data-stu-id="31529-311">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="31529-312">En el caso de los huérfanos, la entidad principal/primaria sigue existiendo, pero ya no está relacionada con las entidades dependientes o secundarias.</span><span class="sxs-lookup"><span data-stu-id="31529-312">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>

## <a name="many-to-many-relationships"></a><span data-ttu-id="31529-313">Relaciones de varios a varios</span><span class="sxs-lookup"><span data-stu-id="31529-313">Many-to-many relationships</span></span>

<span data-ttu-id="31529-314">Las relaciones de varios a varios en EF Core se implementan mediante una entidad join.</span><span class="sxs-lookup"><span data-stu-id="31529-314">Many-to-many relationships in EF Core are implemented using a join entity.</span></span> <span data-ttu-id="31529-315">Cada lado, la relación de varios a varios está relacionada con esta entidad de combinación con una relación de uno a varios.</span><span class="sxs-lookup"><span data-stu-id="31529-315">Each side the many-to-many relationship is related to this join entity with a one-to-many relationship.</span></span> <span data-ttu-id="31529-316">Antes de EF Core 5,0, esta entidad join tenía que definirse y asignarse explícitamente.</span><span class="sxs-lookup"><span data-stu-id="31529-316">Before EF Core 5.0, this join entity had to explicitly defined and mapped.</span></span> <span data-ttu-id="31529-317">A partir de EF Core 5,0, se puede crear de forma implícita y oculta.</span><span class="sxs-lookup"><span data-stu-id="31529-317">Starting with EF Core 5.0, it can be created implicitly and hidden.</span></span> <span data-ttu-id="31529-318">Sin embargo, en ambos casos, el comportamiento subyacente es el mismo.</span><span class="sxs-lookup"><span data-stu-id="31529-318">However, in both cases the underlying behavior is the same.</span></span> <span data-ttu-id="31529-319">En primer lugar, veremos este comportamiento subyacente para comprender cómo funciona el seguimiento de relaciones de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="31529-319">We will look at this underlying behavior first to understand how tracking of many-to-many relationships works.</span></span>

### <a name="how-many-to-many-relationships-work"></a><span data-ttu-id="31529-320">Cómo funcionan las relaciones varios a varios</span><span class="sxs-lookup"><span data-stu-id="31529-320">How many-to-many relationships work</span></span>

<span data-ttu-id="31529-321">Tenga en cuenta este modelo de EF Core que crea una relación de varios a varios entre entradas y etiquetas mediante un tipo de entidad de combinación definido explícitamente:</span><span class="sxs-lookup"><span data-stu-id="31529-321">Consider this EF Core model that creates a many-to-many relationship between posts and tags using an explicitly defined join entity type:</span></span>

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

<span data-ttu-id="31529-322">Observe que el `PostTag` tipo de entidad de combinación contiene dos propiedades de clave externa.</span><span class="sxs-lookup"><span data-stu-id="31529-322">Notice that the `PostTag` join entity type contains two foreign key properties.</span></span> <span data-ttu-id="31529-323">En este modelo, para que una publicación esté relacionada con una etiqueta, debe haber una entidad de combinación PostTag en la que el `PostTag.PostId` valor de clave externa coincida con el `Post.Id` valor de clave principal y donde el `PostTag.TagId` valor de clave externa coincida con el `Tag.Id` valor de clave principal.</span><span class="sxs-lookup"><span data-stu-id="31529-323">In this model, for a post to be related to a tag, there must be a PostTag join entity where the `PostTag.PostId` foreign key value matches the `Post.Id` primary key value, and where the `PostTag.TagId` foreign key value matches the `Tag.Id` primary key value.</span></span> <span data-ttu-id="31529-324">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="31529-324">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

<span data-ttu-id="31529-325">Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) después de ejecutar este código, se muestra que la entrada y la etiqueta están relacionadas con la nueva `PostTag` entidad de combinación:</span><span class="sxs-lookup"><span data-stu-id="31529-325">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after running this code shows that the post and tag are related by the new `PostTag` join entity:</span></span>

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

<span data-ttu-id="31529-326">Observe que las navegaciones de la colección en `Post` y `Tag` se han corregido, al igual que las navegaciones de referencia en `PostTag` .</span><span class="sxs-lookup"><span data-stu-id="31529-326">Notice that the collection navigations on `Post` and `Tag` have been fixed up, as have the reference navigations on `PostTag`.</span></span> <span data-ttu-id="31529-327">Estas relaciones se pueden manipular mediante navegaciones en lugar de valores de FK, al igual que en todos los ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="31529-327">These relationships can be manipulated by navigations instead of FK values, just as in all the preceding examples.</span></span> <span data-ttu-id="31529-328">Por ejemplo, el código anterior se puede modificar para agregar la relación estableciendo las navegaciones de referencia en la entidad de combinación:</span><span class="sxs-lookup"><span data-stu-id="31529-328">For example, the code above can be modified to add the relationship by setting the reference navigations on the join entity:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

<span data-ttu-id="31529-329">Esto produce exactamente el mismo cambio en claves externas y en las navegaciones que en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="31529-329">This results in exactly the same change to FKs and navigations as in the previous example.</span></span>

### <a name="skip-navigations"></a><span data-ttu-id="31529-330">Omitir navegaciones</span><span class="sxs-lookup"><span data-stu-id="31529-330">Skip navigations</span></span>

> [!NOTE]
> <span data-ttu-id="31529-331">Omitir las navegaciones se introdujeron en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="31529-331">Skip navigations were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="31529-332">Manipular la tabla de combinación manualmente puede resultar complicado.</span><span class="sxs-lookup"><span data-stu-id="31529-332">Manipulating the join table manually can be cumbersome.</span></span> <span data-ttu-id="31529-333">A partir de EF Core 5,0, las relaciones varios a varios se pueden manipular directamente mediante las navegaciones de colección especiales que "omiten" la entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="31529-333">Starting with EF Core 5.0, many-to-many relationships can be manipulated directly using special collection navigations that "skip over" the join entity.</span></span> <span data-ttu-id="31529-334">Por ejemplo, se pueden agregar dos navegaciones de omisión al modelo anterior; uno de las etiquetas post a y el otro de la etiqueta a posts:</span><span class="sxs-lookup"><span data-stu-id="31529-334">For example, two skip navigations can be added to the model above; one from Post to Tags, and the other from Tag to Posts:</span></span>

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

<span data-ttu-id="31529-335">Esta relación de varios a varios requiere la siguiente configuración para garantizar que las navegaciones de omisión y las navegaciones normales se usan para la misma relación de varios a varios:</span><span class="sxs-lookup"><span data-stu-id="31529-335">This many-to-many relationship requires the following configuration to ensure the skip navigations and normal navigations are all used for the same many-to-many relationship:</span></span>

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

<span data-ttu-id="31529-336">Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre la asignación de relaciones varios a varios.</span><span class="sxs-lookup"><span data-stu-id="31529-336">See [Relationships](xref:core/modeling/relationships) for more information on mapping many-to-many relationships.</span></span>

<span data-ttu-id="31529-337">Omitir las navegaciones tienen el aspecto y se comportan como navegación de colección normal.</span><span class="sxs-lookup"><span data-stu-id="31529-337">Skip navigations look and behave like normal collection navigations.</span></span> <span data-ttu-id="31529-338">Sin embargo, el modo en que trabajan con valores de clave externa es diferente.</span><span class="sxs-lookup"><span data-stu-id="31529-338">However, the way they work with foreign key values is different.</span></span> <span data-ttu-id="31529-339">Vamos a asociar una publicación con una etiqueta, pero esta vez mediante una omisión de navegación:</span><span class="sxs-lookup"><span data-stu-id="31529-339">Let's associate a post with a tag, but this time using a skip navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

<span data-ttu-id="31529-340">Tenga en cuenta que este código no usa la entidad join.</span><span class="sxs-lookup"><span data-stu-id="31529-340">Notice that this code doesn't use the join entity.</span></span> <span data-ttu-id="31529-341">En su lugar, simplemente agrega una entidad a una colección de navegación de la misma manera que si se tratara de una relación de uno a varios.</span><span class="sxs-lookup"><span data-stu-id="31529-341">It instead just adds an entity to a navigation collection in the same way as would be done if this were a one-to-many relationship.</span></span> <span data-ttu-id="31529-342">La vista de depuración resultante es esencialmente la misma que antes:</span><span class="sxs-lookup"><span data-stu-id="31529-342">The resulting debug view is essentially the same as before:</span></span>

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

<span data-ttu-id="31529-343">Observe que `PostTag` se ha creado automáticamente una instancia de la entidad de combinación con los valores de FK establecidos en los valores PK de la etiqueta y la publicación que ahora están asociadas.</span><span class="sxs-lookup"><span data-stu-id="31529-343">Notice that an instance of the `PostTag` join entity was created automatically with FK values set to the PK values of the tag and post that are now associated.</span></span> <span data-ttu-id="31529-344">Todas las referencias normales y las navegaciones de colección se han corregido para que coincidan con estos valores de FK.</span><span class="sxs-lookup"><span data-stu-id="31529-344">All the normal reference and collection navigations have been fixed up to match these FK values.</span></span> <span data-ttu-id="31529-345">Además, dado que este modelo contiene saltos de navegación, también se han corregido.</span><span class="sxs-lookup"><span data-stu-id="31529-345">Also, since this model contains skip navigations, these have also been fixed up.</span></span> <span data-ttu-id="31529-346">En concreto, aunque agregamos la etiqueta a la `Post.Tags` navegación de omitir, la `Tag.Posts` navegación inversa omitir en el otro lado de esta relación también se ha corregido para que contenga la publicación asociada.</span><span class="sxs-lookup"><span data-stu-id="31529-346">Specifically, even though we added the tag to the `Post.Tags` skip navigation, the `Tag.Posts` inverse skip navigation on the other side of this relationship has also been fixed up to contain the associated post.</span></span>

<span data-ttu-id="31529-347">Merece la pena tener en cuenta que las relaciones de varios a varios subyacentes todavía se pueden manipular directamente aunque se hayan superpuesto en la parte superior las navegaciones de omitir.</span><span class="sxs-lookup"><span data-stu-id="31529-347">It is worth noting that the underlying many-to-many relationships can still be manipulated directly even when skip navigations have been layered on top.</span></span> <span data-ttu-id="31529-348">Por ejemplo, la etiqueta y la publicación pueden asociarse como hicimos antes de introducir omitir navegación:</span><span class="sxs-lookup"><span data-stu-id="31529-348">For example, the tag and Post could be associated as we did before introducing skip navigations:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

<span data-ttu-id="31529-349">O usando valores de FK:</span><span class="sxs-lookup"><span data-stu-id="31529-349">Or using FK values:</span></span>

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

<span data-ttu-id="31529-350">Esto hará que las navegaciones de omisión se solucionen correctamente, lo que da lugar a la misma salida de la vista de depuración que en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="31529-350">This will still result in the skip navigations being fixed up correctly, resulting in the same debug view output as in the previous example.</span></span>

### <a name="skip-navigations-only"></a><span data-ttu-id="31529-351">Omitir solo navegación</span><span class="sxs-lookup"><span data-stu-id="31529-351">Skip navigations only</span></span>

<span data-ttu-id="31529-352">En la sección anterior hemos agregado omitir las navegaciones, _además_ de definir totalmente las dos relaciones uno a varios subyacentes.</span><span class="sxs-lookup"><span data-stu-id="31529-352">In the previous section we added skip navigations _in addition to_ fully defining the two underlying one-to-many relationships.</span></span> <span data-ttu-id="31529-353">Esto resulta útil para ilustrar lo que sucede con los valores de FK, pero a menudo no es necesario.</span><span class="sxs-lookup"><span data-stu-id="31529-353">This is useful to illustrate what happens to FK values, but is often unnecessary.</span></span> <span data-ttu-id="31529-354">En su lugar, se puede definir la relación de varios a varios usando _solo las navegaciones de omisión_.</span><span class="sxs-lookup"><span data-stu-id="31529-354">Instead, the many-to-many relationship can be defined using _only skip navigations_.</span></span> <span data-ttu-id="31529-355">Así es como se define la relación de varios a varios en el modelo en la parte superior de este documento.</span><span class="sxs-lookup"><span data-stu-id="31529-355">This is how the many-to-many relationship is defined in the model at the very top of this document.</span></span> <span data-ttu-id="31529-356">Con este modelo, se puede volver a asociar una publicación y una etiqueta agregando un comentario a la `Tag.Posts` navegación por omitir (o, como alternativa, agregando una etiqueta a la `Post.Tags` navegación omitida):</span><span class="sxs-lookup"><span data-stu-id="31529-356">Using this model, we can again associate a Post and a Tag by adding a post to the `Tag.Posts` skip navigation (or, alternately, adding a tag to the `Post.Tags` skip navigation):</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

<span data-ttu-id="31529-357">Al examinar la vista de depuración después de hacer este cambio, se revela que EF Core ha creado una instancia de `Dictionary<string, object>` para representar la entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="31529-357">Looking at the debug view after making this change reveals that EF Core has created an instance of `Dictionary<string, object>` to represent the join entity.</span></span> <span data-ttu-id="31529-358">Esta entidad join contiene `PostsId` `TagsId` las propiedades de clave externa y que se han establecido para que coincidan con los valores PK de la entrada y la etiqueta que están asociados.</span><span class="sxs-lookup"><span data-stu-id="31529-358">This join entity contains both `PostsId` and `TagsId` foreign key properties which have been set to match the PK values of the post and tag that are associated.</span></span>

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

<span data-ttu-id="31529-359">Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre las entidades de combinación implícitas y el uso de `Dictionary<string, object>` tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="31529-359">See [Relationships](xref:core/modeling/relationships) for more information about implicit join entities and the use of `Dictionary<string, object>` entity types.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="31529-360">El tipo CLR que se usa para los tipos de entidad de combinación por Convención puede cambiar en futuras versiones para mejorar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="31529-360">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="31529-361">No dependa del tipo de combinación a menos que se haya `Dictionary<string, object>` configurado explícitamente.</span><span class="sxs-lookup"><span data-stu-id="31529-361">Do not depend on the join type being `Dictionary<string, object>` unless this has been explicitly configured.</span></span>

### <a name="join-entities-with-payloads"></a><span data-ttu-id="31529-362">Unir entidades con cargas</span><span class="sxs-lookup"><span data-stu-id="31529-362">Join entities with payloads</span></span>

<span data-ttu-id="31529-363">Hasta ahora todos los ejemplos han utilizado un tipo de entidad de combinación (ya sea explícito o implícito) que solo contiene las dos propiedades de clave externa necesarias para la relación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="31529-363">So far all the examples have used a join entity type (whether explicit or implicit) that contains only the two foreign key properties needed for the many-to-many relationship.</span></span> <span data-ttu-id="31529-364">Ninguno de estos valores de FK debe establecerse explícitamente por la aplicación al manipular las relaciones, ya que sus valores proceden de las propiedades de clave principal de las entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="31529-364">Neither of these FK values need to be explicitly set by the application when manipulating relationships because their values come from the primary key properties of the related entities.</span></span> <span data-ttu-id="31529-365">Esto permite a EF Core crear instancias de la entidad de combinación sin datos que faltan.</span><span class="sxs-lookup"><span data-stu-id="31529-365">This allows EF Core to create instances of the join entity without missing data.</span></span>

#### <a name="payloads-with-generated-values"></a><span data-ttu-id="31529-366">Cargas con valores generados</span><span class="sxs-lookup"><span data-stu-id="31529-366">Payloads with generated values</span></span>

<span data-ttu-id="31529-367">EF Core admite la adición de propiedades adicionales al tipo de entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="31529-367">EF Core supports adding additional properties to the join entity type.</span></span> <span data-ttu-id="31529-368">Esto se conoce como la asignación de una "carga" a la entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="31529-368">This is known as giving the join entity a "payload".</span></span> <span data-ttu-id="31529-369">Por ejemplo, vamos a agregar `TaggedOn` la propiedad a la `PostTag` entidad de combinación:</span><span class="sxs-lookup"><span data-stu-id="31529-369">For example, let's add `TaggedOn` property to the `PostTag` join entity:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

<span data-ttu-id="31529-370">Esta propiedad de carga no se establecerá cuando EF Core crea una instancia de entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="31529-370">This payload property will not be set when EF Core creates a join entity instance.</span></span> <span data-ttu-id="31529-371">La manera más común de tratar esto es usar propiedades de carga con valores generados automáticamente.</span><span class="sxs-lookup"><span data-stu-id="31529-371">The most common way to deal with this is to use payload properties with automatically generated values.</span></span> <span data-ttu-id="31529-372">Por ejemplo, la `TaggedOn` propiedad se puede configurar para usar una marca de tiempo generada por el almacén cuando se inserta cada nueva entidad:</span><span class="sxs-lookup"><span data-stu-id="31529-372">For example, the `TaggedOn` property can be configured to use a store-generated timestamp when each new entity is inserted:</span></span>

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

<span data-ttu-id="31529-373">Ahora se puede etiquetar una publicación de la misma manera que antes:</span><span class="sxs-lookup"><span data-stu-id="31529-373">A post can now be tagged in the same way as before:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

<span data-ttu-id="31529-374">Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) después de llamar a SaveChanges, se muestra que la propiedad payload se ha establecido correctamente:</span><span class="sxs-lookup"><span data-stu-id="31529-374">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after calling SaveChanges shows that the payload property has been set appropriately:</span></span>

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

#### <a name="explicitly-setting-payload-values"></a><span data-ttu-id="31529-375">Establecer explícitamente los valores de carga</span><span class="sxs-lookup"><span data-stu-id="31529-375">Explicitly setting payload values</span></span>

<span data-ttu-id="31529-376">Después de en el ejemplo anterior, vamos a agregar una propiedad de carga que no usa un valor generado automáticamente:</span><span class="sxs-lookup"><span data-stu-id="31529-376">Following on from the previous example, let's add a payload property that does not use an automatically generated value:</span></span>

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

<span data-ttu-id="31529-377">Ahora se puede etiquetar una publicación de la misma manera que antes, y la entidad de combinación se creará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="31529-377">A post can now be tagged in the same way as before, and the join entity will still be created automatically.</span></span> <span data-ttu-id="31529-378">A continuación, se puede tener acceso a esta entidad mediante uno de los mecanismos descritos en acceso a entidades de las que se ha [realizado un seguimiento](xref:core/change-tracking/entity-entries).</span><span class="sxs-lookup"><span data-stu-id="31529-378">This entity can then be accessed using one of the mechanisms described in [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="31529-379">Por ejemplo, el código siguiente utiliza <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> para tener acceso a la instancia de la entidad de combinación:</span><span class="sxs-lookup"><span data-stu-id="31529-379">For example, the code below uses <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> to access the join entity instance:</span></span>

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

<span data-ttu-id="31529-380">Una vez que se encuentra la entidad de combinación, se puede manipular de la manera normal; en este ejemplo, para establecer la `TaggedBy` propiedad de carga antes de llamar a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="31529-380">Once the join entity has been located it can be manipulated in the normal way--in this example, to set the `TaggedBy` payload property before calling SaveChanges.</span></span>

> [!NOTE]
> <span data-ttu-id="31529-381">Tenga en cuenta que <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> aquí se requiere una llamada a para proporcionar a EF Core una oportunidad de detectar el cambio de propiedad de navegación y crear la instancia de la entidad de combinación antes de que `Find` se use.</span><span class="sxs-lookup"><span data-stu-id="31529-381">Note that a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is required here to give EF Core a chance to detect the navigation property change and create the join entity instance before `Find` is used.</span></span> <span data-ttu-id="31529-382">Consulte [detección y notificaciones de cambios](xref:core/change-tracking/change-detection) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="31529-382">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information.</span></span>

<span data-ttu-id="31529-383">Como alternativa, la entidad de combinación se puede crear explícitamente para asociar una publicación con una etiqueta.</span><span class="sxs-lookup"><span data-stu-id="31529-383">Alternately, the join entity can be created explicitly to associate a post with a tag.</span></span> <span data-ttu-id="31529-384">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="31529-384">For example:</span></span>

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

<span data-ttu-id="31529-385">Por último, otra manera de establecer los datos de carga es reemplazar <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o usar el <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> evento para procesar entidades antes de actualizar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="31529-385">Finally, another way to set payload data is by either overriding <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or using the <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> event to process entities before updating the database.</span></span> <span data-ttu-id="31529-386">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="31529-386">For example:</span></span>

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
