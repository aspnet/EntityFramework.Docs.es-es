---
title: 'Eliminación en cascada: EF Core'
description: Configuración de comportamientos en cascada que se desencadenan cuando una entidad se elimina o se interrumpe la relación con su entidad de seguridad o primaria
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 27ba84fa5d7e0d72e66ccbd96df9b6a5008791fb
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983344"
---
# <a name="cascade-delete"></a><span data-ttu-id="8d417-103">Eliminación en cascada</span><span class="sxs-lookup"><span data-stu-id="8d417-103">Cascade Delete</span></span>

<span data-ttu-id="8d417-104">Entity Framework Core (EF Core) representa relaciones mediante claves externas.</span><span class="sxs-lookup"><span data-stu-id="8d417-104">Entity Framework Core (EF Core) represents relationships using foreign keys.</span></span> <span data-ttu-id="8d417-105">Una entidad con una clave externa es la entidad secundaria o dependiente en la relación.</span><span class="sxs-lookup"><span data-stu-id="8d417-105">An entity with a foreign key is the child or dependent entity in the relationship.</span></span> <span data-ttu-id="8d417-106">El valor de clave externa de esta entidad debe coincidir con el de clave principal (o con uno de clave alternativa) de la entidad de seguridad o primaria relacionada.</span><span class="sxs-lookup"><span data-stu-id="8d417-106">This entity's foreign key value must match the primary key value (or an alternate key value) of the related principal/parent entity.</span></span>

<span data-ttu-id="8d417-107">Si se elimina la entidad de seguridad o primaria, los valores de clave externa de las entidades dependientes o secundarias dejarán de coincidir con la clave principal o la clave alternativa de _cualquier_ entidad de seguridad o primaria.</span><span class="sxs-lookup"><span data-stu-id="8d417-107">If the principal/parent entity is deleted, then the foreign key values of the dependents/children will no longer match the primary or alternate key of _any_ principal/parent.</span></span> <span data-ttu-id="8d417-108">Este estado no es válido y producirá una infracción de la restricción referencial en la mayoría de las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-108">This is an invalid state, and will cause a referential constraint violation in most databases.</span></span>

<span data-ttu-id="8d417-109">Hay dos maneras de evitar esta infracción de la restricción referencial:</span><span class="sxs-lookup"><span data-stu-id="8d417-109">There are two options to avoid this referential constraint violation:</span></span>

1. <span data-ttu-id="8d417-110">Establecer los valores de CE (clave externa) en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-110">Set the FK values to null</span></span>
2. <span data-ttu-id="8d417-111">Eliminar también las entidades dependientes o secundarias</span><span class="sxs-lookup"><span data-stu-id="8d417-111">Also delete the dependent/child entities</span></span>

<span data-ttu-id="8d417-112">La primera opción solo es válida para las relaciones opcionales en las que la propiedad de clave externa, así como la columna de la base de datos a la que está asignada, deben admitir valores NULL.</span><span class="sxs-lookup"><span data-stu-id="8d417-112">The first option in only valid for optional relationships where the foreign key property (and the database column to which it is mapped) must be nullable.</span></span>

<span data-ttu-id="8d417-113">La segunda opción es válida para cualquier tipo de relación y se conoce como "eliminación en cascada".</span><span class="sxs-lookup"><span data-stu-id="8d417-113">The second option is valid for any kind of relationship and is known as "cascade delete".</span></span>

> [!TIP]
> <span data-ttu-id="8d417-114">En este documento se describen las eliminaciones en cascada (y la eliminación de entidades huérfanas) desde el punto de vista de la actualización de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-114">This document describes cascade deletes (and deleting orphans) from the perspective of updating the database.</span></span> <span data-ttu-id="8d417-115">Se hace un uso intensivo de los conceptos introducidos en los artículos [Herramienta de seguimiento de cambios en EF Core](xref:core/change-tracking/index) y [Cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="8d417-115">It makes heavy use of concepts introduced in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="8d417-116">Debe entender perfectamente estos conceptos antes de abordar el material de este artículo.</span><span class="sxs-lookup"><span data-stu-id="8d417-116">Make sure to fully understand these concepts before tackling the material here.</span></span>

> [!TIP]  
> <span data-ttu-id="8d417-117">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).</span><span class="sxs-lookup"><span data-stu-id="8d417-117">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).</span></span>

## <a name="when-cascading-behaviors-happen"></a><span data-ttu-id="8d417-118">Cuándo se producen comportamientos en cascada</span><span class="sxs-lookup"><span data-stu-id="8d417-118">When cascading behaviors happen</span></span>

<span data-ttu-id="8d417-119">Las eliminaciones en cascada son necesarias cuando una entidad dependiente o secundaria ya no se puede asociar a su entidad de seguridad o a su entidad primaria actual.</span><span class="sxs-lookup"><span data-stu-id="8d417-119">Cascading deletes are needed when a dependent/child entity can no longer be associated with its current principal/parent.</span></span> <span data-ttu-id="8d417-120">Esto puede ocurrir porque la entidad de seguridad o primaria se elimina, o bien cuando la entidad de seguridad o primaria todavía existe, pero la entidad dependiente o secundaria ya no está asociada a ella.</span><span class="sxs-lookup"><span data-stu-id="8d417-120">This can happen because the principal/parent is deleted, or it can happen when the principal/parent still exists but the dependent/child is no longer associated with it.</span></span>

### <a name="deleting-a-principalparent"></a><span data-ttu-id="8d417-121">Eliminación de una entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-121">Deleting a principal/parent</span></span>

<span data-ttu-id="8d417-122">Considere este modelo sencillo en el que `Blog` es la entidad de seguridad o primaria en una relación con `Post`, que es la entidad dependiente o secundaria.</span><span class="sxs-lookup"><span data-stu-id="8d417-122">Consider this simple model where `Blog` is the principal/parent in a relationship with `Post`, which is the dependent/child.</span></span> <span data-ttu-id="8d417-123">`Post.BlogId` es una propiedad de clave externa cuyo valor debe coincidir con el de la clave principal `Post.Id` de la publicación a la que pertenece el blog.</span><span class="sxs-lookup"><span data-stu-id="8d417-123">`Post.BlogId` is a foreign key property, the value of which must match the `Post.Id` primary key of the post to which the blog belongs.</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

