---
title: 'Herramienta de seguimiento de cambios: EF Core'
description: Información general sobre el seguimiento de cambios para EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 52223e5472b09271d19ac9449a3989b4a0e277f7
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129525"
---
# <a name="change-tracking-in-ef-core"></a><span data-ttu-id="ecccc-103">Herramienta de seguimiento de cambios en EF Core</span><span class="sxs-lookup"><span data-stu-id="ecccc-103">Change Tracking in EF Core</span></span>

<span data-ttu-id="ecccc-104">Cada instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> realiza un seguimiento de los cambios realizados en las entidades.</span><span class="sxs-lookup"><span data-stu-id="ecccc-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="ecccc-105">Estas entidades de las que se realiza un seguimiento, a su vez, impulsan los cambios en la base de datos cuando se llama a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span><span class="sxs-lookup"><span data-stu-id="ecccc-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="ecccc-106">En este documento se presenta información general sobre el seguimiento de cambios de Entity Framework Core (EF Core) y cómo se relaciona con las consultas y actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="ecccc-106">This document presents an overview of Entity Framework Core (EF Core) change tracking and how it relates to queries and updates.</span></span>

> [!TIP]
> <span data-ttu-id="ecccc-107">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="ecccc-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="ecccc-108">Para simplificar, este documento utiliza métodos sincrónicos como <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, y hace referencia a ellos, en lugar de sus equivalentes asincrónicos como <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="ecccc-108">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="ecccc-109">La llamada al método asincrónico y su espera pueden sustituirse a menos que se indique lo contrario.</span><span class="sxs-lookup"><span data-stu-id="ecccc-109">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="how-to-track-entities"></a><span data-ttu-id="ecccc-110">Seguimiento de entidades</span><span class="sxs-lookup"><span data-stu-id="ecccc-110">How to track entities</span></span>

<span data-ttu-id="ecccc-111">Se realiza un seguimiento de las instancias de entidad cuando se dan estos casos:</span><span class="sxs-lookup"><span data-stu-id="ecccc-111">Entity instances become tracked when they are:</span></span>

- <span data-ttu-id="ecccc-112">Se devuelven de una consulta ejecutada en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-112">Returned from a query executed against the database</span></span>
- <span data-ttu-id="ecccc-113">Se asocian explícitamente a DbContext por `Add`, `Attach`, `Update`o métodos similares.</span><span class="sxs-lookup"><span data-stu-id="ecccc-113">Explicitly attached to the DbContext by `Add`, `Attach`, `Update`, or similar methods</span></span>
- <span data-ttu-id="ecccc-114">Se detecta como nuevas entidades conectadas a las entidades sometidas a seguimiento existentes</span><span class="sxs-lookup"><span data-stu-id="ecccc-114">Detected as new entities connected to existing tracked entities</span></span>

<span data-ttu-id="ecccc-115">El seguimiento de las instancias de entidad deja de realizarse cuando se dan estos casos:</span><span class="sxs-lookup"><span data-stu-id="ecccc-115">Entity instances are no longer tracked when:</span></span>

- <span data-ttu-id="ecccc-116">DbContext se desecha.</span><span class="sxs-lookup"><span data-stu-id="ecccc-116">The DbContext is disposed</span></span>
- <span data-ttu-id="ecccc-117">La herramienta de seguimiento de cambios está desactivada (EF Core 5.0 y versiones posteriores).</span><span class="sxs-lookup"><span data-stu-id="ecccc-117">The change tracker is cleared (EF Core 5.0 and later)</span></span>
- <span data-ttu-id="ecccc-118">Las entidades se desasocian explícitamente.</span><span class="sxs-lookup"><span data-stu-id="ecccc-118">The entities are explicitly detached</span></span>

