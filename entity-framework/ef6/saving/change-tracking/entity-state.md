---
title: 'Trabajar con Estados de entidad: EF6'
description: Trabajar con Estados de entidad en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 981bdbca982403338f3f65a41f601641d59d74d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619981"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="9266f-103">Trabajar con Estados de entidad</span><span class="sxs-lookup"><span data-stu-id="9266f-103">Working with entity states</span></span>
<span data-ttu-id="9266f-104">En este tema se explica cómo agregar y adjuntar entidades a un contexto y cómo Entity Framework procesa estas durante el SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-104">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="9266f-105">Entity Framework se encarga del seguimiento del estado de las entidades mientras están conectadas a un contexto, pero en escenarios desconectados o de N niveles, puede permitir que EF sepa en qué estado deben estar las entidades.</span><span class="sxs-lookup"><span data-stu-id="9266f-105">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="9266f-106">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="9266f-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="9266f-107">Estados de la entidad y SaveChanges</span><span class="sxs-lookup"><span data-stu-id="9266f-107">Entity states and SaveChanges</span></span>

<span data-ttu-id="9266f-108">Una entidad puede estar en uno de cinco Estados, tal y como se define en la enumeración EntityState.</span><span class="sxs-lookup"><span data-stu-id="9266f-108">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="9266f-109">Estos estados son:</span><span class="sxs-lookup"><span data-stu-id="9266f-109">These states are:</span></span>  

- <span data-ttu-id="9266f-110">Agregado: el contexto está realizando el seguimiento de la entidad, pero aún no existe en la base de datos</span><span class="sxs-lookup"><span data-stu-id="9266f-110">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="9266f-111">Unchanged: el contexto realiza un seguimiento de la entidad y existe en la base de datos, y sus valores de propiedad no han cambiado con respecto a los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9266f-111">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="9266f-112">Modificado: el contexto está realizando el seguimiento de la entidad y existe en la base de datos, y se han modificado algunos o todos sus valores de propiedad.</span><span class="sxs-lookup"><span data-stu-id="9266f-112">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="9266f-113">Eliminado: el contexto realiza un seguimiento de la entidad y existe en la base de datos, pero se ha marcado para su eliminación de la base de datos la próxima vez que se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-113">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="9266f-114">Detached: el contexto no está realizando el seguimiento de la entidad</span><span class="sxs-lookup"><span data-stu-id="9266f-114">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="9266f-115">SaveChanges realiza diferentes acciones para entidades en diferentes Estados:</span><span class="sxs-lookup"><span data-stu-id="9266f-115">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="9266f-116">SaveChanges no toca las entidades sin modificar.</span><span class="sxs-lookup"><span data-stu-id="9266f-116">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="9266f-117">Las actualizaciones no se envían a la base de datos para entidades en el estado sin cambios.</span><span class="sxs-lookup"><span data-stu-id="9266f-117">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="9266f-118">Las entidades agregadas se insertan en la base de datos y, a continuación, se vuelven sin cambios cuando se devuelve SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-118">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="9266f-119">Las entidades modificadas se actualizan en la base de datos y, a continuación, se vuelven sin cambios cuando se devuelve SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-119">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="9266f-120">Las entidades eliminadas se eliminan de la base de datos y, a continuación, se desasocian del contexto.</span><span class="sxs-lookup"><span data-stu-id="9266f-120">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="9266f-121">En los ejemplos siguientes se muestran las maneras en las que se puede cambiar el estado de una entidad o un gráfico de entidades.</span><span class="sxs-lookup"><span data-stu-id="9266f-121">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="9266f-122">Agregar una nueva entidad al contexto</span><span class="sxs-lookup"><span data-stu-id="9266f-122">Adding a new entity to the context</span></span>  

