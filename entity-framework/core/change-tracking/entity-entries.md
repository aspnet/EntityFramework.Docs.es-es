---
title: 'Acceso a entidades de las que se ha realizado un seguimiento: EF Core'
description: Usar EntityEntry, DbContext. Entries y DbSet. local para tener acceso a las entidades sometidas a seguimiento
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129641"
---
# <a name="accessing-tracked-entities"></a><span data-ttu-id="aadd1-103">Acceso a entidades sometidas a seguimiento</span><span class="sxs-lookup"><span data-stu-id="aadd1-103">Accessing Tracked Entities</span></span>

<span data-ttu-id="aadd1-104">Hay cuatro API principales para tener acceso a las entidades a las que realiza un seguimiento <xref:Microsoft.EntityFrameworkCore.DbContext> :</span><span class="sxs-lookup"><span data-stu-id="aadd1-104">There are four main APIs for accessing entities tracked by a <xref:Microsoft.EntityFrameworkCore.DbContext>:</span></span>

- <span data-ttu-id="aadd1-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> Devuelve una <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instancia de para una instancia de entidad determinada.</span><span class="sxs-lookup"><span data-stu-id="aadd1-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> returns an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance for a given entity instance.</span></span>
- <span data-ttu-id="aadd1-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> Devuelve <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instancias para todas las entidades de las que se ha realizado un seguimiento o para todas las entidades de las que se ha realizado un seguimiento de un tipo determinado.</span><span class="sxs-lookup"><span data-stu-id="aadd1-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> returns <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances for all tracked entities, or for all tracked entities of a given type.</span></span>
- <span data-ttu-id="aadd1-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> y buscan <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> una sola entidad por clave principal, buscando primero las entidades de las que se realiza un seguimiento y, a continuación, consultando la base de datos si es necesario.</span><span class="sxs-lookup"><span data-stu-id="aadd1-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> find a single entity by primary key, first looking in tracked entities, and then querying the database if needed.</span></span>
- <span data-ttu-id="aadd1-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Devuelve las entidades reales (no las instancias de EntityEntry) para las entidades del tipo de entidad representadas por DbSet.</span><span class="sxs-lookup"><span data-stu-id="aadd1-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns actual entities (not EntityEntry instances) for entities of the entity type represented by the DbSet.</span></span>

<span data-ttu-id="aadd1-109">Cada una de ellas se describe con más detalle en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="aadd1-109">Each of these is described in more detail in the sections below.</span></span>

> [!TIP]
> <span data-ttu-id="aadd1-110">En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="aadd1-110">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="aadd1-111">Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.</span><span class="sxs-lookup"><span data-stu-id="aadd1-111">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="aadd1-112">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo desde GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span><span class="sxs-lookup"><span data-stu-id="aadd1-112">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span></span>

## <a name="using-dbcontextentry-and-entityentry-instances"></a><span data-ttu-id="aadd1-113">Usar instancias de DbContext. entry e EntityEntry</span><span class="sxs-lookup"><span data-stu-id="aadd1-113">Using DbContext.Entry and EntityEntry instances</span></span>

<span data-ttu-id="aadd1-114">Para cada entidad de la que se realiza un seguimiento, Entity Framework Core (EF Core) realiza un seguimiento de:</span><span class="sxs-lookup"><span data-stu-id="aadd1-114">For each tracked entity, Entity Framework Core (EF Core) keeps track of:</span></span>