<span data-ttu-id="8d417-124">Por convención, esta relación se configura como obligatoria, ya que la propiedad de clave externa `Post.BlogId` no acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="8d417-124">By convention, this relationship is configured as a required, since the `Post.BlogId` foreign key property is non-nullable.</span></span> <span data-ttu-id="8d417-125">Las relaciones obligatorias están configuradas para usar eliminaciones en cascada de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8d417-125">Required relationships are configured to use cascade deletes by default.</span></span> <span data-ttu-id="8d417-126">Consulte el artículo [Relaciones](xref:core/modeling/relationships) para obtener más información sobre las relaciones de modelado.</span><span class="sxs-lookup"><span data-stu-id="8d417-126">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships.</span></span>

<span data-ttu-id="8d417-127">Al eliminar un blog, se eliminan todas las publicaciones en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-127">When deleting a blog, all posts are cascade deleted.</span></span> <span data-ttu-id="8d417-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8d417-128">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

<span data-ttu-id="8d417-129">SaveChanges genera el siguiente código SQL, usando SQL Server como ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8d417-129">SaveChanges generates the following SQL, using SQL Server as an example:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a><span data-ttu-id="8d417-130">Interrupción de una relación</span><span class="sxs-lookup"><span data-stu-id="8d417-130">Severing a relationship</span></span>

<span data-ttu-id="8d417-131">En lugar de eliminar el blog, podríamos interrumpir la relación entre cada publicación y su blog.</span><span class="sxs-lookup"><span data-stu-id="8d417-131">Rather than deleting the blog, we could instead sever the relationship between each post and its blog.</span></span> <span data-ttu-id="8d417-132">Esto se puede hacer estableciendo la propiedad de navegación de referencia `Post.Blog` en NULL para cada publicación:</span><span class="sxs-lookup"><span data-stu-id="8d417-132">This can be done by setting the reference navigation `Post.Blog` to null for each post:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

<span data-ttu-id="8d417-133">La relación también se puede interrumpir quitando todas las publicaciones de la navegación de colección `Blog.Posts`:</span><span class="sxs-lookup"><span data-stu-id="8d417-133">The relationship can also be severed by removing each post from the `Blog.Posts` collection navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

<span data-ttu-id="8d417-134">En cualquier caso, el resultado es el mismo: el blog no se elimina, solo se eliminan las publicaciones que ya no están asociadas a ningún blog:</span><span class="sxs-lookup"><span data-stu-id="8d417-134">In either case the result is the same: the blog is not deleted, but the posts that are no longer associated with any blog are deleted:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="8d417-135">La eliminación de entidades que ya no están asociadas a ninguna entidad de seguridad o dependiente se denomina "eliminación de entidades huérfanas".</span><span class="sxs-lookup"><span data-stu-id="8d417-135">Deleting entities that are no longer associated with any principal/dependent is known as "deleting orphans".</span></span>

> [!TIP]
> <span data-ttu-id="8d417-136">La eliminación en cascada y la eliminación de entidades huérfanas están estrechamente relacionadas.</span><span class="sxs-lookup"><span data-stu-id="8d417-136">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="8d417-137">Ambas dan como resultado la eliminación de entidades dependientes o secundarias cuando se interrumpe su relación con la entidad de seguridad o primaria requerida.</span><span class="sxs-lookup"><span data-stu-id="8d417-137">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="8d417-138">En el caso de la eliminación en cascada, esta interrupción de la relación tiene lugar porque se elimina la propia entidad de seguridad o primaria.</span><span class="sxs-lookup"><span data-stu-id="8d417-138">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="8d417-139">En el caso de las entidades huérfanas, la entidad de seguridad o primaria sigue existiendo, pero ya no está relacionada con las entidades dependientes o secundarias.</span><span class="sxs-lookup"><span data-stu-id="8d417-139">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>  

## <a name="where-cascading-behaviors-happen"></a><span data-ttu-id="8d417-140">Dónde se producen los comportamientos en cascada</span><span class="sxs-lookup"><span data-stu-id="8d417-140">Where cascading behaviors happen</span></span>

<span data-ttu-id="8d417-141">Los comportamientos en cascada se pueden aplicar a:</span><span class="sxs-lookup"><span data-stu-id="8d417-141">Cascading behaviors can be applied to:</span></span>

- <span data-ttu-id="8d417-142">Entidades de las que la clase <xref:Microsoft.EntityFrameworkCore.DbContext> actual realiza un seguimiento</span><span class="sxs-lookup"><span data-stu-id="8d417-142">Entities tracked by the current <xref:Microsoft.EntityFrameworkCore.DbContext></span></span>
- <span data-ttu-id="8d417-143">Entidades de la base de datos que no se han cargado en el contexto</span><span class="sxs-lookup"><span data-stu-id="8d417-143">Entities in the database that have not been loaded into the context</span></span>

### <a name="cascade-delete-of-tracked-entities"></a><span data-ttu-id="8d417-144">Eliminación en cascada de entidades de las que se realiza un seguimiento</span><span class="sxs-lookup"><span data-stu-id="8d417-144">Cascade delete of tracked entities</span></span>

<span data-ttu-id="8d417-145">EF Core aplica siempre los comportamientos en cascada configurados a las entidades sometidas a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="8d417-145">EF Core always applies configured cascading behaviors to tracked entities.</span></span> <span data-ttu-id="8d417-146">Esto significa que, si la aplicación carga todas las entidades dependientes o secundarias relevantes en la instancia de DbContext (como se muestra en los ejemplos anteriores), los comportamientos en cascada se aplicarán correctamente independientemente de cómo esté configurada la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-146">This means that if the application loads all relevant dependent/child entities into the DbContext, as is shown in the examples above, then cascading behaviors will be correctly applied regardless of how the database is configured.</span></span>

> [!TIP]
> <span data-ttu-id="8d417-147">El momento exacto en que se producen los comportamientos en cascada para las entidades sometidas a seguimiento se puede controlar mediante las propiedades <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="8d417-147">The exact timing of when cascading behaviors happen to tracked entities can be controlled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8d417-148">Para obtener más información, vea el artículo [Cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="8d417-148">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