<span data-ttu-id="9266f-123">Se puede Agregar una nueva entidad al contexto llamando al método Add en DbSet.</span><span class="sxs-lookup"><span data-stu-id="9266f-123">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="9266f-124">Esto coloca la entidad en el estado agregado, lo que significa que se insertará en la base de datos la próxima vez que se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-124">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="9266f-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-125">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="9266f-126">Otra manera de agregar una nueva entidad al contexto es cambiar su estado a agregado.</span><span class="sxs-lookup"><span data-stu-id="9266f-126">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="9266f-127">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="9266f-128">Por último, puede Agregar una nueva entidad al contexto al enlazarla a otra entidad de la que ya se está realizando el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="9266f-128">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="9266f-129">Esto podría ser agregando la nueva entidad a la propiedad de navegación de colección de otra entidad o estableciendo una propiedad de navegación de referencia de otra entidad para que apunte a la nueva entidad.</span><span class="sxs-lookup"><span data-stu-id="9266f-129">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="9266f-130">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-130">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="9266f-131">Tenga en cuenta que, para todos estos ejemplos, si la entidad que se va a agregar tiene referencias a otras entidades de las que todavía no se ha realizado un seguimiento, estas nuevas entidades también se agregarán al contexto y se insertarán en la base de datos la próxima vez que se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-131">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="9266f-132">Asociar una entidad existente al contexto</span><span class="sxs-lookup"><span data-stu-id="9266f-132">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="9266f-133">Si tiene una entidad que sabe que ya existe en la base de datos pero en la que no se realiza el seguimiento actualmente por el contexto, puede indicar al contexto que realice el seguimiento de la entidad mediante el método Attach en DbSet.</span><span class="sxs-lookup"><span data-stu-id="9266f-133">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="9266f-134">La entidad estará en el estado Unchanged en el contexto.</span><span class="sxs-lookup"><span data-stu-id="9266f-134">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="9266f-135">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-135">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9266f-136">Tenga en cuenta que no se realizarán cambios en la base de datos si se llama a SaveChanges sin realizar ninguna otra manipulación de la entidad adjunta.</span><span class="sxs-lookup"><span data-stu-id="9266f-136">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="9266f-137">Esto se debe a que la entidad está en el estado Unchanged.</span><span class="sxs-lookup"><span data-stu-id="9266f-137">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="9266f-138">Otra manera de asociar una entidad existente al contexto es cambiar su estado a sin cambios.</span><span class="sxs-lookup"><span data-stu-id="9266f-138">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="9266f-139">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-139">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9266f-140">Tenga en cuenta que para ambos ejemplos, si la entidad que se va a asociar tiene referencias a otras entidades de las que todavía no se ha realizado un seguimiento, estas nuevas entidades también se adjuntarán al contexto en el estado sin cambios.</span><span class="sxs-lookup"><span data-stu-id="9266f-140">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="9266f-141">Adjuntar una entidad existente pero modificada al contexto</span><span class="sxs-lookup"><span data-stu-id="9266f-141">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="9266f-142">Si tiene una entidad que sabe que ya existe en la base de datos, pero en la que se pueden realizar cambios, puede indicar al contexto que adjunte la entidad y establezca su estado en modificado.</span><span class="sxs-lookup"><span data-stu-id="9266f-142">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="9266f-143">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-143">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9266f-144">Al cambiar el estado a modificado, todas las propiedades de la entidad se marcarán como modificadas y todos los valores de propiedad se enviarán a la base de datos cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-144">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="9266f-145">Tenga en cuenta que si la entidad que se va a asociar tiene referencias a otras entidades de las que todavía no se ha realizado un seguimiento, estas nuevas entidades se adjuntarán al contexto en el estado Unchanged (no se modificarán automáticamente).</span><span class="sxs-lookup"><span data-stu-id="9266f-145">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="9266f-146">Si tiene varias entidades que deben marcarse como modificadas, debe establecer el estado de cada una de estas entidades de forma individual.</span><span class="sxs-lookup"><span data-stu-id="9266f-146">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="9266f-147">Cambiar el estado de una entidad de la que se ha realizado un seguimiento</span><span class="sxs-lookup"><span data-stu-id="9266f-147">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="9266f-148">Puede cambiar el estado de una entidad de la que ya se realiza el seguimiento estableciendo la propiedad State en su entrada.</span><span class="sxs-lookup"><span data-stu-id="9266f-148">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="9266f-149">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-149">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="9266f-150">Tenga en cuenta que la llamada a Add o attach para una entidad de la que ya se ha realizado un seguimiento también se puede usar para cambiar el estado de la entidad.</span><span class="sxs-lookup"><span data-stu-id="9266f-150">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="9266f-151">Por ejemplo, al llamar a attach para una entidad que está actualmente en el estado agregado, se cambiará su estado a sin cambios.</span><span class="sxs-lookup"><span data-stu-id="9266f-151">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="9266f-152">Insertar o actualizar patrón</span><span class="sxs-lookup"><span data-stu-id="9266f-152">Insert or update pattern</span></span>  

<span data-ttu-id="9266f-153">Un patrón común para algunas aplicaciones es agregar una entidad como nueva (lo que da como resultado una inserción de base de datos) o adjuntar una entidad como existente y marcarla como modificada (lo que da como resultado una actualización de la base de datos) en función del valor de la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9266f-153">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="9266f-154">Por ejemplo, al utilizar las claves principales de enteros generados por la base de datos, es habitual tratar una entidad con una clave cero como nueva y una entidad con una clave distinta de cero como existente.</span><span class="sxs-lookup"><span data-stu-id="9266f-154">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="9266f-155">Este patrón se puede lograr estableciendo el estado de la entidad en función de una comprobación del valor de la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9266f-155">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="9266f-156">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9266f-156">For example:</span></span>  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

<span data-ttu-id="9266f-157">Tenga en cuenta que al cambiar el estado a modificado, todas las propiedades de la entidad se marcarán como modificadas y todos los valores de propiedad se enviarán a la base de datos cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9266f-157">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