- <span data-ttu-id="aadd1-115">El estado general de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-115">The overall state of the entity.</span></span> <span data-ttu-id="aadd1-116">Este es uno de `Unchanged` , `Modified` , `Added` o `Deleted` ; consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="aadd1-116">This is one of `Unchanged`, `Modified`, `Added`, or `Deleted`; see [Change Tracking in EF Core](xref:core/change-tracking/index) for more information.</span></span>
- <span data-ttu-id="aadd1-117">Las relaciones entre las entidades de las que se realiza un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="aadd1-117">The relationships between tracked entities.</span></span> <span data-ttu-id="aadd1-118">Por ejemplo, el blog al que pertenece un envío.</span><span class="sxs-lookup"><span data-stu-id="aadd1-118">For example, the blog to which a post belongs.</span></span>
- <span data-ttu-id="aadd1-119">"Valores actuales" de las propiedades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-119">The "current values" of properties.</span></span>
- <span data-ttu-id="aadd1-120">Los "valores originales" de las propiedades, cuando esta información está disponible.</span><span class="sxs-lookup"><span data-stu-id="aadd1-120">The "original values" of properties, when this information is available.</span></span> <span data-ttu-id="aadd1-121">Los valores originales son los valores de propiedad que existían al consultar la entidad desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-121">Original values are the property values that existed when entity was queried from the database.</span></span>
- <span data-ttu-id="aadd1-122">Los valores de propiedad que se han modificado desde que se consultaron.</span><span class="sxs-lookup"><span data-stu-id="aadd1-122">Which property values have been modified since they were queried.</span></span>
- <span data-ttu-id="aadd1-123">Otra información sobre los valores de propiedad, como si el valor es [temporal](xref:core/change-tracking/miscellaneous#temporary-values)o no.</span><span class="sxs-lookup"><span data-stu-id="aadd1-123">Other information about property values, such as whether or not the value is [temporary](xref:core/change-tracking/miscellaneous#temporary-values).</span></span>

<span data-ttu-id="aadd1-124">Al pasar una instancia de entidad a, se obtiene <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> un que <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> proporciona acceso a esta información para la entidad determinada.</span><span class="sxs-lookup"><span data-stu-id="aadd1-124">Passing an entity instance to <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> results in an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> providing access to this information for the given entity.</span></span> <span data-ttu-id="aadd1-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-125">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

<span data-ttu-id="aadd1-126">En las secciones siguientes se muestra cómo usar EntityEntry para tener acceso y manipular el estado de la entidad, así como el estado de las propiedades y las navegaciones de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-126">The following sections show how to use an EntityEntry to access and manipulate entity state, as well as the state of the entity's properties and navigations.</span></span>

### <a name="working-with-the-entity"></a><span data-ttu-id="aadd1-127">Trabajar con la entidad</span><span class="sxs-lookup"><span data-stu-id="aadd1-127">Working with the entity</span></span>

<span data-ttu-id="aadd1-128">El uso más común de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> es el acceso al actual <xref:Microsoft.EntityFrameworkCore.EntityState> de una entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-128">The most common use of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> is to access the current <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity.</span></span> <span data-ttu-id="aadd1-129">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-129">For example:</span></span>

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

<span data-ttu-id="aadd1-130">El método entry también se puede usar en entidades que todavía no se han controlado.</span><span class="sxs-lookup"><span data-stu-id="aadd1-130">The Entry method can also be used on entities that are not yet tracked.</span></span> <span data-ttu-id="aadd1-131">Esto _no inicia el seguimiento de la entidad_; el estado de la entidad sigue siendo `Detatched` .</span><span class="sxs-lookup"><span data-stu-id="aadd1-131">This _does not start tracking the entity_; the state of the entity is still `Detatched`.</span></span> <span data-ttu-id="aadd1-132">Sin embargo, el EntityEntry devuelto se puede usar para cambiar el estado de la entidad, momento en el que se realizará el seguimiento de la entidad en el estado especificado.</span><span class="sxs-lookup"><span data-stu-id="aadd1-132">However, the returned EntityEntry can then be used to change the entity state, at which point the entity will become tracked in the given state.</span></span> <span data-ttu-id="aadd1-133">Por ejemplo, el código siguiente comenzará el seguimiento de una instancia de blog como `Added` :</span><span class="sxs-lookup"><span data-stu-id="aadd1-133">For example, the following code will start tracking a Blog instance as `Added`:</span></span>

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> <span data-ttu-id="aadd1-134">A diferencia de EF6, establecer el estado de una entidad individual no hará que se realice el seguimiento de todas las entidades conectadas.</span><span class="sxs-lookup"><span data-stu-id="aadd1-134">Unlike in EF6, setting the state of an individual entity will not cause all connected entities to be tracked.</span></span> <span data-ttu-id="aadd1-135">Esto hace que se establezca el estado de esta manera como una operación de nivel inferior que llamar a `Add` , `Attach` o `Update` , que operan en un grafo completo de entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-135">This makes setting the state this way a lower-level operation than calling `Add`, `Attach`, or `Update`, which operate on an entire graph of entities.</span></span>

<span data-ttu-id="aadd1-136">En la tabla siguiente se resumen las maneras de usar una EntityEntry para trabajar con una entidad completa:</span><span class="sxs-lookup"><span data-stu-id="aadd1-136">The following table summarizes ways to use an EntityEntry to work with an entire entity:</span></span>

| <span data-ttu-id="aadd1-137">Miembro EntityEntry</span><span class="sxs-lookup"><span data-stu-id="aadd1-137">EntityEntry member</span></span>                                                                                         | <span data-ttu-id="aadd1-138">Descripción</span><span class="sxs-lookup"><span data-stu-id="aadd1-138">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | <span data-ttu-id="aadd1-139">Obtiene y establece el <xref:Microsoft.EntityFrameworkCore.EntityState> de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-139">Gets and sets the <xref:Microsoft.EntityFrameworkCore.EntityState> of the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | <span data-ttu-id="aadd1-140">Obtiene la instancia de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-140">Gets the entity instance.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <span data-ttu-id="aadd1-141"><xref:Microsoft.EntityFrameworkCore.DbContext>Que está realizando el seguimiento de esta entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-141">The <xref:Microsoft.EntityFrameworkCore.DbContext> that is tracking this entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <span data-ttu-id="aadd1-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadatos para el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata for the type of entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | <span data-ttu-id="aadd1-143">Indica si se ha establecido o no el valor de clave de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-143">Whether or not the entity has had its key value set.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | <span data-ttu-id="aadd1-144">Sobrescribe los valores de propiedad con los valores leídos de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-144">Overwrites property values with values read from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | <span data-ttu-id="aadd1-145">Fuerza la detección de cambios solo para esta entidad; consulte [detección y notificaciones de cambios](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="aadd1-145">Forces detection of changes for this entity only; see [Change Detection and Notifications](xref:core/change-tracking/change-detection).</span></span>

### <a name="working-with-a-single-property"></a><span data-ttu-id="aadd1-146">Trabajar con una sola propiedad</span><span class="sxs-lookup"><span data-stu-id="aadd1-146">Working with a single property</span></span>

<span data-ttu-id="aadd1-147">Varias sobrecargas de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> permiten el acceso a información sobre una propiedad individual de una entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-147">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> allow access to information about an individual property of an entity.</span></span> <span data-ttu-id="aadd1-148">Por ejemplo, mediante una API de tipo "fuertemente tipada":</span><span class="sxs-lookup"><span data-stu-id="aadd1-148">For example, using a strongly-typed, fluent-like API:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

<span data-ttu-id="aadd1-149">En su lugar, se puede pasar el nombre de la propiedad como una cadena.</span><span class="sxs-lookup"><span data-stu-id="aadd1-149">The property name can instead be passed as a string.</span></span> <span data-ttu-id="aadd1-150">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-150">For example:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

<span data-ttu-id="aadd1-151">A continuación, el devuelto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> se puede utilizar para tener acceso a la información sobre la propiedad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-151">The returned <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> can then be used to access information about the property.</span></span> <span data-ttu-id="aadd1-152">Por ejemplo, se puede usar para obtener y establecer el valor actual de la propiedad en esta entidad:</span><span class="sxs-lookup"><span data-stu-id="aadd1-152">For example, it can be used to get and set the current value of the property on this entity:</span></span>

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

<span data-ttu-id="aadd1-153">Los dos métodos de propiedad usados anteriormente devuelven una instancia genérica fuertemente tipada <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> .</span><span class="sxs-lookup"><span data-stu-id="aadd1-153">Both of the Property methods used above return a strongly-typed generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> instance.</span></span> <span data-ttu-id="aadd1-154">Se prefiere el uso de este tipo genérico porque permite el acceso a los valores de propiedad sin [tipos de valor de conversión boxing](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span><span class="sxs-lookup"><span data-stu-id="aadd1-154">Using this generic type is preferred because it allows access to property values without [boxing value types](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span></span> <span data-ttu-id="aadd1-155">Sin embargo, si no se conoce el tipo de entidad o propiedad en tiempo de compilación, en <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> su lugar se puede obtener un no genérico:</span><span class="sxs-lookup"><span data-stu-id="aadd1-155">However, if the type of entity or property is not known at compile-time, then a non-generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> can be obtained instead:</span></span>

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

<span data-ttu-id="aadd1-156">Esto permite el acceso a la información de propiedades de cualquier propiedad, independientemente de su tipo, a costa de los tipos de valor de conversión boxing.</span><span class="sxs-lookup"><span data-stu-id="aadd1-156">This allows access to property information for any property regardless of its type, at the expense of boxing value types.</span></span> <span data-ttu-id="aadd1-157">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-157">For example:</span></span>

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

<span data-ttu-id="aadd1-158">En la tabla siguiente se resume la información de propiedades expuesta por PropertyEntry:</span><span class="sxs-lookup"><span data-stu-id="aadd1-158">The following table summarizes property information exposed by PropertyEntry:</span></span>

| <span data-ttu-id="aadd1-159">Miembro PropertyEntry</span><span class="sxs-lookup"><span data-stu-id="aadd1-159">PropertyEntry member</span></span>                               | <span data-ttu-id="aadd1-160">Descripción</span><span class="sxs-lookup"><span data-stu-id="aadd1-160">Description</span></span>
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | <span data-ttu-id="aadd1-161">Obtiene y establece el valor actual de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-161">Gets and sets the current value of the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | <span data-ttu-id="aadd1-162">Obtiene y establece el valor original de la propiedad, si está disponible.</span><span class="sxs-lookup"><span data-stu-id="aadd1-162">Gets and sets the original value of the property, if available.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | <span data-ttu-id="aadd1-163">Referencia inversa a la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-163">A back reference to the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> for the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <span data-ttu-id="aadd1-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadatos de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadata for the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | <span data-ttu-id="aadd1-165">Indica si esta propiedad está marcada como modificada y permite cambiar este estado.</span><span class="sxs-lookup"><span data-stu-id="aadd1-165">Indicates whether this property is marked as modified, and allows this state to be changed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | <span data-ttu-id="aadd1-166">Indica si esta propiedad está marcada como [temporal](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)y permite cambiar este estado.</span><span class="sxs-lookup"><span data-stu-id="aadd1-166">Indicates whether this property is marked as [temporary](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values), and allows this state to be changed.</span></span>

<span data-ttu-id="aadd1-167">Notas:</span><span class="sxs-lookup"><span data-stu-id="aadd1-167">Notes:</span></span>

- <span data-ttu-id="aadd1-168">El valor original de una propiedad es el valor que tenía la propiedad cuando se realizó una consulta a la entidad desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-168">The original value of a property is the value that the property had when the entity was queried from the database.</span></span> <span data-ttu-id="aadd1-169">Sin embargo, los valores originales no están disponibles si la entidad se desconectó y, a continuación, se asocia explícitamente a otro DbContext, por ejemplo, con `Attach` o `Update` .</span><span class="sxs-lookup"><span data-stu-id="aadd1-169">However, original values are not available if the entity was disconnected and then explicitly attached to another DbContext, for example with `Attach` or `Update`.</span></span> <span data-ttu-id="aadd1-170">En este caso, el valor original devuelto será el mismo que el valor actual.</span><span class="sxs-lookup"><span data-stu-id="aadd1-170">In this case, the original value returned will be the same as the current value.</span></span>
- <span data-ttu-id="aadd1-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> solo actualizará las propiedades marcadas como modificadas.</span><span class="sxs-lookup"><span data-stu-id="aadd1-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> will only update properties marked as modified.</span></span> <span data-ttu-id="aadd1-172">Establézcalo en <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> true para forzar que EF Core actualice un valor de propiedad determinado, o establézcalo en false para impedir que EF Core actualice el valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-172">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> to true to force EF Core to update a given property value, or set it to false to prevent EF Core from updating the property value.</span></span>
- <span data-ttu-id="aadd1-173">Los [generadores](xref:core/modeling/generated-properties)de valores EF Core generan normalmente [valores temporales](xref:core/change-tracking/miscellaneous) .</span><span class="sxs-lookup"><span data-stu-id="aadd1-173">[Temporary values](xref:core/change-tracking/miscellaneous) are typically generated by EF Core [value generators](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="aadd1-174">Al establecer el valor actual de una propiedad, se reemplazará el valor temporal por el valor especificado y se marcará la propiedad como no temporal.</span><span class="sxs-lookup"><span data-stu-id="aadd1-174">Setting the current value of a property will replace the temporary value with the given value and mark the property as not temporary.</span></span> <span data-ttu-id="aadd1-175">Establézcalo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> en true para forzar que un valor sea temporal incluso después de que se haya establecido explícitamente.</span><span class="sxs-lookup"><span data-stu-id="aadd1-175">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> to true to force a value to be temporary even after it has been explicitly set.</span></span>

### <a name="working-with-a-single-navigation"></a><span data-ttu-id="aadd1-176">Trabajar con una sola navegación</span><span class="sxs-lookup"><span data-stu-id="aadd1-176">Working with a single navigation</span></span>

<span data-ttu-id="aadd1-177">Varias sobrecargas de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> permiten el acceso a información sobre una navegación individual.</span><span class="sxs-lookup"><span data-stu-id="aadd1-177">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> allow access to information about an individual navigation.</span></span>

<span data-ttu-id="aadd1-178">Se tiene acceso a las navegaciones de referencia a una sola entidad relacionada a través de los <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> métodos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-178">Reference navigations to a single related entity are accessed through the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> methods.</span></span> <span data-ttu-id="aadd1-179">Las navegaciones de referencia apuntan a los lados "uno" de las relaciones uno a varios y ambos lados de las relaciones uno a uno.</span><span class="sxs-lookup"><span data-stu-id="aadd1-179">Reference navigations point to the "one" sides of one-to-many relationships, and both sides of one-to-one relationships.</span></span> <span data-ttu-id="aadd1-180">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-180">For example:</span></span>

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

<span data-ttu-id="aadd1-181">Las navegaciones también pueden ser colecciones de entidades relacionadas cuando se usan para los lados "varios" de las relaciones uno a varios y varios a varios.</span><span class="sxs-lookup"><span data-stu-id="aadd1-181">Navigations can also be collections of related entities when used for the "many" sides of one-to-many and many-to-many relationships.</span></span> <span data-ttu-id="aadd1-182">Los <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> métodos se usan para tener acceso a las navegaciones de la colección.</span><span class="sxs-lookup"><span data-stu-id="aadd1-182">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> methods are used to access collection navigations.</span></span> <span data-ttu-id="aadd1-183">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-183">For example:</span></span>

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

<span data-ttu-id="aadd1-184">Algunas operaciones son comunes para todas las navegaciones.</span><span class="sxs-lookup"><span data-stu-id="aadd1-184">Some operations are common for all navigations.</span></span> <span data-ttu-id="aadd1-185">Se puede tener acceso a estas para las referencias y a las navegaciones de la colección mediante el <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> método.</span><span class="sxs-lookup"><span data-stu-id="aadd1-185">These can be accessed for both reference and collection navigations using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="aadd1-186">Tenga en cuenta que solo está disponible el acceso no genérico cuando se tiene acceso a todas las navegaciones juntas.</span><span class="sxs-lookup"><span data-stu-id="aadd1-186">Note that only non-generic access is available when accessing all navigations together.</span></span> <span data-ttu-id="aadd1-187">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-187">For example:</span></span>

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

<span data-ttu-id="aadd1-188">En la tabla siguiente se resumen las distintas formas de usar <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :</span><span class="sxs-lookup"><span data-stu-id="aadd1-188">The following table summarizes ways to use <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry>:</span></span>

| <span data-ttu-id="aadd1-189">Miembro NavigationEntry</span><span class="sxs-lookup"><span data-stu-id="aadd1-189">NavigationEntry member</span></span>                                                                                    | <span data-ttu-id="aadd1-190">Descripción</span><span class="sxs-lookup"><span data-stu-id="aadd1-190">Description</span></span>
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | <span data-ttu-id="aadd1-191">Obtiene y establece el valor actual de la navegación.</span><span class="sxs-lookup"><span data-stu-id="aadd1-191">Gets and sets the current value of the navigation.</span></span> <span data-ttu-id="aadd1-192">Se trata de la colección completa para las navegaciones de la colección.</span><span class="sxs-lookup"><span data-stu-id="aadd1-192">This is the entire collection for collection navigations.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <span data-ttu-id="aadd1-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadatos para la navegación.</span><span class="sxs-lookup"><span data-stu-id="aadd1-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadata for the navigation.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | <span data-ttu-id="aadd1-194">Obtiene o establece un valor que indica si la entidad o colección relacionada se ha cargado completamente desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-194">Gets or sets a value indicating whether the related entity or collection has been fully loaded from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | <span data-ttu-id="aadd1-195">Carga la entidad o colección relacionada desde la base de datos; Vea la [carga explícita de datos relacionados](xref:core/querying/related-data/explicit).</span><span class="sxs-lookup"><span data-stu-id="aadd1-195">Loads the related entity or collection from the database; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | <span data-ttu-id="aadd1-196">La consulta EF Core utilizaría para cargar esta navegación como un `IQueryable` que se puede componer más; vea la [carga explícita de datos relacionados](xref:core/querying/related-data/explicit).</span><span class="sxs-lookup"><span data-stu-id="aadd1-196">The query EF Core would use to load this navigation as an `IQueryable` that can be further composed; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>

### <a name="working-with-all-properties-of-an-entity"></a><span data-ttu-id="aadd1-197">Trabajar con todas las propiedades de una entidad</span><span class="sxs-lookup"><span data-stu-id="aadd1-197">Working with all properties of an entity</span></span>

<span data-ttu-id="aadd1-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> Devuelve un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> para cada propiedad de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> for every property of the entity.</span></span> <span data-ttu-id="aadd1-199">Se puede usar para realizar una acción para cada propiedad de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-199">This can be used to perform an action for every property of the entity.</span></span> <span data-ttu-id="aadd1-200">Por ejemplo, para establecer cualquier propiedad DateTime en `DateTime.Now` :</span><span class="sxs-lookup"><span data-stu-id="aadd1-200">For example, to set any DateTime property to `DateTime.Now`:</span></span>

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

<span data-ttu-id="aadd1-201">Además, EntityEntry contiene varios métodos para obtener y establecer todos los valores de propiedad al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="aadd1-201">In addition, EntityEntry contains several methods to get and set all property values at the same time.</span></span> <span data-ttu-id="aadd1-202">Estos métodos usan la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> clase, que representa una colección de propiedades y sus valores.</span><span class="sxs-lookup"><span data-stu-id="aadd1-202">These methods use the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> class, which represents a collection of properties and their values.</span></span> <span data-ttu-id="aadd1-203">PropertyValues se puede obtener para los valores actuales o originales, o para los valores que están almacenados actualmente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-203">PropertyValues can be obtained for current or original values, or for the values as currently stored in the database.</span></span> <span data-ttu-id="aadd1-204">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-204">For example:</span></span>

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

<span data-ttu-id="aadd1-205">Estos objetos PropertyValues no son muy útiles por sí solos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-205">These PropertyValues objects are not very useful on their own.</span></span> <span data-ttu-id="aadd1-206">Sin embargo, se pueden combinar para realizar operaciones comunes necesarias al manipular entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-206">However, they can be combined to perform common operations needed when manipulating entities.</span></span> <span data-ttu-id="aadd1-207">Esto resulta útil cuando se trabaja con objetos de transferencia de datos y cuando se resuelven [conflictos de simultaneidad optimista](xref:core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="aadd1-207">This is useful when working with data transfer objects and when resolving [optimistic concurrency conflicts](xref:core/saving/concurrency).</span></span> <span data-ttu-id="aadd1-208">En las secciones siguientes se muestran algunos ejemplos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-208">The following sections show some examples.</span></span>

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a><span data-ttu-id="aadd1-209">Establecimiento de los valores actuales o originales de una entidad o DTO</span><span class="sxs-lookup"><span data-stu-id="aadd1-209">Setting current or original values from an entity or DTO</span></span>

<span data-ttu-id="aadd1-210">Los valores actuales o originales de una entidad se pueden actualizar mediante la copia de los valores de otro objeto.</span><span class="sxs-lookup"><span data-stu-id="aadd1-210">The current or original values of an entity can be updated by copying values from another object.</span></span> <span data-ttu-id="aadd1-211">Por ejemplo, considere un `BlogDto` objeto de transferencia de datos (DTO) con las mismas propiedades que el tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="aadd1-211">For example, consider a `BlogDto` data transfer object (DTO) with the same properties as the entity type:</span></span>

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

<span data-ttu-id="aadd1-212">Se puede usar para establecer los valores actuales de una entidad de la que se ha realizado un seguimiento mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="aadd1-212">This can be used to set the current values of a tracked entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

<span data-ttu-id="aadd1-213">Esta técnica se usa a veces al actualizar una entidad con valores obtenidos de una llamada de servicio o un cliente en una aplicación de n niveles.</span><span class="sxs-lookup"><span data-stu-id="aadd1-213">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="aadd1-214">Tenga en cuenta que el objeto utilizado no tiene que ser del mismo tipo que la entidad, siempre y cuando tenga propiedades cuyos nombres coincidan con los de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-214">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="aadd1-215">En el ejemplo anterior, se usa una instancia de DTO `BlogDto` para establecer los valores actuales de una entidad de la que se ha realizado un seguimiento `Blog` .</span><span class="sxs-lookup"><span data-stu-id="aadd1-215">In the example above, an instance of the DTO `BlogDto` is used to set the current values of a tracked `Blog` entity.</span></span>

<span data-ttu-id="aadd1-216">Tenga en cuenta que las propiedades solo se marcarán como modificadas si el conjunto de valores difiere del valor actual.</span><span class="sxs-lookup"><span data-stu-id="aadd1-216">Note that properties will only be marked as modified if the value set differs from the current value.</span></span>

#### <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="aadd1-217">Establecer los valores actuales o originales de un diccionario</span><span class="sxs-lookup"><span data-stu-id="aadd1-217">Setting current or original values from a dictionary</span></span>

<span data-ttu-id="aadd1-218">En el ejemplo anterior se establecen valores de una instancia de Entity o DTO.</span><span class="sxs-lookup"><span data-stu-id="aadd1-218">The previous example set values from an entity or DTO instance.</span></span> <span data-ttu-id="aadd1-219">El mismo comportamiento está disponible cuando los valores de propiedad se almacenan como pares de nombre y valor en un diccionario.</span><span class="sxs-lookup"><span data-stu-id="aadd1-219">The same behavior is available when property values are stored as name/value pairs in a dictionary.</span></span> <span data-ttu-id="aadd1-220">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-220">For example:</span></span>

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a><span data-ttu-id="aadd1-221">Establecer valores actuales o originales de la base de datos</span><span class="sxs-lookup"><span data-stu-id="aadd1-221">Setting current or original values from the database</span></span>

<span data-ttu-id="aadd1-222">Los valores actuales o originales de una entidad se pueden actualizar con los valores más recientes de la base de datos llamando a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> o <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> y usando el objeto devuelto para establecer los valores actuales o originales, o ambos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-222">The current or original values of an entity can be updated with the latest values from the database by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> and using the returned object to set current or original values, or both.</span></span> <span data-ttu-id="aadd1-223">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-223">For example:</span></span>

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="aadd1-224">Crear un objeto clonado que contenga valores actual, original o de base de datos</span><span class="sxs-lookup"><span data-stu-id="aadd1-224">Creating a cloned object containing current, original, or database values</span></span>

<span data-ttu-id="aadd1-225">El objeto PropertyValues devuelto de CurrentValues, OriginalValues o GetDatabaseValues se puede usar para crear un clon de la entidad mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="aadd1-225">The PropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType>.</span></span> <span data-ttu-id="aadd1-226">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-226">For example:</span></span>

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

<span data-ttu-id="aadd1-227">Tenga en cuenta que `ToObject` devuelve una nueva instancia de la que el DbContext no realiza un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="aadd1-227">Note that `ToObject` returns a new instance that is not tracked by the DbContext.</span></span> <span data-ttu-id="aadd1-228">El objeto devuelto tampoco tiene ninguna relación establecida con otras entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-228">The returned object also does not have any relationships set to other entities.</span></span>

<span data-ttu-id="aadd1-229">El objeto clonado puede ser útil para resolver problemas relacionados con las actualizaciones simultáneas en la base de datos, especialmente cuando se enlazan datos a objetos de un tipo determinado.</span><span class="sxs-lookup"><span data-stu-id="aadd1-229">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially when data binding to objects of a certain type.</span></span> <span data-ttu-id="aadd1-230">Vea [simultaneidad optimista](xref:core/saving/concurrency) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="aadd1-230">See [optimistic concurrency](xref:core/saving/concurrency) for more information.</span></span>

### <a name="working-with-all-navigations-of-an-entity"></a><span data-ttu-id="aadd1-231">Trabajar con todas las navegaciones de una entidad</span><span class="sxs-lookup"><span data-stu-id="aadd1-231">Working with all navigations of an entity</span></span>

<span data-ttu-id="aadd1-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> Devuelve un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> para cada navegación de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> for every navigation of the entity.</span></span> <span data-ttu-id="aadd1-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> hacen lo mismo, pero se restringen a las navegaciones de referencia o de colección, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="aadd1-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> do the same thing, but restricted to reference or collection navigations respectively.</span></span> <span data-ttu-id="aadd1-234">Se puede usar para realizar una acción para cada navegación de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-234">This can be used to perform an action for every navigation of the entity.</span></span> <span data-ttu-id="aadd1-235">Por ejemplo, para forzar la carga de todas las entidades relacionadas:</span><span class="sxs-lookup"><span data-stu-id="aadd1-235">For example, to force loading of all related entities:</span></span>

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a><span data-ttu-id="aadd1-236">Trabajar con todos los miembros de una entidad</span><span class="sxs-lookup"><span data-stu-id="aadd1-236">Working with all members of an entity</span></span>

<span data-ttu-id="aadd1-237">Las propiedades normales y las propiedades de navegación tienen un estado y un comportamiento diferentes.</span><span class="sxs-lookup"><span data-stu-id="aadd1-237">Regular properties and navigation properties have different state and behavior.</span></span> <span data-ttu-id="aadd1-238">Por lo tanto, es habitual procesar las navegaciones y las no navegaciones por separado, tal como se muestra en las secciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="aadd1-238">It is therefore common to process navigations and non-navigations separately, as shown in the sections above.</span></span> <span data-ttu-id="aadd1-239">Sin embargo, a veces puede resultar útil hacer algo con cualquier miembro de la entidad, independientemente de si se trata de una propiedad o navegación normal.</span><span class="sxs-lookup"><span data-stu-id="aadd1-239">However, sometimes it can be useful to do something with any member of the entity, regardless of whether it is a regular property or navigation.</span></span> <span data-ttu-id="aadd1-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> se proporcionan para este fin.</span><span class="sxs-lookup"><span data-stu-id="aadd1-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> are provided for this purpose.</span></span> <span data-ttu-id="aadd1-241">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-241">For example:</span></span>

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

<span data-ttu-id="aadd1-242">La ejecución de este código en un blog del ejemplo genera el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="aadd1-242">Running this code on a blog from the sample generates the following output:</span></span>

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> <span data-ttu-id="aadd1-243">La [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) muestra información como esta.</span><span class="sxs-lookup"><span data-stu-id="aadd1-243">The [change tracker debug view](xref:core/change-tracking/debug-views) shows information like this.</span></span> <span data-ttu-id="aadd1-244">La vista de depuración para todo el seguimiento de cambios se genera a partir del individuo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> de cada entidad de la que se realiza el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="aadd1-244">The debug view for the entire change tracker is generated from the individual <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> of each tracked entity.</span></span>

## <a name="find-and-findasync"></a><span data-ttu-id="aadd1-245">Buscar y FindAsync</span><span class="sxs-lookup"><span data-stu-id="aadd1-245">Find and FindAsync</span></span>

<span data-ttu-id="aadd1-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> están diseñados para una búsqueda eficaz de una sola entidad cuando se conoce su clave principal.</span><span class="sxs-lookup"><span data-stu-id="aadd1-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> are designed for efficient lookup of a single entity when its primary key is known.</span></span> <span data-ttu-id="aadd1-247">Buscar primero comprueba si ya se ha realizado el seguimiento de la entidad y, si es así, devuelve la entidad inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="aadd1-247">Find first checks if the entity is already tracked, and if so returns the entity immediately.</span></span> <span data-ttu-id="aadd1-248">Solo se realiza una consulta de base de datos si no se realiza un seguimiento de la entidad localmente.</span><span class="sxs-lookup"><span data-stu-id="aadd1-248">A database query is only made if the entity is not tracked locally.</span></span> <span data-ttu-id="aadd1-249">Por ejemplo, considere este código que llama a buscar dos veces para la misma entidad:</span><span class="sxs-lookup"><span data-stu-id="aadd1-249">For example, consider this code that calls Find twice for the same entity:</span></span>

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

<span data-ttu-id="aadd1-250">La salida de este código (incluido el registro de EF Core) cuando se usa SQLite es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="aadd1-250">The output from this code (including EF Core logging) when using SQLite is:</span></span>

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

<span data-ttu-id="aadd1-251">Observe que la primera llamada no encuentra la entidad localmente y ejecuta una consulta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-251">Notice that the first call does not find the entity locally and so executes a database query.</span></span> <span data-ttu-id="aadd1-252">Por el contrario, la segunda llamada devuelve la misma instancia sin consultar la base de datos porque ya se está realizando el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="aadd1-252">Conversely, the second call returns the same instance without querying the database because it is already being tracked.</span></span>

<span data-ttu-id="aadd1-253">Find devuelve NULL si no se realiza un seguimiento de una entidad con la clave especificada localmente y no existe en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-253">Find returns null if an entity with the given key is not tracked locally and does not exist in the database.</span></span>

### <a name="composite-keys"></a><span data-ttu-id="aadd1-254">Claves compuestas</span><span class="sxs-lookup"><span data-stu-id="aadd1-254">Composite keys</span></span>

<span data-ttu-id="aadd1-255">La búsqueda también se puede usar con claves compuestas.</span><span class="sxs-lookup"><span data-stu-id="aadd1-255">Find can also be used with composite keys.</span></span> <span data-ttu-id="aadd1-256">Por ejemplo, considere una `OrderLine` entidad con una clave compuesta que consta del identificador de pedido y el ID. de producto:</span><span class="sxs-lookup"><span data-stu-id="aadd1-256">For example, consider an `OrderLine` entity with a composite key consisting of the order ID and the product ID:</span></span>

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

<span data-ttu-id="aadd1-257">La clave compuesta debe estar configurada en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> para definir las partes clave _y su orden_.</span><span class="sxs-lookup"><span data-stu-id="aadd1-257">The composite key must be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> to define the key parts _and their order_.</span></span> <span data-ttu-id="aadd1-258">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-258">For example:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="aadd1-259">Observe que `OrderId` es la primera parte de la clave y `ProductId` es la segunda parte de la clave.</span><span class="sxs-lookup"><span data-stu-id="aadd1-259">Notice that `OrderId` is the first part of the key and `ProductId` is the second part of the key.</span></span> <span data-ttu-id="aadd1-260">Este orden debe utilizarse al pasar los valores de clave que se van a buscar.</span><span class="sxs-lookup"><span data-stu-id="aadd1-260">This order must be used when passing key values to Find.</span></span> <span data-ttu-id="aadd1-261">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-261">For example:</span></span>

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a><span data-ttu-id="aadd1-262">Usar ChangeTracker. Entries para tener acceso a todas las entidades sometidas a seguimiento</span><span class="sxs-lookup"><span data-stu-id="aadd1-262">Using ChangeTracker.Entries to access all tracked entities</span></span>

<span data-ttu-id="aadd1-263">Hasta ahora solo hemos tenido acceso a una sola vez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> .</span><span class="sxs-lookup"><span data-stu-id="aadd1-263">So far we have accessed only a single <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> at a time.</span></span> <span data-ttu-id="aadd1-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> Devuelve un EntityEntry para cada entidad de la que el DbContext realiza un seguimiento actualmente.</span><span class="sxs-lookup"><span data-stu-id="aadd1-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> returns an EntityEntry for every entity currently tracked by the DbContext.</span></span> <span data-ttu-id="aadd1-265">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-265">For example:</span></span>

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

<span data-ttu-id="aadd1-266">Este código genera el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="aadd1-266">This code generates the following output:</span></span>

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="aadd1-267">Observe que se devuelven entradas para blogs y publicaciones.</span><span class="sxs-lookup"><span data-stu-id="aadd1-267">Notice that entries for both blogs and posts are returned.</span></span> <span data-ttu-id="aadd1-268">En su lugar, los resultados se pueden filtrar a un tipo de entidad específico mediante la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> sobrecarga genérica:</span><span class="sxs-lookup"><span data-stu-id="aadd1-268">The results can instead be filtered to a specific entity type using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> generic overload:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

<span data-ttu-id="aadd1-269">La salida de este código muestra que solo se devuelven publicaciones:</span><span class="sxs-lookup"><span data-stu-id="aadd1-269">The output from this code shows that only posts are returned:</span></span>

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="aadd1-270">Además, el uso de la sobrecarga genérica devuelve instancias genéricas <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> .</span><span class="sxs-lookup"><span data-stu-id="aadd1-270">Also, using the generic overload returns generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances.</span></span> <span data-ttu-id="aadd1-271">Esto es lo que permite el acceso de tipo fluida a la `Id` propiedad en este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="aadd1-271">This is what allows that fluent-like access to the `Id` property in this example.</span></span>

<span data-ttu-id="aadd1-272">No es necesario que el tipo genérico usado para filtrar sea un tipo de entidad asignado; en su lugar, se puede usar un tipo base o una interfaz sin asignar.</span><span class="sxs-lookup"><span data-stu-id="aadd1-272">The generic type used for filtering does not have to be a mapped entity type; an unmapped base type or interface can be used instead.</span></span> <span data-ttu-id="aadd1-273">Por ejemplo, si todos los tipos de entidad del modelo implementan una interfaz que define su propiedad de clave:</span><span class="sxs-lookup"><span data-stu-id="aadd1-273">For example, if all the entity types in the model implement an interface defining their key property:</span></span>

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

<span data-ttu-id="aadd1-274">Después, esta interfaz se puede usar para trabajar con la clave de cualquier entidad de la que se ha realizado un seguimiento de forma fuertemente tipada.</span><span class="sxs-lookup"><span data-stu-id="aadd1-274">Then this interface can be used to work with the key of any tracked entity in a strongly-typed manner.</span></span> <span data-ttu-id="aadd1-275">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-275">For example:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a><span data-ttu-id="aadd1-276">Usar DbSet. local para consultar las entidades sometidas a seguimiento</span><span class="sxs-lookup"><span data-stu-id="aadd1-276">Using DbSet.Local to query tracked entities</span></span>

<span data-ttu-id="aadd1-277">Las consultas de EF Core se ejecutan siempre en la base de datos y solo devuelven las entidades que se han guardado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-277">EF Core queries are always executed on the database, and only return entities that have been saved to the database.</span></span> <span data-ttu-id="aadd1-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> proporciona un mecanismo para consultar a DbContext para entidades de las que se ha realizado un seguimiento local.</span><span class="sxs-lookup"><span data-stu-id="aadd1-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> provides a mechanism to query the DbContext for local, tracked entities.</span></span>

<span data-ttu-id="aadd1-279">Dado que `DbSet.Local` se utiliza para consultar las entidades de las que se realiza un seguimiento, es habitual cargar entidades en DbContext y, después, trabajar con esas entidades cargadas.</span><span class="sxs-lookup"><span data-stu-id="aadd1-279">Since `DbSet.Local` is used to query tracked entities, it is typical to load entities into the DbContext and then work with those loaded entities.</span></span> <span data-ttu-id="aadd1-280">Esto es especialmente cierto para el enlace de datos, pero también puede ser útil en otras situaciones.</span><span class="sxs-lookup"><span data-stu-id="aadd1-280">This is especially true for data binding, but can also be useful in other situations.</span></span> <span data-ttu-id="aadd1-281">Por ejemplo, en el código siguiente, la base de datos se consulta primero para todos los blogs y publicaciones.</span><span class="sxs-lookup"><span data-stu-id="aadd1-281">For example, in the following code the database is first queried for all blogs and posts.</span></span> <span data-ttu-id="aadd1-282">El <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> método de extensión se utiliza para ejecutar esta consulta con los resultados seguidos por el contexto sin devolverse directamente a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="aadd1-282">The <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> extension method is used to execute this query with the results tracked by the context without being returned directly to the application.</span></span> <span data-ttu-id="aadd1-283">(El uso de `ToList` o similar tiene el mismo efecto, pero con la sobrecarga de crear la lista devuelta, que no se necesita aquí). A continuación, en el ejemplo `DbSet.Local` se usa para acceder a las entidades de las que se realiza un seguimiento local:</span><span class="sxs-lookup"><span data-stu-id="aadd1-283">(Using `ToList` or similar has the same effect but with the overhead of creating the returned list, which is not needed here.) The example then uses `DbSet.Local` to access the locally tracked entities:</span></span>

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

<span data-ttu-id="aadd1-284">Tenga en cuenta que, a diferencia <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> de, `DbSet.Local` devuelve directamente las instancias de la entidad.</span><span class="sxs-lookup"><span data-stu-id="aadd1-284">Notice that, unlike <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType>, `DbSet.Local` returns entity instances directly.</span></span> <span data-ttu-id="aadd1-285">Por supuesto, una EntityEntry puede obtenerse siempre para la entidad devuelta mediante una llamada a <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="aadd1-285">An EntityEntry can, of course, always be obtained for the returned entity by calling <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>.</span></span>

### <a name="the-local-view"></a><span data-ttu-id="aadd1-286">La vista local</span><span class="sxs-lookup"><span data-stu-id="aadd1-286">The local view</span></span>

<span data-ttu-id="aadd1-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Devuelve una vista de entidades de las que se realiza un seguimiento local que refleja el actual <xref:Microsoft.EntityFrameworkCore.EntityState> de esas entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns a view of locally tracked entities that reflects the current <xref:Microsoft.EntityFrameworkCore.EntityState> of those entities.</span></span> <span data-ttu-id="aadd1-288">En concreto, esto significa que:</span><span class="sxs-lookup"><span data-stu-id="aadd1-288">Specifically, this means that:</span></span>

- <span data-ttu-id="aadd1-289">`Added` se incluyen las entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-289">`Added` entities are included.</span></span> <span data-ttu-id="aadd1-290">Tenga en cuenta que este no es el caso de las consultas de EF Core normales, ya que las `Added` entidades aún no existen en la base de datos y, por tanto, nunca se devuelven en una consulta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="aadd1-290">Note that this is not the case for normal EF Core queries, since `Added` entities do not yet exist in the database and so are therefore never returned by a database query.</span></span>
- <span data-ttu-id="aadd1-291">`Deleted` se excluyen las entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-291">`Deleted` entities are excluded.</span></span> <span data-ttu-id="aadd1-292">Tenga en cuenta que esto no es el caso de las consultas de EF Core normales, ya que las `Deleted` entidades todavía existen en la base de datos y _, por_ lo tanto, las consultas de base de datos las devuelven.</span><span class="sxs-lookup"><span data-stu-id="aadd1-292">Note that this is again not the case for normal EF Core queries, since `Deleted` entities still exist in the database and so _are_ returned by database queries.</span></span>

<span data-ttu-id="aadd1-293">Todo esto significa que `DbSet.Local` es una vista sobre los datos que refleja el estado conceptual actual del gráfico de entidades, con las `Added` entidades incluidas y las `Deleted` entidades excluidas.</span><span class="sxs-lookup"><span data-stu-id="aadd1-293">All of this means that `DbSet.Local` is view over the data that reflects the current conceptual state of the entity graph, with `Added` entities included and `Deleted` entities excluded.</span></span> <span data-ttu-id="aadd1-294">Esto coincide con el estado de la base de datos que se espera que sea después de llamar a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="aadd1-294">This matches what database state is expected to be after SaveChanges is called.</span></span>

<span data-ttu-id="aadd1-295">Esta suele ser la vista ideal para el enlace de datos, ya que presenta al usuario los datos a medida que los entienden en función de los cambios realizados por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="aadd1-295">This is typically the ideal view for data binding, since it presents to the user the data as they understand it based on the changes made by the application.</span></span>

<span data-ttu-id="aadd1-296">En el código siguiente se muestra el marcado de una publicación como `Deleted` y, a continuación, la adición de una nueva publicación, que se marca como `Added` :</span><span class="sxs-lookup"><span data-stu-id="aadd1-296">The following code demonstrates this my marking one post as `Deleted` and then adding a new post, marking it as `Added`:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

<span data-ttu-id="aadd1-297">El resultado de este código es:</span><span class="sxs-lookup"><span data-stu-id="aadd1-297">The output from this code is:</span></span>

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

<span data-ttu-id="aadd1-298">Tenga en cuenta que la publicación eliminada se quita de la vista local y se incluye la publicación agregada.</span><span class="sxs-lookup"><span data-stu-id="aadd1-298">Notice that the deleted post is removed from the local view, and the added post is included.</span></span>

### <a name="using-local-to-add-and-remove-entities"></a><span data-ttu-id="aadd1-299">Usar local para agregar y quitar entidades</span><span class="sxs-lookup"><span data-stu-id="aadd1-299">Using Local to add and remove entities</span></span>

<span data-ttu-id="aadd1-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> devuelve una instancia de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span><span class="sxs-lookup"><span data-stu-id="aadd1-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns an instance of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span></span> <span data-ttu-id="aadd1-301">Se trata de una implementación de <xref:System.Collections.Generic.ICollection%601> que genera y responde a las notificaciones cuando se agregan y quitan entidades de la colección.</span><span class="sxs-lookup"><span data-stu-id="aadd1-301">This is an implementation of <xref:System.Collections.Generic.ICollection%601> that generates and responds to notifications when entities are added and removed from the collection.</span></span> <span data-ttu-id="aadd1-302">(Este es el mismo concepto que <xref:System.Collections.ObjectModel.ObservableCollection%601> , pero se implementa como una proyección sobre las entradas de seguimiento de cambios de EF Core existentes, en lugar de como una colección independiente).</span><span class="sxs-lookup"><span data-stu-id="aadd1-302">(This is the same concept as <xref:System.Collections.ObjectModel.ObservableCollection%601>, but implemented as a projection over existing EF Core change tracking entries, rather than as an independent collection.)</span></span>

<span data-ttu-id="aadd1-303">Las notificaciones de la vista local se enlazan al seguimiento de cambios de DbContext de modo que la vista local permanece sincronizada con DbContext.</span><span class="sxs-lookup"><span data-stu-id="aadd1-303">The local view's notifications are hooked into DbContext change tracking such that the local view stays in sync with the DbContext.</span></span> <span data-ttu-id="aadd1-304">Concretamente:</span><span class="sxs-lookup"><span data-stu-id="aadd1-304">Specifically:</span></span>

- <span data-ttu-id="aadd1-305">Agregar una nueva entidad a `DbSet.Local` hace que el DbContext realice el seguimiento, normalmente en el `Added` Estado.</span><span class="sxs-lookup"><span data-stu-id="aadd1-305">Adding a new entity to `DbSet.Local` causes it to be tracked by the DbContext, typically in the `Added` state.</span></span> <span data-ttu-id="aadd1-306">(Si la entidad ya tiene un valor de clave generado, se realiza el seguimiento como `Unchanged` en su lugar).</span><span class="sxs-lookup"><span data-stu-id="aadd1-306">(If the entity already has a generated key value, then it is tracked as `Unchanged` instead.)</span></span>
- <span data-ttu-id="aadd1-307">Al quitar una entidad de `DbSet.Local` , se marca como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="aadd1-307">Removing an entity from `DbSet.Local` causes it to be marked as `Deleted`.</span></span>
- <span data-ttu-id="aadd1-308">Una entidad que se convierte en el seguimiento por DbContext aparecerá automáticamente en la `DbSet.Local` colección.</span><span class="sxs-lookup"><span data-stu-id="aadd1-308">An entity that becomes tracked by the DbContext will automatically appear in the `DbSet.Local` collection.</span></span> <span data-ttu-id="aadd1-309">Por ejemplo, la ejecución de una consulta para incorporar más entidades automáticamente hace que se actualice la vista local.</span><span class="sxs-lookup"><span data-stu-id="aadd1-309">For example, executing a query to bring in more entities automatically causes the local view to be updated.</span></span>
- <span data-ttu-id="aadd1-310">Una entidad marcada como se `Deleted` quitará automáticamente de la colección local.</span><span class="sxs-lookup"><span data-stu-id="aadd1-310">An entity that is marked as `Deleted` will be removed from the local collection automatically.</span></span>

<span data-ttu-id="aadd1-311">Esto significa que la vista local se puede usar para manipular las entidades de las que se ha realizado un seguimiento agregando y quitando de la colección.</span><span class="sxs-lookup"><span data-stu-id="aadd1-311">This means the local view can be used to manipulate tracked entities simply by adding and removing from the collection.</span></span> <span data-ttu-id="aadd1-312">Por ejemplo, permite modificar el código de ejemplo anterior para agregar y quitar entradas de la colección local:</span><span class="sxs-lookup"><span data-stu-id="aadd1-312">For example, lets modify the previous example code to add and remove posts from the local collection:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

<span data-ttu-id="aadd1-313">La salida permanece sin cambios en el ejemplo anterior porque los cambios realizados en la vista local se sincronizan con DbContext.</span><span class="sxs-lookup"><span data-stu-id="aadd1-313">The output remains unchanged from the previous example because changes made to the local view are synced with the DbContext.</span></span>

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a><span data-ttu-id="aadd1-314">Usar la vista local para el enlace de datos de Windows Forms o WPF</span><span class="sxs-lookup"><span data-stu-id="aadd1-314">Using the local view for Windows Forms or WPF data binding</span></span>

<span data-ttu-id="aadd1-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> constituye la base para el enlace de datos a entidades EF Core.</span><span class="sxs-lookup"><span data-stu-id="aadd1-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> forms the basis for data binding to EF Core entities.</span></span> <span data-ttu-id="aadd1-316">Sin embargo, tanto Windows Forms como WPF funcionan mejor cuando se usan con el tipo específico de colección de notificaciones que esperan.</span><span class="sxs-lookup"><span data-stu-id="aadd1-316">However, both Windows Forms and WPF work best when used with the specific type of notifying collection that they expect.</span></span> <span data-ttu-id="aadd1-317">La vista local admite la creación de estos tipos de colección específicos:</span><span class="sxs-lookup"><span data-stu-id="aadd1-317">The local view supports creating these specific collection types:</span></span>

- <span data-ttu-id="aadd1-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> Devuelve un <xref:System.Collections.ObjectModel.ObservableCollection%601> para el enlace de datos de WPF.</span><span class="sxs-lookup"><span data-stu-id="aadd1-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> returns an <xref:System.Collections.ObjectModel.ObservableCollection%601> for WPF data binding.</span></span>
- <span data-ttu-id="aadd1-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> Devuelve un <xref:System.ComponentModel.BindingList%601> para el enlace de datos Windows Forms.</span><span class="sxs-lookup"><span data-stu-id="aadd1-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> returns a <xref:System.ComponentModel.BindingList%601> for Windows Forms data binding.</span></span>

<span data-ttu-id="aadd1-320">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aadd1-320">For example:</span></span>

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

<span data-ttu-id="aadd1-321">Vea Introducción [a WPF](xref:core/get-started/wpf) para obtener más información sobre el enlace de datos de wpf con EF Core.</span><span class="sxs-lookup"><span data-stu-id="aadd1-321">See [Get Started with WPF](xref:core/get-started/wpf) for more information on WPF data binding with EF Core.</span></span>

> [!TIP]
> <span data-ttu-id="aadd1-322">La vista local de una instancia de DbSet determinada se crea de forma diferida cuando se obtiene acceso por primera vez y después se almacena en caché.</span><span class="sxs-lookup"><span data-stu-id="aadd1-322">The local view for a given DbSet instance is created lazily when first accessed and then cached.</span></span> <span data-ttu-id="aadd1-323">La creación de LocalView es rápida y no usa mucha memoria.</span><span class="sxs-lookup"><span data-stu-id="aadd1-323">LocalView creation itself is fast and it does not use significant memory.</span></span> <span data-ttu-id="aadd1-324">Sin embargo, llama a [DetectChanges](xref:core/change-tracking/change-detection), que puede ser lento para un gran número de entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-324">However, it does call [DetectChanges](xref:core/change-tracking/change-detection), which can be slow for large numbers of entities.</span></span> <span data-ttu-id="aadd1-325">Las colecciones creadas por `ToObservableCollection` y `ToBindingList` también se crean de forma diferida y, a continuación, se almacenan en caché.</span><span class="sxs-lookup"><span data-stu-id="aadd1-325">The collections created by `ToObservableCollection` and `ToBindingList` are also created lazily and and then cached.</span></span> <span data-ttu-id="aadd1-326">Ambos métodos crean colecciones nuevas, que pueden ser lentas y usar una gran cantidad de memoria cuando se usan miles de entidades.</span><span class="sxs-lookup"><span data-stu-id="aadd1-326">Both of these methods create new collections, which can be slow and use a lot of memory when thousands of entities are involved.</span></span>