### <a name="cascade-delete-in-the-database"></a><span data-ttu-id="8d417-149">Eliminaciones en cascada en la base de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-149">Cascade delete in the database</span></span>

<span data-ttu-id="8d417-150">Muchos sistemas de base de datos también ofrecen comportamientos en cascada, los cuales se desencadenan cuando se elimina una entidad en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-150">Many database systems also offer cascading behaviors that are triggered when an entity is deleted in the database.</span></span> <span data-ttu-id="8d417-151">EF Core configura estos comportamientos en función del comportamiento de eliminación en cascada del modelo de EF Core cuando se crea una base de datos con migraciones de <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> o EF Core.</span><span class="sxs-lookup"><span data-stu-id="8d417-151">EF Core configures these behaviors based on the cascade delete behavior in the EF Core model when a database is created using <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> or EF Core migrations.</span></span> <span data-ttu-id="8d417-152">Por ejemplo, con el modelo anterior, se crea la siguiente tabla para las publicaciones cuando se usa SQL Server:</span><span class="sxs-lookup"><span data-stu-id="8d417-152">For example, using the model above, the following table is created for posts when using SQL Server:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

<span data-ttu-id="8d417-153">Fíjese en que la restricción de clave externa que define la relación entre los blogs y las publicaciones está configurada con `ON DELETE CASCADE`.</span><span class="sxs-lookup"><span data-stu-id="8d417-153">Notice that the foreign key constraint defining the relationship between blogs and posts is configured with `ON DELETE CASCADE`.</span></span>

<span data-ttu-id="8d417-154">Si sabemos que la base de datos está configurada de esta manera, podemos eliminar un blog _sin cargar primero las publicaciones_, ya que la base de datos se encargará de eliminar todas las publicaciones relacionadas con ese blog.</span><span class="sxs-lookup"><span data-stu-id="8d417-154">If we know that the database is configured like this, then we can delete a blog _without first loading posts_ and the database will take care of deleting all the posts that were related to that blog.</span></span> <span data-ttu-id="8d417-155">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8d417-155">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

<span data-ttu-id="8d417-156">Observe que no hay ningún método `Include` para las publicaciones, por lo que no se cargan.</span><span class="sxs-lookup"><span data-stu-id="8d417-156">Notice that there is no `Include` for posts, so they are not loaded.</span></span> <span data-ttu-id="8d417-157">En este caso, SaveChanges eliminará solo el blog, ya que es la única entidad de la que se realiza un seguimiento:</span><span class="sxs-lookup"><span data-stu-id="8d417-157">SaveChanges in this case will delete just the blog, since that's the only entity being tracked:</span></span>

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="8d417-158">Esto produciría una excepción si la restricción de clave externa de la base de datos no estuviera configurada para las eliminaciones en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-158">This would result in an exception if the foreign key constraint in the database is not configured for cascade deletes.</span></span> <span data-ttu-id="8d417-159">Pero, en este caso, la base de datos elimina las publicaciones porque se ha configurado con `ON DELETE CASCADE` en el momento de su creación.</span><span class="sxs-lookup"><span data-stu-id="8d417-159">However, in this case the posts are deleted by the database because it has been configured with `ON DELETE CASCADE` when it was created.</span></span>

> [!NOTE]
> <span data-ttu-id="8d417-160">Las bases de datos no suelen tener ninguna forma de eliminar automáticamente las entidades huérfanas.</span><span class="sxs-lookup"><span data-stu-id="8d417-160">Databases don't typically have any way to automatically delete orphans.</span></span> <span data-ttu-id="8d417-161">Esto se debe a que EF Core representa las relaciones mediante navegaciones y claves externas, pero las bases de datos solo tienen claves externas.</span><span class="sxs-lookup"><span data-stu-id="8d417-161">This is because while EF Core represents relationships using navigations as well of foreign keys, databases have only foreign keys and no navigations.</span></span> <span data-ttu-id="8d417-162">Lo que significa que normalmente no es posible interrumpir una relación sin cargar ambos lados en la instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="8d417-162">This means that it is usually not possible to sever a relationship without loading both sides into the DbContext.</span></span>

> [!NOTE]
> <span data-ttu-id="8d417-163">Actualmente, la base de datos en memoria de EF Core no admite las eliminaciones en cascada en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-163">The EF Core in-memory database does not currently support cascade deletes in the database.</span></span>

> [!WARNING]
> <span data-ttu-id="8d417-164">No configure la eliminación en cascada en la base de datos cuando quiera eliminar temporalmente las entidades.</span><span class="sxs-lookup"><span data-stu-id="8d417-164">Do not configure cascade delete in the database when soft-deleting entities.</span></span> <span data-ttu-id="8d417-165">Esto podría eliminar por completo las entidades accidentalmente en lugar de eliminarlas temporalmente.</span><span class="sxs-lookup"><span data-stu-id="8d417-165">This may cause entities to be accidentally really deleted instead of soft-deleted.</span></span>

### <a name="database-cascade-limitations"></a><span data-ttu-id="8d417-166">Limitaciones de los comportamientos en cascada en las bases de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-166">Database cascade limitations</span></span>

<span data-ttu-id="8d417-167">Algunas bases de datos, especialmente las de SQL Server, tienen limitaciones en cuanto a los comportamientos en cascada que forman ciclos.</span><span class="sxs-lookup"><span data-stu-id="8d417-167">Some databases, most notably SQL Server, have limitations on the cascade behaviors that form cycles.</span></span> <span data-ttu-id="8d417-168">Por ejemplo, tomemos el siguiente modelo:</span><span class="sxs-lookup"><span data-stu-id="8d417-168">For example, consider the following model:</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

<span data-ttu-id="8d417-169">Este modelo tiene tres relaciones, todas necesarias y, por tanto, están configuradas para la eliminación en cascada por convención:</span><span class="sxs-lookup"><span data-stu-id="8d417-169">This model has three relationships, all required and therefore configured to cascade delete by convention:</span></span>