<span data-ttu-id="ecccc-119">DbContext está diseñado para representar una unidad de trabajo de corta duración, como se describe en [Inicialización y configuración de DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ecccc-119">DbContext is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="ecccc-120">Esto significa que desechar DbContext es _la forma normal_ detener el seguimiento de las entidades.</span><span class="sxs-lookup"><span data-stu-id="ecccc-120">This means that disposing the DbContext is _the normal way_ to stop tracking entities.</span></span> <span data-ttu-id="ecccc-121">En otras palabras, la vigencia de DbContext debe pasar por estos pasos:</span><span class="sxs-lookup"><span data-stu-id="ecccc-121">In other words, the lifetime of a DbContext should be:</span></span>

1. <span data-ttu-id="ecccc-122">Creación de la instancia de DbContext</span><span class="sxs-lookup"><span data-stu-id="ecccc-122">Create the DbContext instance</span></span>
2. <span data-ttu-id="ecccc-123">Seguimiento de algunas entidades</span><span class="sxs-lookup"><span data-stu-id="ecccc-123">Track some entities</span></span>
3. <span data-ttu-id="ecccc-124">Realización de algunos cambios en las entidades</span><span class="sxs-lookup"><span data-stu-id="ecccc-124">Make some changes to the entities</span></span>
4. <span data-ttu-id="ecccc-125">Llamada a SaveChanges para actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="ecccc-125">Call SaveChanges to update the database</span></span>
5. <span data-ttu-id="ecccc-126">Eliminación de la instancia de DbContext</span><span class="sxs-lookup"><span data-stu-id="ecccc-126">Dispose the DbContext instance</span></span>

> [!TIP]
> <span data-ttu-id="ecccc-127">No es necesario borrar la herramienta de seguimiento de cambios ni desasociar explícitamente las instancias de la entidad al adoptar este enfoque.</span><span class="sxs-lookup"><span data-stu-id="ecccc-127">It is not necessary to clear the change tracker or explicitly detach entity instances when taking this approach.</span></span> <span data-ttu-id="ecccc-128">Sin embargo, si necesita desasociar entidades, llamar a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> es más eficaz que separar las entidades una por una.</span><span class="sxs-lookup"><span data-stu-id="ecccc-128">However, if you do need to detach entities, then calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> is more efficient than detaching entities one-by-one.</span></span>

## <a name="entity-states"></a><span data-ttu-id="ecccc-129">Estados de entidad</span><span class="sxs-lookup"><span data-stu-id="ecccc-129">Entity states</span></span>

<span data-ttu-id="ecccc-130">Cada entidad está asociada a un elemento <xref:Microsoft.EntityFrameworkCore.EntityState>determinado:</span><span class="sxs-lookup"><span data-stu-id="ecccc-130">Every entity is is associated with a given <xref:Microsoft.EntityFrameworkCore.EntityState>:</span></span>

- <span data-ttu-id="ecccc-131"><xref:Microsoft.EntityFrameworkCore.DbContext> ya no realiza el seguimiento de las entidades `Detached`.</span><span class="sxs-lookup"><span data-stu-id="ecccc-131">`Detached` entities are not being tracked by the <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
- <span data-ttu-id="ecccc-132">Las entidades `Added` son nuevas y aún no se han insertado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-132">`Added` entities are new and have not yet been inserted into the database.</span></span> <span data-ttu-id="ecccc-133">Esto significa que se insertarán cuando se llame a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span><span class="sxs-lookup"><span data-stu-id="ecccc-133">This means they will be inserted when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>
- <span data-ttu-id="ecccc-134">Las entidades`Unchanged` _no_ han cambiado desde que se consultaron desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-134">`Unchanged` entities have _not_ been changed since they were queried from the database.</span></span> <span data-ttu-id="ecccc-135">Todas las entidades devueltas de las consultas se encuentran inicialmente en este estado.</span><span class="sxs-lookup"><span data-stu-id="ecccc-135">All entities returned from queries are initially in this state.</span></span>
- <span data-ttu-id="ecccc-136">Las entidades `Modified` han cambiado desde que se consultaron desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-136">`Modified` entities have been changed since they were queried from the database.</span></span> <span data-ttu-id="ecccc-137">Esto significa que se actualizarán cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="ecccc-137">This means they will be updated when SaveChanges is called.</span></span>
- <span data-ttu-id="ecccc-138">Existen entidades `Deleted` en la base de datos, pero se marcan para eliminarse cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="ecccc-138">`Deleted` entities exist in the database, but are marked to be deleted when SaveChanges is called.</span></span>

<span data-ttu-id="ecccc-139">EF Core realiza un seguimiento de los cambios en el nivel de propiedad.</span><span class="sxs-lookup"><span data-stu-id="ecccc-139">EF Core tracks changes at the property level.</span></span> <span data-ttu-id="ecccc-140">Por ejemplo, si solo se modifica un valor de propiedad único, una actualización de base de datos solo cambiará ese valor.</span><span class="sxs-lookup"><span data-stu-id="ecccc-140">For example, if only a single property value is modified, then a database update will change only that value.</span></span> <span data-ttu-id="ecccc-141">Sin embargo, las propiedades solo se pueden marcar como modificadas cuando la propia entidad está en el estado de modificación.</span><span class="sxs-lookup"><span data-stu-id="ecccc-141">However, properties can only be marked as modified when the entity itself is in the Modified state.</span></span> <span data-ttu-id="ecccc-142">(O, desde una perspectiva alternativa, el estado de modificación significa que al menos un valor de propiedad se ha marcado como modificado).</span><span class="sxs-lookup"><span data-stu-id="ecccc-142">(Or, from an alternate perspective, the Modified state means that at least one property value has been marked as modified.)</span></span>

<span data-ttu-id="ecccc-143">En la tabla siguiente se resumen los diferentes estados:</span><span class="sxs-lookup"><span data-stu-id="ecccc-143">The following table summarizes the different states:</span></span>

| <span data-ttu-id="ecccc-144">Estado de la entidad</span><span class="sxs-lookup"><span data-stu-id="ecccc-144">Entity state</span></span>     | <span data-ttu-id="ecccc-145">Seguido por DbContext</span><span class="sxs-lookup"><span data-stu-id="ecccc-145">Tracked by DbContext</span></span> | <span data-ttu-id="ecccc-146">Existe en la base de datos</span><span class="sxs-lookup"><span data-stu-id="ecccc-146">Exists in database</span></span> | <span data-ttu-id="ecccc-147">Propiedades modificadas</span><span class="sxs-lookup"><span data-stu-id="ecccc-147">Properties modified</span></span> | <span data-ttu-id="ecccc-148">Acción en SaveChanges</span><span class="sxs-lookup"><span data-stu-id="ecccc-148">Action on SaveChanges</span></span>
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | <span data-ttu-id="ecccc-149">No</span><span class="sxs-lookup"><span data-stu-id="ecccc-149">No</span></span>                   | -                  | -                   | -
| `Added`          | <span data-ttu-id="ecccc-150">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-150">Yes</span></span>                  | <span data-ttu-id="ecccc-151">No</span><span class="sxs-lookup"><span data-stu-id="ecccc-151">No</span></span>                 | -                   | <span data-ttu-id="ecccc-152">Insertar</span><span class="sxs-lookup"><span data-stu-id="ecccc-152">Insert</span></span>
| `Unchanged`      | <span data-ttu-id="ecccc-153">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-153">Yes</span></span>                  | <span data-ttu-id="ecccc-154">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-154">Yes</span></span>                | <span data-ttu-id="ecccc-155">No</span><span class="sxs-lookup"><span data-stu-id="ecccc-155">No</span></span>                  | -
| `Modified`       | <span data-ttu-id="ecccc-156">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-156">Yes</span></span>                  | <span data-ttu-id="ecccc-157">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-157">Yes</span></span>                | <span data-ttu-id="ecccc-158">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-158">Yes</span></span>                 | <span data-ttu-id="ecccc-159">Actualizar</span><span class="sxs-lookup"><span data-stu-id="ecccc-159">Update</span></span>
| `Deleted`        | <span data-ttu-id="ecccc-160">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-160">Yes</span></span>                  | <span data-ttu-id="ecccc-161">Sí</span><span class="sxs-lookup"><span data-stu-id="ecccc-161">Yes</span></span>                | -                   | <span data-ttu-id="ecccc-162">Eliminar</span><span class="sxs-lookup"><span data-stu-id="ecccc-162">Delete</span></span>

> [!NOTE]
> <span data-ttu-id="ecccc-163">Este texto utiliza términos de base de datos relacional para mayor claridad.</span><span class="sxs-lookup"><span data-stu-id="ecccc-163">This text uses relational database terms for clarity.</span></span> <span data-ttu-id="ecccc-164">Las bases de datos NoSQL suelen admitir operaciones similares pero posiblemente con nombres diferentes.</span><span class="sxs-lookup"><span data-stu-id="ecccc-164">NoSQL databases typically support similar operations but possibly with different names.</span></span> <span data-ttu-id="ecccc-165">Consulte la documentación del proveedor de bases de datos para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="ecccc-165">Consult your database provider documentation for more information.</span></span>

## <a name="tracking-from-queries"></a><span data-ttu-id="ecccc-166">Seguimiento de consultas</span><span class="sxs-lookup"><span data-stu-id="ecccc-166">Tracking from queries</span></span>

<span data-ttu-id="ecccc-167">El seguimiento de cambios de EF Core funciona mejor cuando se utiliza la misma instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> para consultar las entidades y actualizarlas mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span><span class="sxs-lookup"><span data-stu-id="ecccc-167">EF Core change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="ecccc-168">Esto se debe a que EF Core realiza un seguimiento automático del estado de las entidades consultadas y, a continuación, detecta los cambios realizados en estas entidades cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="ecccc-168">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span>

<span data-ttu-id="ecccc-169">Este enfoque tiene varias ventajas con respecto a [realizar un seguimiento explícito de las instancias de entidad](xref:core/change-tracking/explicit-tracking):</span><span class="sxs-lookup"><span data-stu-id="ecccc-169">This approach has several advantages over [explicitly tracking entity instances](xref:core/change-tracking/explicit-tracking):</span></span>

- <span data-ttu-id="ecccc-170">Es sencilla.</span><span class="sxs-lookup"><span data-stu-id="ecccc-170">It is simple.</span></span> <span data-ttu-id="ecccc-171">Los estados de la entidad rara vez tienen que manipularse explícitamente: EF Core se encarga de los cambios de estado.</span><span class="sxs-lookup"><span data-stu-id="ecccc-171">Entity states rarely need to be manipulated explicitly--EF Core takes care of state changes.</span></span>
- <span data-ttu-id="ecccc-172">Las actualizaciones solo se limitan a aquellos valores que realmente han cambiado.</span><span class="sxs-lookup"><span data-stu-id="ecccc-172">Updates are limited to only those values that have actually changed.</span></span>
- <span data-ttu-id="ecccc-173">Los valores de las [propiedades reemplazadas](xref:core/modeling/shadow-properties) se conservan y se usan según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="ecccc-173">The values of [shadow properties](xref:core/modeling/shadow-properties) are preserved and used as needed.</span></span> <span data-ttu-id="ecccc-174">Esto es especialmente importante cuando las claves externas se almacenan en el estado reemplazado.</span><span class="sxs-lookup"><span data-stu-id="ecccc-174">This is especially relevant when foreign keys are stored in shadow state.</span></span>
- <span data-ttu-id="ecccc-175">Los valores originales de las propiedades se conservan automáticamente y se usan para mejorar la eficacia de las actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="ecccc-175">The original values of properties are preserved automatically and used for efficient updates.</span></span>

## <a name="simple-query-and-update"></a><span data-ttu-id="ecccc-176">Consulta y actualización simples</span><span class="sxs-lookup"><span data-stu-id="ecccc-176">Simple query and update</span></span>

<span data-ttu-id="ecccc-177">Por ejemplo, considere un modelo de blog o publicaciones sencillo:</span><span class="sxs-lookup"><span data-stu-id="ecccc-177">For example, consider a simple blog/posts model:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="ecccc-178">Podemos usar este modelo para consultar blogs y publicaciones y luego realizar algunas actualizaciones en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="ecccc-178">We can use this model to query for blogs and posts and then make some updates to the database:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

<span data-ttu-id="ecccc-179">La llamada a SaveChanges da como resultado las siguientes actualizaciones de base de datos, con SQLite como base de datos de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ecccc-179">Calling SaveChanges results in the following database updates, using SQLite as an example database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

<span data-ttu-id="ecccc-180">La vista de depuración de la [herramienta de seguimiento de cambios](xref:core/change-tracking/debug-views) es una excelente manera de visualizar las entidades de las que se realiza el seguimiento y cuáles son sus estados.</span><span class="sxs-lookup"><span data-stu-id="ecccc-180">The [change tracker debug view](xref:core/change-tracking/debug-views) is a great way visualize which entities are being tracked and what their states are.</span></span> <span data-ttu-id="ecccc-181">Por ejemplo, la inserción del código siguiente en el ejemplo anterior antes de llamar a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="ecccc-181">For example, inserting the following code into the sample above before calling SaveChanges:</span></span>

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

<span data-ttu-id="ecccc-182">Se genera el siguiente código resultado:</span><span class="sxs-lookup"><span data-stu-id="ecccc-182">Generates the following output:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="ecccc-183">Observe específicamente:</span><span class="sxs-lookup"><span data-stu-id="ecccc-183">Notice specifically:</span></span>

- <span data-ttu-id="ecccc-184">La propiedad `Blog.Name` se marca como modificada (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`) y esto hace que el blog esté en el estado `Modified`.</span><span class="sxs-lookup"><span data-stu-id="ecccc-184">The `Blog.Name` property is marked as modified (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), and this results in the blog being in the `Modified` state.</span></span>
- <span data-ttu-id="ecccc-185">La propiedad `Post.Title` de la publicación 2 se marca como modificada (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`) y esto hace que esta publicación esté en el estado `Modified`.</span><span class="sxs-lookup"><span data-stu-id="ecccc-185">The `Post.Title` property of post 2 is marked as modified (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), and this results in this post being in the `Modified` state.</span></span>
- <span data-ttu-id="ecccc-186">Los demás valores de propiedad de la publicación 2 no han cambiado y, por lo tanto, no se marcan como modificados.</span><span class="sxs-lookup"><span data-stu-id="ecccc-186">The other property values of post 2 have not changed and are therefore not marked as modified.</span></span> <span data-ttu-id="ecccc-187">Este es el motivo por el cual estos valores no se incluyen en la actualización de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-187">This is why these values are not included in the database update.</span></span>
- <span data-ttu-id="ecccc-188">La otra publicación no se modificó de ningún modo.</span><span class="sxs-lookup"><span data-stu-id="ecccc-188">The other post was not modified in any way.</span></span> <span data-ttu-id="ecccc-189">Esta es la razón por la que todavía está en el estado `Unchanged` y no se incluye en la actualización de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-189">This is why it is still in the `Unchanged` state and is not included in the database update.</span></span>

## <a name="query-then-insert-update-and-delete"></a><span data-ttu-id="ecccc-190">Consulta y luego inserción, actualización y eliminación</span><span class="sxs-lookup"><span data-stu-id="ecccc-190">Query then insert, update, and delete</span></span>

<span data-ttu-id="ecccc-191">Las actualizaciones como las del ejemplo anterior se pueden combinar con inserciones y eliminaciones en la misma unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="ecccc-191">Updates like those in the previous example can be combined with inserts and deletes in the same unit-of-work.</span></span> <span data-ttu-id="ecccc-192">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ecccc-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

<span data-ttu-id="ecccc-193">En este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ecccc-193">In this example:</span></span>

- <span data-ttu-id="ecccc-194">Se consultan un blog y publicaciones relacionadas desde la base de datos y se realiza su seguimiento.</span><span class="sxs-lookup"><span data-stu-id="ecccc-194">A blog and related posts are queried from the database and tracked</span></span>
- <span data-ttu-id="ecccc-195">Se cambia la propiedad `Blog.Name`.</span><span class="sxs-lookup"><span data-stu-id="ecccc-195">The `Blog.Name` property is changed</span></span>
- <span data-ttu-id="ecccc-196">Se agrega una nueva publicación a la colección de publicaciones existentes para el blog.</span><span class="sxs-lookup"><span data-stu-id="ecccc-196">A new post is added to the collection of existing posts for the blog</span></span>
- <span data-ttu-id="ecccc-197">Una publicación existente se marca para su eliminación mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ecccc-197">An existing post is marked for deletion by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="ecccc-198">Al mirar de nuevo en la [vista de depuración de la herramienta de seguimiento de cambios](xref:core/change-tracking/debug-views) antes de llamar a SaveChanges se muestra cómo EF Core está realizando el seguimiento de estos cambios:</span><span class="sxs-lookup"><span data-stu-id="ecccc-198">Looking again at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows how EF Core is tracking these changes:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
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
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="ecccc-199">Tenga en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecccc-199">Notice that:</span></span>

- <span data-ttu-id="ecccc-200">El blog se marca como `Modified`.</span><span class="sxs-lookup"><span data-stu-id="ecccc-200">The blog is marked as `Modified`.</span></span> <span data-ttu-id="ecccc-201">Se generará una actualización de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-201">This will generate a database update.</span></span>
- <span data-ttu-id="ecccc-202">La publicación 2 se marca como `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="ecccc-202">Post 2 is marked as `Deleted`.</span></span> <span data-ttu-id="ecccc-203">Se generará una eliminación de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-203">This will generate a database delete.</span></span>
- <span data-ttu-id="ecccc-204">Una nueva publicación con un identificador temporal se asocia al blog 1 y se marca como `Added`.</span><span class="sxs-lookup"><span data-stu-id="ecccc-204">A new post with a temporary ID is associated with blog 1 and is marked as `Added`.</span></span> <span data-ttu-id="ecccc-205">Se generará una inserción de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-205">This will generate a database insert.</span></span>

<span data-ttu-id="ecccc-206">Esto da como resultado los siguientes comandos de base de datos (con SQLite) cuando se llama a SaveChanges:</span><span class="sxs-lookup"><span data-stu-id="ecccc-206">This results in the following database commands (using SQLite) when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="ecccc-207">Vea [Seguimiento explícito de las entidades](xref:core/change-tracking/explicit-tracking) para obtener más información sobre cómo insertar y eliminar entidades.</span><span class="sxs-lookup"><span data-stu-id="ecccc-207">See [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking) for more information on inserting and deleting entities.</span></span> <span data-ttu-id="ecccc-208">Consulte [Detección y notificaciones de cambios](xref:core/change-tracking/change-detection) para obtener más información sobre cómo EF Core detecta automáticamente los cambios como este.</span><span class="sxs-lookup"><span data-stu-id="ecccc-208">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information on how EF Core automatically detects changes like this.</span></span>

> [!TIP]
> <span data-ttu-id="ecccc-209">Llame a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> para determinar si se han realizado cambios que provocarán que SaveChanges realice actualizaciones en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecccc-209">Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> to determine whether any changes have been made that will cause SaveChanges to make updates to the database.</span></span> <span data-ttu-id="ecccc-210">Si HasChanges devuelve false, SaveChanges será una operación inefectiva.</span><span class="sxs-lookup"><span data-stu-id="ecccc-210">If HasChanges return false, then SaveChanges will be a no-op.</span></span>