- <span data-ttu-id="8d417-170">Al eliminar un blog, se eliminarán en cascada todas las publicaciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="8d417-170">Deleting a blog will cascade delete all the related posts</span></span>
- <span data-ttu-id="8d417-171">Al eliminar al autor de las publicaciones, se eliminarán en cascada sus publicaciones creadas.</span><span class="sxs-lookup"><span data-stu-id="8d417-171">Deleting the author of posts will cause the authored posts to be cascade deleted</span></span>
- <span data-ttu-id="8d417-172">Al eliminar al propietario de un blog, el blog se eliminará en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-172">Deleting the owner of a blog will cause the blog to be cascade deleted</span></span>

<span data-ttu-id="8d417-173">Esto es todo razonable (aunque las directivas de administración de blogs son un poco severas), pero al intentar crear una base de datos de SQL Server con estas eliminaciones en cascada configuradas, se produce la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="8d417-173">This is all reasonable (if a bit draconian in blog management policies!) but attempting to create a SQL Server database with these cascades configured results in the following exception:</span></span>

> <span data-ttu-id="8d417-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Si especifica la restricción FOREIGN KEY "FK_Posts_Person_AuthorId" en la tabla "Posts", podrían producirse ciclos o múltiples rutas en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Introducing FOREIGN KEY constraint 'FK_Posts_Person_AuthorId' on table 'Posts' may cause cycles or multiple cascade paths.</span></span> <span data-ttu-id="8d417-175">Especifique ON DELETE NO ACTION o UPDATE NO ACTION, o bien modifique otras restricciones FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="8d417-175">Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.</span></span>

<span data-ttu-id="8d417-176">Hay dos formas de abordar esta situación:</span><span class="sxs-lookup"><span data-stu-id="8d417-176">There are two ways to handle this situation:</span></span>

1. <span data-ttu-id="8d417-177">Cambiar una o varias relaciones para que no se eliminen en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-177">Change one or more of the relationships to not cascade delete.</span></span>
2. <span data-ttu-id="8d417-178">Configurar la base de datos sin una o varias de estas eliminaciones en cascada y asegurarse de que se cargan todas las entidades dependientes para que EF Core pueda realizar el comportamiento en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-178">Configure the database without one or more of these cascade deletes, then ensure all dependent entities are loaded so that EF Core can perform the cascading behavior.</span></span>

<span data-ttu-id="8d417-179">Tomando el primer enfoque con nuestro ejemplo, podríamos hacer que la relación entre el propietario y el blog fuera opcional asignándole una propiedad de clave externa que acepte valores NULL:</span><span class="sxs-lookup"><span data-stu-id="8d417-179">Taking the first approach with our example, we could make the blog-owner relationship optional by giving it a nullable foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="8d417-180">Una relación opcional permite que el blog exista sin un propietario, lo que significa que la eliminación en cascada ya no se configurará de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8d417-180">An optional relationship allows the blog to exist without an owner, which means cascade delete will no longer be configured by default.</span></span> <span data-ttu-id="8d417-181">También significa que ya no hay un ciclo en las acciones en cascada y, por tanto, la base de datos se puede crear sin errores en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8d417-181">This means there is no longer a cycle in cascading actions, and the database can be created without error on SQL Server.</span></span>

<span data-ttu-id="8d417-182">Si tomamos el segundo enfoque, podemos mantener la relación entre el propietario y el blog como obligatoria, configurarla para la eliminación en cascada y hacer que esta configuración solo se aplique a las entidades sometidas a seguimiento, no a la base de datos:</span><span class="sxs-lookup"><span data-stu-id="8d417-182">Taking the second approach instead, we can keep the blog-owner relationship required and configured for cascade delete, but make this configuration only apply to tracked entities, not the database:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="8d417-183">¿Qué ocurre si se cargan tanto un propietario como el blog que posee y luego se elimina al propietario?</span><span class="sxs-lookup"><span data-stu-id="8d417-183">Now what happens if we load both a person and the blog they own, then delete the person?</span></span>

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

<span data-ttu-id="8d417-184">EF Core eliminará en cascada al propietario para que también se elimine el blog:</span><span class="sxs-lookup"><span data-stu-id="8d417-184">EF Core will cascade the delete of the owner so that the blog is also deleted:</span></span>

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="8d417-185">Pero el blog puede no cargarse cuando se elimina al propietario:</span><span class="sxs-lookup"><span data-stu-id="8d417-185">However, if the blog is not loaded when the owner is deleted:</span></span>

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

<span data-ttu-id="8d417-186">Esto generará una excepción debido a una infracción de la restricción de clave externa en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="8d417-186">Then an exception will be thrown due to violation of the foreign key constraint in the database:</span></span>

> <span data-ttu-id="8d417-187">Microsoft.Data.SqlClient.SqlException: Instrucción DELETE en conflicto con la restricción REFERENCE "FK_Blogs_People_OwnerId".</span><span class="sxs-lookup"><span data-stu-id="8d417-187">Microsoft.Data.SqlClient.SqlException: The DELETE statement conflicted with the REFERENCE constraint "FK_Blogs_People_OwnerId".</span></span> <span data-ttu-id="8d417-188">El conflicto ha aparecido en la base de datos "Scratch", tabla "dbo.Blogs", columna "OwnerId".</span><span class="sxs-lookup"><span data-stu-id="8d417-188">The conflict occurred in database "Scratch", table "dbo.Blogs", column 'OwnerId'.</span></span>
<span data-ttu-id="8d417-189">Se terminó la instrucción.</span><span class="sxs-lookup"><span data-stu-id="8d417-189">The statement has been terminated.</span></span>

## <a name="cascading-nulls"></a><span data-ttu-id="8d417-190">Establecimiento de valores NULL en cascada</span><span class="sxs-lookup"><span data-stu-id="8d417-190">Cascading nulls</span></span>

<span data-ttu-id="8d417-191">Las relaciones opcionales tienen propiedades de clave externa que aceptan valores NULL asignadas a columnas de base de datos que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="8d417-191">Optional relationships have nullable foreign key properties mapped to nullable database columns.</span></span> <span data-ttu-id="8d417-192">Esto significa que el valor de clave externa se puede establecer en NULL cuando la entidad de seguridad o primaria actual se elimina o cuando se interrumpe su relación con la entidad dependiente o secundaria.</span><span class="sxs-lookup"><span data-stu-id="8d417-192">This means that the foreign key value can be set to null when the current principal/parent is deleted or is severed from the dependent/child.</span></span>

<span data-ttu-id="8d417-193">Echemos un vistazo de nuevo a los ejemplos del apartado [Cuándo se producen comportamientos en cascada](#when-cascading-behaviors-happen), pero esta vez con una relación opcional representada por una propiedad de clave externa `Post.BlogId` que acepta valores NULL:</span><span class="sxs-lookup"><span data-stu-id="8d417-193">Let's look again at the examples from [When cascading behaviors happen](#when-cascading-behaviors-happen), but this time with an optional relationship represented by a nullable `Post.BlogId` foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="8d417-194">Esta propiedad de clave externa se establecerá en NULL para cada publicación cuando se elimine su blog relacionado.</span><span class="sxs-lookup"><span data-stu-id="8d417-194">This foreign key property will be set to null for each post when its related blog is deleted.</span></span> <span data-ttu-id="8d417-195">Por ejemplo, tenemos este código (que es el mismo que antes):</span><span class="sxs-lookup"><span data-stu-id="8d417-195">For example, this code, which is the same as before:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

<span data-ttu-id="8d417-196">Y este código ahora dará como resultado las siguientes actualizaciones de la base de datos cuando se llame a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="8d417-196">Will now result in the following database updates when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="8d417-197">Del mismo modo, si la relación se interrumpe con cualquiera de los ejemplos anteriores, como el siguiente:</span><span class="sxs-lookup"><span data-stu-id="8d417-197">Likewise, if the relationship is severed using either of the examples from above:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

<span data-ttu-id="8d417-198">O:</span><span class="sxs-lookup"><span data-stu-id="8d417-198">Or:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

<span data-ttu-id="8d417-199">En estos casos, se actualizan las publicaciones con valores de clave externa NULL cuando se llama a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="8d417-199">Then the posts are updated with null foreign key values when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="8d417-200">Consulte el artículo [Cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) para obtener más información sobre cómo EF Core administra las claves externas y las navegaciones a medida que se cambian sus valores.</span><span class="sxs-lookup"><span data-stu-id="8d417-200">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on how EF Core manages foreign keys and navigations as their values are changed.</span></span>

> [!NOTE]
> <span data-ttu-id="8d417-201">La corrección de relaciones como esta es el comportamiento predeterminado de Entity Framework desde la primera versión en 2008.</span><span class="sxs-lookup"><span data-stu-id="8d417-201">The fixup of relationships like this has been the default behavior of Entity Framework since the first version in 2008.</span></span> <span data-ttu-id="8d417-202">Antes de EF Core, este comportamiento no tenía nombre y no era posible cambiarlo.</span><span class="sxs-lookup"><span data-stu-id="8d417-202">Prior to EF Core it didn't have a name and was not possible to change.</span></span> <span data-ttu-id="8d417-203">Ahora se conoce como `ClientSetNull`, tal y como se describe en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="8d417-203">It is now known as `ClientSetNull` as described in the next section.</span></span>

<span data-ttu-id="8d417-204">Las bases de datos también se pueden configurar para establecer valores NULL en cascada de esta forma cuando se elimina una entidad de seguridad o primaria en una relación opcional,</span><span class="sxs-lookup"><span data-stu-id="8d417-204">Databases can also be configured to cascade nulls like this when a principal/parent in an optional relationship is deleted.</span></span> <span data-ttu-id="8d417-205">aunque esto es mucho menos común que el uso de eliminaciones en cascada en las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-205">However, this is much less common than using cascading deletes in the database.</span></span> <span data-ttu-id="8d417-206">El uso de eliminaciones en cascada y del establecimiento de valores NULL en cascada en la base de datos al mismo tiempo producirá casi siempre ciclos de relación cuando se use SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8d417-206">Using cascading deletes and cascading nulls in the database at the same time will almost always result in relationship cycles when using SQL Server.</span></span> <span data-ttu-id="8d417-207">Consulte la sección siguiente para obtener más información sobre la configuración de valores NULL en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-207">See the next section for more information on configuring cascading nulls.</span></span>

## <a name="configuring-cascading-behaviors"></a><span data-ttu-id="8d417-208">Configuración de comportamientos en cascada</span><span class="sxs-lookup"><span data-stu-id="8d417-208">Configuring cascading behaviors</span></span>

> [!TIP]
> <span data-ttu-id="8d417-209">Asegúrese de leer las secciones anteriores antes de llegar aquí.</span><span class="sxs-lookup"><span data-stu-id="8d417-209">Be sure to read sections above before coming here.</span></span> <span data-ttu-id="8d417-210">Es posible que las opciones de configuración no tengan sentido si no se comprende el material anterior.</span><span class="sxs-lookup"><span data-stu-id="8d417-210">The configuration options will likely not make sense if the preceding material is not understood.</span></span>

<span data-ttu-id="8d417-211">Los comportamientos en cascada se configuran por relación mediante el método <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="8d417-211">Cascade behaviors are configured per relationship using the <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> method in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="8d417-212">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8d417-212">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="8d417-213">Consulte el artículo [Relaciones](xref:core/modeling/relationships) para obtener más información sobre la configuración de relaciones entre tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="8d417-213">See [Relationships](xref:core/modeling/relationships) for more information on configuring relationships between entity types.</span></span>

<span data-ttu-id="8d417-214">`OnDelete` acepta un valor de la enumeración <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> (que es ciertamente confusa).</span><span class="sxs-lookup"><span data-stu-id="8d417-214">`OnDelete` accepts a value from the, admittedly confusing, <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> enum.</span></span> <span data-ttu-id="8d417-215">Esta enumeración define el comportamiento de EF Core en las entidades sometidas a seguimiento y también la configuración de eliminación en cascada en la base de datos cuando se usa EF para crear el esquema.</span><span class="sxs-lookup"><span data-stu-id="8d417-215">This enum defines both the behavior of EF Core on tracked entities, and the configuration of cascade delete in the database when EF is used to create the schema.</span></span>

### <a name="impact-on-database-schema"></a><span data-ttu-id="8d417-216">Impacto en el esquema de la base de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-216">Impact on database schema</span></span>

<span data-ttu-id="8d417-217">En la siguiente tabla se muestra el resultado de cada valor `OnDelete` en la restricción de clave externa creada por migraciones de EF Core o <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span><span class="sxs-lookup"><span data-stu-id="8d417-217">The following table shows the result of each `OnDelete` value on the foreign key constraint created by EF Core migrations or <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span></span>

| <span data-ttu-id="8d417-218">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="8d417-218">DeleteBehavior</span></span>        | <span data-ttu-id="8d417-219">Impacto en el esquema de la base de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-219">Impact on database schema</span></span>
|:----------------------|--------------------------
| <span data-ttu-id="8d417-220">Cascade</span><span class="sxs-lookup"><span data-stu-id="8d417-220">Cascade</span></span>               | <span data-ttu-id="8d417-221">ON DELETE CASCADE</span><span class="sxs-lookup"><span data-stu-id="8d417-221">ON DELETE CASCADE</span></span>
| <span data-ttu-id="8d417-222">Restringir</span><span class="sxs-lookup"><span data-stu-id="8d417-222">Restrict</span></span>              | <span data-ttu-id="8d417-223">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="8d417-223">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="8d417-224">NoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-224">NoAction</span></span>              | <span data-ttu-id="8d417-225">database default</span><span class="sxs-lookup"><span data-stu-id="8d417-225">database default</span></span>
| <span data-ttu-id="8d417-226">SetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-226">SetNull</span></span>               | <span data-ttu-id="8d417-227">ON DELETE SET NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-227">ON DELETE SET NULL</span></span>
| <span data-ttu-id="8d417-228">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-228">ClientSetNull</span></span>         | <span data-ttu-id="8d417-229">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="8d417-229">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="8d417-230">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="8d417-230">ClientCascade</span></span>         | <span data-ttu-id="8d417-231">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="8d417-231">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="8d417-232">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-232">ClientNoAction</span></span>        | <span data-ttu-id="8d417-233">database default</span><span class="sxs-lookup"><span data-stu-id="8d417-233">database default</span></span>

> [!NOTE]
> <span data-ttu-id="8d417-234">Esta tabla es confusa y tenemos previsto revisarla en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="8d417-234">This table is confusing and we plan to revisit this in a future release.</span></span> <span data-ttu-id="8d417-235">Consulte la [incidencia n.º 21252 de GitHub](https://github.com/dotnet/efcore/issues/21252).</span><span class="sxs-lookup"><span data-stu-id="8d417-235">See [GitHub Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span></span>

<span data-ttu-id="8d417-236">Los comportamientos de `ON DELETE NO ACTION` y `ON DELETE RESTRICT` en las bases de datos relacionales suelen ser idénticos o muy parecidos.</span><span class="sxs-lookup"><span data-stu-id="8d417-236">The behaviors of `ON DELETE NO ACTION` and `ON DELETE RESTRICT` in relational databases are typically either identical or very similar.</span></span> <span data-ttu-id="8d417-237">A pesar de lo que puede implicar `NO ACTION`, ambas opciones hacen que se apliquen las restricciones referenciales.</span><span class="sxs-lookup"><span data-stu-id="8d417-237">Despite what `NO ACTION` may imply, both of these options cause referential constraints to be enforced.</span></span> <span data-ttu-id="8d417-238">La diferencia, si la hay, es _cuándo_ comprueba las restricciones la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-238">The difference, when there is one, is _when_ the database checks the constraints.</span></span>  <span data-ttu-id="8d417-239">Consulte la documentación de su base de datos para conocer las diferencias específicas entre `ON DELETE NO ACTION` y `ON DELETE RESTRICT` en su sistema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-239">Check your database documentation for the specific differences between `ON DELETE NO ACTION` and `ON DELETE RESTRICT` on your database system.</span></span>

<span data-ttu-id="8d417-240">Los únicos valores que generarán comportamientos en cascada en la base de datos son `Cascade` y `SetNull`.</span><span class="sxs-lookup"><span data-stu-id="8d417-240">The only values that will cause cascading behaviors on the database are `Cascade` and `SetNull`.</span></span> <span data-ttu-id="8d417-241">El resto de valores configurarán la base de datos para que no se realice ningún cambio en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-241">All other values will configure the database to not cascade any changes.</span></span>

### <a name="impact-on-savechanges-behavior"></a><span data-ttu-id="8d417-242">Impacto en el comportamiento de SaveChanges</span><span class="sxs-lookup"><span data-stu-id="8d417-242">Impact on SaveChanges behavior</span></span>

<span data-ttu-id="8d417-243">En las tablas de las siguientes secciones se describe lo que sucede con las entidades dependientes o secundarias cuando se elimina la entidad de seguridad o primaria, así como cuando se interrumpe su relación con las entidades dependientes o secundarias.</span><span class="sxs-lookup"><span data-stu-id="8d417-243">The tables in the following sections cover what happens to dependent/child entities when the principal/parent is deleted, or its relationship to the dependent/child entities is severed.</span></span> <span data-ttu-id="8d417-244">Cada tabla abarca uno de los siguientes casos:</span><span class="sxs-lookup"><span data-stu-id="8d417-244">Each table covers one of:</span></span>

- <span data-ttu-id="8d417-245">Relaciones opcionales (CE que admiten valores NULL) y obligatorias (CE que no aceptan valores NULL)</span><span class="sxs-lookup"><span data-stu-id="8d417-245">Optional (nullable FK) and required (non-nullable FK) relationships</span></span>
- <span data-ttu-id="8d417-246">Cuando la instancia de DbContext carga y realiza el seguimiento de las entidades dependientes o secundarias, y cuando estas solo existen en la base de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-246">When dependents/children are loaded and tracked by the DbContext and when they exist only in the database</span></span>

#### <a name="required-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="8d417-247">Relación obligatoria con las entidades dependientes o secundarias cargadas</span><span class="sxs-lookup"><span data-stu-id="8d417-247">Required relationship with dependents/children loaded</span></span>

| <span data-ttu-id="8d417-248">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="8d417-248">DeleteBehavior</span></span>    | <span data-ttu-id="8d417-249">Impacto al eliminar una entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-249">On deleting principal/parent</span></span>             | <span data-ttu-id="8d417-250">Impacto al interrumpir la relación con la entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-250">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="8d417-251">Cascade</span><span class="sxs-lookup"><span data-stu-id="8d417-251">Cascade</span></span>           | <span data-ttu-id="8d417-252">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-252">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="8d417-253">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-253">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="8d417-254">Restringir</span><span class="sxs-lookup"><span data-stu-id="8d417-254">Restrict</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="8d417-255">NoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-255">NoAction</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="8d417-256">SetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-256">SetNull</span></span>           | <span data-ttu-id="8d417-257">Se produce una excepción `SqlException` al crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-257">`SqlException` on creating database</span></span>      | <span data-ttu-id="8d417-258">Se produce una excepción `SqlException` al crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-258">`SqlException` on creating database</span></span>
| <span data-ttu-id="8d417-259">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-259">ClientSetNull</span></span>     | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="8d417-260">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="8d417-260">ClientCascade</span></span>     | <span data-ttu-id="8d417-261">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-261">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="8d417-262">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-262">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="8d417-263">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-263">ClientNoAction</span></span>    | `DbUpdateException`                      | `InvalidOperationException`

<span data-ttu-id="8d417-264">Notas:</span><span class="sxs-lookup"><span data-stu-id="8d417-264">Notes:</span></span>

- <span data-ttu-id="8d417-265">El valor predeterminado para las relaciones obligatorias como esta es `Cascade`.</span><span class="sxs-lookup"><span data-stu-id="8d417-265">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="8d417-266">El uso de cualquier cosa que no sea la eliminación en cascada para las relaciones obligatorias producirá una excepción cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="8d417-266">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="8d417-267">Normalmente, se trata de una excepción `InvalidOperationException` de EF Core, puesto que el estado no válido se detecta en las entidades secundarias o dependientes cargadas.</span><span class="sxs-lookup"><span data-stu-id="8d417-267">Typically, this is an `InvalidOperationException` from EF Core since the invalid state is detected in the loaded children/dependents.</span></span>
  - <span data-ttu-id="8d417-268">`ClientNoAction` obliga a EF Core a no comprobar la corrección de las entidades dependientes antes de enviarlas a la base de datos, por lo que en este caso la base de datos produce una excepción, y SaveChanges la encapsula en una excepción `DbUpdateException`.</span><span class="sxs-lookup"><span data-stu-id="8d417-268">`ClientNoAction` forces EF Core to not check fixup dependents before sending them to the database, so in this case the database throws an exception, which is then wrapped in a `DbUpdateException` by SaveChanges.</span></span>
  - <span data-ttu-id="8d417-269">El método `SetNull` se rechaza al crear la base de datos, ya que la columna de clave externa no admite valores NULL.</span><span class="sxs-lookup"><span data-stu-id="8d417-269">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>
- <span data-ttu-id="8d417-270">Como las entidades dependientes o secundarias están cargadas, EF Core siempre las elimina y nunca se dejan para que la base de datos las elimine.</span><span class="sxs-lookup"><span data-stu-id="8d417-270">Since dependents/children are loaded, they are always deleted by EF Core, and never left for the database to delete.</span></span>

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="8d417-271">Relación obligatoria con las entidades dependientes o secundarias no cargadas</span><span class="sxs-lookup"><span data-stu-id="8d417-271">Required relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="8d417-272">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="8d417-272">DeleteBehavior</span></span>    | <span data-ttu-id="8d417-273">Impacto al eliminar una entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-273">On deleting principal/parent</span></span>             | <span data-ttu-id="8d417-274">Impacto al interrumpir la relación con la entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-274">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="8d417-275">Cascade</span><span class="sxs-lookup"><span data-stu-id="8d417-275">Cascade</span></span>           | <span data-ttu-id="8d417-276">La base de datos elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-276">Dependents deleted by database</span></span>           | <span data-ttu-id="8d417-277">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-277">N/A</span></span>
| <span data-ttu-id="8d417-278">Restringir</span><span class="sxs-lookup"><span data-stu-id="8d417-278">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="8d417-279">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-279">N/A</span></span>
| <span data-ttu-id="8d417-280">NoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-280">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="8d417-281">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-281">N/A</span></span>
| <span data-ttu-id="8d417-282">SetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-282">SetNull</span></span>           | <span data-ttu-id="8d417-283">Se produce una excepción `SqlException` al crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="8d417-283">`SqlException` on creating database</span></span>      | <span data-ttu-id="8d417-284">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-284">N/A</span></span>
| <span data-ttu-id="8d417-285">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-285">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="8d417-286">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-286">N/A</span></span>
| <span data-ttu-id="8d417-287">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="8d417-287">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="8d417-288">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-288">N/A</span></span>
| <span data-ttu-id="8d417-289">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-289">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="8d417-290">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-290">N/A</span></span>

<span data-ttu-id="8d417-291">Notas:</span><span class="sxs-lookup"><span data-stu-id="8d417-291">Notes:</span></span>

- <span data-ttu-id="8d417-292">No es posible interrumpir la relación en este caso, ya que las entidades dependientes o secundarias no están cargadas.</span><span class="sxs-lookup"><span data-stu-id="8d417-292">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="8d417-293">El valor predeterminado para las relaciones obligatorias como esta es `Cascade`.</span><span class="sxs-lookup"><span data-stu-id="8d417-293">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="8d417-294">El uso de cualquier cosa que no sea la eliminación en cascada para las relaciones obligatorias producirá una excepción cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="8d417-294">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="8d417-295">Normalmente, se trata de una excepción `DbUpdateException` porque las entidades dependientes o secundarias no están cargadas y, por lo tanto, el estado no válido solo puede detectarlo la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d417-295">Typically, this is a `DbUpdateException` because the dependents/children are not loaded, and hence the invalid state can only be detected by the database.</span></span> <span data-ttu-id="8d417-296">Después, SaveChanges encapsula la excepción de la base de datos en una excepción `DbUpdateException`.</span><span class="sxs-lookup"><span data-stu-id="8d417-296">SaveChanges then wraps the database exception in a `DbUpdateException`.</span></span>
  - <span data-ttu-id="8d417-297">El método `SetNull` se rechaza al crear la base de datos, ya que la columna de clave externa no admite valores NULL.</span><span class="sxs-lookup"><span data-stu-id="8d417-297">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>

#### <a name="optional-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="8d417-298">Relación opcional con las entidades dependientes o secundarias cargadas</span><span class="sxs-lookup"><span data-stu-id="8d417-298">Optional relationship with dependents/children loaded</span></span>

| <span data-ttu-id="8d417-299">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="8d417-299">DeleteBehavior</span></span>    | <span data-ttu-id="8d417-300">Impacto al eliminar una entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-300">On deleting principal/parent</span></span>             | <span data-ttu-id="8d417-301">Impacto al interrumpir la relación con la entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-301">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="8d417-302">Cascade</span><span class="sxs-lookup"><span data-stu-id="8d417-302">Cascade</span></span>           | <span data-ttu-id="8d417-303">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-303">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="8d417-304">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-304">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="8d417-305">Restringir</span><span class="sxs-lookup"><span data-stu-id="8d417-305">Restrict</span></span>          | <span data-ttu-id="8d417-306">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-306">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="8d417-307">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-307">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="8d417-308">NoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-308">NoAction</span></span>          | <span data-ttu-id="8d417-309">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-309">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="8d417-310">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-310">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="8d417-311">SetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-311">SetNull</span></span>           | <span data-ttu-id="8d417-312">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-312">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="8d417-313">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-313">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="8d417-314">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-314">ClientSetNull</span></span>     | <span data-ttu-id="8d417-315">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-315">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="8d417-316">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-316">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="8d417-317">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="8d417-317">ClientCascade</span></span>     | <span data-ttu-id="8d417-318">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-318">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="8d417-319">EF Core elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-319">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="8d417-320">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-320">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="8d417-321">EF Core establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-321">Dependent FKs set to null by EF Core</span></span>

<span data-ttu-id="8d417-322">Notas:</span><span class="sxs-lookup"><span data-stu-id="8d417-322">Notes:</span></span>

- <span data-ttu-id="8d417-323">El valor predeterminado para las relaciones opcionales como esta es `ClientSetNull`.</span><span class="sxs-lookup"><span data-stu-id="8d417-323">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="8d417-324">Las entidades dependientes o secundarias nunca se eliminan, a menos que se configuren los comportamientos `Cascade` o `ClientCascade`.</span><span class="sxs-lookup"><span data-stu-id="8d417-324">Dependents/children are never deleted unless `Cascade` or `ClientCascade` are configured.</span></span>
- <span data-ttu-id="8d417-325">El resto de valores hacen que EF Core establezca las claves externas de las entidades dependientes en NULL, excepto el siguiente valor:</span><span class="sxs-lookup"><span data-stu-id="8d417-325">All other values cause the dependent FKs to be set to null by EF Core...</span></span>
  - <span data-ttu-id="8d417-326">`ClientNoAction` indica a EF Core que no toque las claves externas de las entidades dependientes o secundarias cuando se elimina la entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8d417-326">...except `ClientNoAction` which tells EF Core not to touch the foreign keys of dependents/children when the principal/parent is deleted.</span></span> <span data-ttu-id="8d417-327">En este caso, la base de datos produce una excepción, que SaveChanges encapsula como `DbUpdateException`.</span><span class="sxs-lookup"><span data-stu-id="8d417-327">The database therefore throws an exception, which is wrapped as a `DbUpdateException` by SaveChanges.</span></span>

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="8d417-328">Relación opcional con las entidades dependientes o secundarias no cargadas</span><span class="sxs-lookup"><span data-stu-id="8d417-328">Optional relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="8d417-329">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="8d417-329">DeleteBehavior</span></span>    | <span data-ttu-id="8d417-330">Impacto al eliminar una entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-330">On deleting principal/parent</span></span>             | <span data-ttu-id="8d417-331">Impacto al interrumpir la relación con la entidad de seguridad o primaria</span><span class="sxs-lookup"><span data-stu-id="8d417-331">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="8d417-332">Cascade</span><span class="sxs-lookup"><span data-stu-id="8d417-332">Cascade</span></span>           | <span data-ttu-id="8d417-333">La base de datos elimina las entidades dependientes</span><span class="sxs-lookup"><span data-stu-id="8d417-333">Dependents deleted by database</span></span>           | <span data-ttu-id="8d417-334">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-334">N/A</span></span>
| <span data-ttu-id="8d417-335">Restringir</span><span class="sxs-lookup"><span data-stu-id="8d417-335">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="8d417-336">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-336">N/A</span></span>
| <span data-ttu-id="8d417-337">NoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-337">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="8d417-338">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-338">N/A</span></span>
| <span data-ttu-id="8d417-339">SetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-339">SetNull</span></span>           | <span data-ttu-id="8d417-340">La base de datos establece las CE de las entidades dependientes en NULL</span><span class="sxs-lookup"><span data-stu-id="8d417-340">Dependent FKs set to null by database</span></span>    | <span data-ttu-id="8d417-341">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-341">N/A</span></span>
| <span data-ttu-id="8d417-342">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="8d417-342">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="8d417-343">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-343">N/A</span></span>
| <span data-ttu-id="8d417-344">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="8d417-344">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="8d417-345">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-345">N/A</span></span>
| <span data-ttu-id="8d417-346">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="8d417-346">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="8d417-347">N/D</span><span class="sxs-lookup"><span data-stu-id="8d417-347">N/A</span></span>

<span data-ttu-id="8d417-348">Notas:</span><span class="sxs-lookup"><span data-stu-id="8d417-348">Notes:</span></span>

- <span data-ttu-id="8d417-349">No es posible interrumpir la relación en este caso, ya que las entidades dependientes o secundarias no están cargadas.</span><span class="sxs-lookup"><span data-stu-id="8d417-349">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="8d417-350">El valor predeterminado para las relaciones opcionales como esta es `ClientSetNull`.</span><span class="sxs-lookup"><span data-stu-id="8d417-350">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="8d417-351">Las entidades dependientes o secundarias se deben cargar para evitar una excepción de base de datos, a menos que la base de datos se haya configurado para realizar eliminaciones en cascada o para establecer valores NULL en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d417-351">Dependents/children must be loaded to avoid a database exception unless the database has been configured to cascade either deletes or nulls.</span></span>
