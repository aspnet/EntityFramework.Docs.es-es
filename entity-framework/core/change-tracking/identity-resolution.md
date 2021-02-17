---
title: 'Resolución de identidad: EF Core'
description: Resolución de varias instancias de entidad en una sola instancia mediante valores de clave principal
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: d4c8f935c8d0ab92eaecd8fc7a4156bd824713d4
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543619"
---
# <a name="identity-resolution-in-ef-core"></a><span data-ttu-id="20950-103">Resolución de identidades en EF Core</span><span class="sxs-lookup"><span data-stu-id="20950-103">Identity Resolution in EF Core</span></span>

<span data-ttu-id="20950-104">Un <xref:Microsoft.EntityFrameworkCore.DbContext> solo puede realizar el seguimiento de una instancia de entidad con cualquier valor de clave principal determinado.</span><span class="sxs-lookup"><span data-stu-id="20950-104">A <xref:Microsoft.EntityFrameworkCore.DbContext> can only track one entity instance with any given primary key value.</span></span> <span data-ttu-id="20950-105">Esto significa que se deben resolver varias instancias de una entidad con el mismo valor de clave en una sola instancia.</span><span class="sxs-lookup"><span data-stu-id="20950-105">This means multiple instances of an entity with the same key value must be resolved to a single instance.</span></span> <span data-ttu-id="20950-106">Esto se denomina "resolución de identidad".</span><span class="sxs-lookup"><span data-stu-id="20950-106">This is called "identity resolution".</span></span> <span data-ttu-id="20950-107">La resolución de identidad garantiza Entity Framework Core (EF Core) realiza el seguimiento de un gráfico coherente sin ambigüedades sobre las relaciones o los valores de propiedad de las entidades.</span><span class="sxs-lookup"><span data-stu-id="20950-107">Identity resolution ensures Entity Framework Core (EF Core) is tracking a consistent graph with no ambiguities about the relationships or property values of the entities.</span></span>

> [!TIP]
> <span data-ttu-id="20950-108">En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="20950-108">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="20950-109">Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.</span><span class="sxs-lookup"><span data-stu-id="20950-109">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="20950-110">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span><span class="sxs-lookup"><span data-stu-id="20950-110">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span></span>

## <a name="introduction"></a><span data-ttu-id="20950-111">Introducción</span><span class="sxs-lookup"><span data-stu-id="20950-111">Introduction</span></span>

<span data-ttu-id="20950-112">En el código siguiente se consulta una entidad y, a continuación, se intenta adjuntar una instancia diferente con el mismo valor de clave principal:</span><span class="sxs-lookup"><span data-stu-id="20950-112">The following code queries for an entity and then attempts to attach a different instance with the same primary key value:</span></span>

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

<span data-ttu-id="20950-113">La ejecución de este código produce la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="20950-113">Running this code results in the following exception:</span></span>

> <span data-ttu-id="20950-114">System. InvalidOperationException: no se puede realizar el seguimiento de la instancia del tipo de entidad ' blog ' porque ya se está realizando el seguimiento de otra instancia con el valor de clave ' {ID: 1} '.</span><span class="sxs-lookup"><span data-stu-id="20950-114">System.InvalidOperationException: The instance of entity type 'Blog' cannot be tracked because another instance with the key value '{Id: 1}' is already being tracked.</span></span> <span data-ttu-id="20950-115">Al adjuntar entidades existentes, asegúrese de que solo se adjunta una instancia de entidad con un valor de clave determinado.</span><span class="sxs-lookup"><span data-stu-id="20950-115">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="20950-116">EF Core requiere una única instancia porque:</span><span class="sxs-lookup"><span data-stu-id="20950-116">EF Core requires a single instance because:</span></span>

- <span data-ttu-id="20950-117">Los valores de propiedad pueden ser diferentes en varias instancias.</span><span class="sxs-lookup"><span data-stu-id="20950-117">Property values may be different between multiple instances.</span></span> <span data-ttu-id="20950-118">Al actualizar la base de datos, EF Core necesita saber qué valores de propiedad se deben usar.</span><span class="sxs-lookup"><span data-stu-id="20950-118">When updating the database, EF Core needs to know which property values to use.</span></span>
- <span data-ttu-id="20950-119">Las relaciones con otras entidades pueden ser diferentes entre varias instancias.</span><span class="sxs-lookup"><span data-stu-id="20950-119">Relationships with other entities may be different between multiple instances.</span></span> <span data-ttu-id="20950-120">Por ejemplo, "Bloga" puede estar relacionado con una colección diferente de entradas que "blogB".</span><span class="sxs-lookup"><span data-stu-id="20950-120">For example, "blogA" may be related to a different collection of posts than "blogB".</span></span>

<span data-ttu-id="20950-121">La excepción anterior se encuentra normalmente en estas situaciones:</span><span class="sxs-lookup"><span data-stu-id="20950-121">The exception above is commonly encountered in these situations:</span></span>

- <span data-ttu-id="20950-122">Al intentar actualizar una entidad</span><span class="sxs-lookup"><span data-stu-id="20950-122">When attempting to update an entity</span></span>
- <span data-ttu-id="20950-123">Al intentar realizar el seguimiento de un gráfico serializado de entidades</span><span class="sxs-lookup"><span data-stu-id="20950-123">When attempting to track a serialized graph of entities</span></span>
- <span data-ttu-id="20950-124">Cuando no se puede establecer un valor de clave que no se genera automáticamente</span><span class="sxs-lookup"><span data-stu-id="20950-124">When failing to set a key value that is not automatically generated</span></span>
- <span data-ttu-id="20950-125">Cuando se reutiliza una instancia de DbContext para varias unidades de trabajo</span><span class="sxs-lookup"><span data-stu-id="20950-125">When reusing a DbContext instance for multiple units-of-work</span></span>

<span data-ttu-id="20950-126">En las secciones siguientes se describe cada una de estas situaciones.</span><span class="sxs-lookup"><span data-stu-id="20950-126">Each of these situations is discussed in the following sections.</span></span>

## <a name="updating-an-entity"></a><span data-ttu-id="20950-127">Actualización de una entidad</span><span class="sxs-lookup"><span data-stu-id="20950-127">Updating an entity</span></span>

<span data-ttu-id="20950-128">Hay varios enfoques diferentes para actualizar una entidad con nuevos valores, como se describe en [Change Tracking en EF Core](xref:core/change-tracking/index) y [realizar un seguimiento explícito](xref:core/change-tracking/explicit-tracking)de las entidades.</span><span class="sxs-lookup"><span data-stu-id="20950-128">There are several different approaches to update an entity with new values, as covered in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking).</span></span> <span data-ttu-id="20950-129">Estos enfoques se describen a continuación en el contexto de la resolución de identidades.</span><span class="sxs-lookup"><span data-stu-id="20950-129">These approaches are outlined below in the context of identity resolution.</span></span> <span data-ttu-id="20950-130">Un aspecto importante que se debe tener en cuenta es que cada uno de los enfoques utiliza una consulta o una llamada a uno de `Update` o `Attach` , pero **_nunca a ambos_**.</span><span class="sxs-lookup"><span data-stu-id="20950-130">An important point to notice is that each of the approaches use either a query or a call to one of `Update` or `Attach`, but **_never both_**.</span></span>

### <a name="call-update"></a><span data-ttu-id="20950-131">Actualizar llamada</span><span class="sxs-lookup"><span data-stu-id="20950-131">Call Update</span></span>

<span data-ttu-id="20950-132">A menudo, la entidad que se va a actualizar no procede de una consulta en DbContext que vamos a usar para SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="20950-132">Often the entity to update does not come from a query on the DbContext that we are going to use for SaveChanges.</span></span> <span data-ttu-id="20950-133">Por ejemplo, en una aplicación Web, se puede crear una instancia de la entidad a partir de la información de una solicitud POST.</span><span class="sxs-lookup"><span data-stu-id="20950-133">For example, in a web application, an entity instance may be created from the information in a POST request.</span></span> <span data-ttu-id="20950-134">La manera más sencilla de controlarlo es usar <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> o <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="20950-134">The simplest way to handle this is to use <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="20950-135">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-135">For example:</span></span>

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

<span data-ttu-id="20950-136">En este caso:</span><span class="sxs-lookup"><span data-stu-id="20950-136">In this case:</span></span>

- <span data-ttu-id="20950-137">Solo se crea una instancia de la entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-137">Only a single instance of the entity is created.</span></span>
- <span data-ttu-id="20950-138">La instancia de la entidad **no** se consulta desde la base de datos como parte de la realización de la actualización.</span><span class="sxs-lookup"><span data-stu-id="20950-138">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="20950-139">Todos los valores de propiedad se actualizarán en la base de datos, independientemente de si realmente han cambiado o no.</span><span class="sxs-lookup"><span data-stu-id="20950-139">All property values will be updated in the database, regardless of whether they have actually changed or not.</span></span>
- <span data-ttu-id="20950-140">Se realiza una operación de ida y vuelta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="20950-140">One database round-trip is made.</span></span>

### <a name="query-then-apply-changes"></a><span data-ttu-id="20950-141">Después, la consulta aplica los cambios</span><span class="sxs-lookup"><span data-stu-id="20950-141">Query then apply changes</span></span>

<span data-ttu-id="20950-142">Normalmente, no se sabe qué valores de propiedad se han cambiado realmente cuando se crea una entidad a partir de información en una solicitud POST o similar.</span><span class="sxs-lookup"><span data-stu-id="20950-142">Usually it is not known which property values have actually been changed when an entity is created from information in a POST request or similar.</span></span> <span data-ttu-id="20950-143">A menudo, basta con actualizar todos los valores de la base de datos, como hicimos en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="20950-143">Often it is fine to just update all values in the database, as we did in the previous example.</span></span> <span data-ttu-id="20950-144">Sin embargo, si la aplicación administra muchas entidades y solo un número pequeño de ellos tiene cambios reales, puede ser útil limitar las actualizaciones enviadas.</span><span class="sxs-lookup"><span data-stu-id="20950-144">However, if the application is handling many entities and only a small number of those have actual changes, then it may be useful to limit the updates sent.</span></span> <span data-ttu-id="20950-145">Esto puede lograrse mediante la ejecución de una consulta para realizar el seguimiento de las entidades tal como existen actualmente en la base de datos y, a continuación, aplicar los cambios a estas entidades sometidas a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="20950-145">This can be achieved by executing a query to track the entities as they currently exist in the database, and then applying changes to these tracked entities.</span></span> <span data-ttu-id="20950-146">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-146">For example:</span></span>

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

<span data-ttu-id="20950-147">En este caso:</span><span class="sxs-lookup"><span data-stu-id="20950-147">In this case:</span></span>

- <span data-ttu-id="20950-148">Solo se realiza el seguimiento de una única instancia de la entidad; el devuelto por la consulta por la base de datos `Find` .</span><span class="sxs-lookup"><span data-stu-id="20950-148">Only a single instance of the entity is tracked; the one that is returned from the database by the `Find` query.</span></span>
- <span data-ttu-id="20950-149">`Update`, `Attach` ,, etc. **no** se usan.</span><span class="sxs-lookup"><span data-stu-id="20950-149">`Update`, `Attach`, etc. are **not** used.</span></span>
- <span data-ttu-id="20950-150">En la base de datos solo se actualizan los valores de propiedad que han cambiado realmente.</span><span class="sxs-lookup"><span data-stu-id="20950-150">Only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="20950-151">Se realizan dos viajes de ida y vuelta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="20950-151">Two database round-trips are made.</span></span>

<span data-ttu-id="20950-152">EF Core tiene algunas aplicaciones auxiliares para transferir valores de propiedad como esta.</span><span class="sxs-lookup"><span data-stu-id="20950-152">EF Core has some helpers for transferring property values like this.</span></span> <span data-ttu-id="20950-153">Por ejemplo, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> copiará todos los valores del objeto especificado y los establecerá en el objeto sometido a seguimiento:</span><span class="sxs-lookup"><span data-stu-id="20950-153">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> will copy all the values from the given object and set them on the tracked object:</span></span>

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

<span data-ttu-id="20950-154">`SetValues` acepta varios tipos de objetos, incluidos los objetos de transferencia de datos (DTO) con nombres de propiedad que coinciden con las propiedades del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-154">`SetValues` accepts various object types, including data transfer objects (DTOs) with property names that match the properties of the entity type.</span></span> <span data-ttu-id="20950-155">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-155">For example:</span></span>

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

<span data-ttu-id="20950-156">O un diccionario con entradas de nombre/valor para los valores de propiedad:</span><span class="sxs-lookup"><span data-stu-id="20950-156">Or a dictionary with name/value entries for the property values:</span></span>

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

<span data-ttu-id="20950-157">Vea obtener acceso a entidades de las que se ha [realizado un seguimiento](xref:core/change-tracking/entity-entries) para obtener más información sobre cómo trabajar con valores de propiedad como este.</span><span class="sxs-lookup"><span data-stu-id="20950-157">See [Accessing tracked entities](xref:core/change-tracking/entity-entries) for more information on working with property values like this.</span></span>

### <a name="use-original-values"></a><span data-ttu-id="20950-158">Usar valores originales</span><span class="sxs-lookup"><span data-stu-id="20950-158">Use original values</span></span>

<span data-ttu-id="20950-159">Hasta ahora, cada enfoque ha ejecutado una consulta antes de realizar la actualización o ha actualizado todos los valores de propiedad independientemente de si han cambiado o no.</span><span class="sxs-lookup"><span data-stu-id="20950-159">So far each approach has either executed a query before making the update, or updated all property values regardless of whether or not they have changed.</span></span> <span data-ttu-id="20950-160">Para actualizar solo los valores que han cambiado sin realizar consultas como parte de la actualización, se requiere información específica sobre los valores de propiedad que han cambiado.</span><span class="sxs-lookup"><span data-stu-id="20950-160">To update only values that have changed without querying as part of the update requires specific information about which property values have changed.</span></span> <span data-ttu-id="20950-161">Una forma habitual de obtener esta información es devolver los valores actuales y originales en HTTP POST o similar.</span><span class="sxs-lookup"><span data-stu-id="20950-161">A common way to get this information is to send back both the current and original values in the HTTP Post or similar.</span></span> <span data-ttu-id="20950-162">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-162">For example:</span></span>

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

<span data-ttu-id="20950-163">En este código, la entidad con valores modificados se adjunta primero.</span><span class="sxs-lookup"><span data-stu-id="20950-163">In this code the entity with modified values is first attached.</span></span> <span data-ttu-id="20950-164">Esto hace que EF Core realice el seguimiento de la entidad en el `Unchanged` Estado; es decir, sin valores de propiedad marcados como modificados.</span><span class="sxs-lookup"><span data-stu-id="20950-164">This causes EF Core to track the entity in the `Unchanged` state; that is, with no property values marked as modified.</span></span> <span data-ttu-id="20950-165">A continuación, el Diccionario de valores originales se aplica a esta entidad sometida a seguimiento.</span><span class="sxs-lookup"><span data-stu-id="20950-165">The dictionary of original values is then applied to this tracked entity.</span></span> <span data-ttu-id="20950-166">Esto marcará como propiedades modificadas con distintos valores actuales y originales.</span><span class="sxs-lookup"><span data-stu-id="20950-166">This will mark as modified properties with different current and original values.</span></span> <span data-ttu-id="20950-167">Las propiedades que tienen los mismos valores actual y original no se marcan como modificadas.</span><span class="sxs-lookup"><span data-stu-id="20950-167">Properties that have the same current and original values will not be marked as modified.</span></span>

<span data-ttu-id="20950-168">En este caso:</span><span class="sxs-lookup"><span data-stu-id="20950-168">In this case:</span></span>

- <span data-ttu-id="20950-169">Solo se realiza el seguimiento de una sola instancia de la entidad, mediante Attach.</span><span class="sxs-lookup"><span data-stu-id="20950-169">Only a single instance of the entity is tracked, using Attach.</span></span>
- <span data-ttu-id="20950-170">La instancia de la entidad **no** se consulta desde la base de datos como parte de la realización de la actualización.</span><span class="sxs-lookup"><span data-stu-id="20950-170">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="20950-171">La aplicación de los valores originales garantiza que solo se actualicen en la base de datos los valores de propiedad que hayan cambiado realmente.</span><span class="sxs-lookup"><span data-stu-id="20950-171">Applying the original values ensures that only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="20950-172">Se realiza una operación de ida y vuelta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="20950-172">One database round-trip is made.</span></span>

<span data-ttu-id="20950-173">Al igual que con los ejemplos de la sección anterior, los valores originales no tienen que pasarse como un diccionario; también funcionará una instancia de entidad o DTO.</span><span class="sxs-lookup"><span data-stu-id="20950-173">As with the examples in the previous section, the original values do not have to passed as a dictionary; an entity instance or DTO will also work.</span></span>

> [!TIP]
> <span data-ttu-id="20950-174">Aunque este enfoque tiene características atractivas, requiere el envío de los valores originales de la entidad al cliente web y desde él.</span><span class="sxs-lookup"><span data-stu-id="20950-174">While this approach has appealing characteristics, it does require sending the entity's original values to and from the web client.</span></span> <span data-ttu-id="20950-175">Considere detenidamente si esta complejidad adicional merece las ventajas; para muchas aplicaciones, uno de los enfoques más sencillos es más pragmático.</span><span class="sxs-lookup"><span data-stu-id="20950-175">Carefully consider whether this extra complexity is worth the benefits; for many applications one of the simpler approaches is more pragmatic.</span></span>

## <a name="attaching-a-serialized-graph"></a><span data-ttu-id="20950-176">Asociar un gráfico serializado</span><span class="sxs-lookup"><span data-stu-id="20950-176">Attaching a serialized graph</span></span>

<span data-ttu-id="20950-177">EF Core funciona con gráficos de entidades conectadas a través de claves externas y propiedades de navegación, como se describe en [cambiar las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="20950-177">EF Core works with graphs of entities connected via foreign keys and navigation properties, as described in [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="20950-178">Si estos gráficos se crean fuera de EF Core mediante, por ejemplo, de un archivo JSON, pueden tener varias instancias de la misma entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-178">If these graphs are created outside of EF Core using, for example, from a JSON file, then they can have multiple instances of the same entity.</span></span> <span data-ttu-id="20950-179">Estos duplicados deben resolverse en instancias únicas antes de que se pueda realizar el seguimiento del gráfico.</span><span class="sxs-lookup"><span data-stu-id="20950-179">These duplicates need to be resolved into single instances before the graph can be tracked.</span></span>

### <a name="graphs-with-no-duplicates"></a><span data-ttu-id="20950-180">Gráficos sin duplicados</span><span class="sxs-lookup"><span data-stu-id="20950-180">Graphs with no duplicates</span></span>

<span data-ttu-id="20950-181">Antes de continuar, es importante reconocer que:</span><span class="sxs-lookup"><span data-stu-id="20950-181">Before going any further it is important to recognize that:</span></span>

- <span data-ttu-id="20950-182">Los serializadores suelen tener opciones para administrar bucles e instancias duplicadas en el gráfico.</span><span class="sxs-lookup"><span data-stu-id="20950-182">Serializers often have options for handling loops and duplicate instances in the graph.</span></span>
- <span data-ttu-id="20950-183">La elección del objeto que se usa como la raíz del gráfico puede ayudar a reducir o quitar los duplicados.</span><span class="sxs-lookup"><span data-stu-id="20950-183">The choice of object used as the graph root can often help reduce or remove duplicates.</span></span>

<span data-ttu-id="20950-184">Si es posible, use las opciones de serialización y elija raíces que no produzcan duplicados.</span><span class="sxs-lookup"><span data-stu-id="20950-184">If possible, use serialization options and choose roots that do not result in duplicates.</span></span> <span data-ttu-id="20950-185">Por ejemplo, el código siguiente usa [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/) para serializar una lista de blogs cada uno con sus entradas asociadas:</span><span class="sxs-lookup"><span data-stu-id="20950-185">For example, the following code uses [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) to serialize a list of blogs each with its associated posts:</span></span>

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

<span data-ttu-id="20950-186">El JSON generado a partir de este código es:</span><span class="sxs-lookup"><span data-stu-id="20950-186">The JSON generated from this code is:</span></span>

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

<span data-ttu-id="20950-187">Tenga en cuenta que no hay ningún blog o publicación duplicados en el archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="20950-187">Notice that there are no duplicate blogs or posts in the JSON.</span></span> <span data-ttu-id="20950-188">Esto significa que las llamadas simples a `Update` funcionarán para actualizar estas entidades en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="20950-188">This means that simple calls to `Update` will work to update these entities in the database:</span></span>

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a><span data-ttu-id="20950-189">Administrar duplicados</span><span class="sxs-lookup"><span data-stu-id="20950-189">Handling duplicates</span></span>

<span data-ttu-id="20950-190">El código del ejemplo anterior serializaba cada blog con sus entradas asociadas.</span><span class="sxs-lookup"><span data-stu-id="20950-190">The code in the previous example serialized each blog with its associated posts.</span></span> <span data-ttu-id="20950-191">Si se cambia para serializar cada publicación con su blog asociado, los duplicados se introducen en el JSON serializado.</span><span class="sxs-lookup"><span data-stu-id="20950-191">If this is changed to serialize each post with its associated blog, then duplicates are introduced into the serialized JSON.</span></span> <span data-ttu-id="20950-192">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

<span data-ttu-id="20950-193">El JSON serializado ahora tiene el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="20950-193">The serialized JSON now looks like this:</span></span>

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

<span data-ttu-id="20950-194">Observe que el gráfico ahora incluye varias instancias de blog con el mismo valor de clave, así como varias instancias posteriores con el mismo valor de clave.</span><span class="sxs-lookup"><span data-stu-id="20950-194">Notice that the graph now includes multiple Blog instances with the same key value, as well as multiple Post instance with the same key value.</span></span> <span data-ttu-id="20950-195">Al intentar realizar un seguimiento de este gráfico como hicimos en el ejemplo anterior, se producirá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="20950-195">Attempting to track this graph like we did in the previous example will throw:</span></span>

> <span data-ttu-id="20950-196">System. InvalidOperationException: no se puede realizar el seguimiento de la instancia del tipo de entidad ' post ' porque ya se está realizando el seguimiento de otra instancia con el valor de clave ' {ID: 2} '.</span><span class="sxs-lookup"><span data-stu-id="20950-196">System.InvalidOperationException: The instance of entity type 'Post' cannot be tracked because another instance with the key value '{Id: 2}' is already being tracked.</span></span> <span data-ttu-id="20950-197">Al adjuntar entidades existentes, asegúrese de que solo se adjunta una instancia de entidad con un valor de clave determinado.</span><span class="sxs-lookup"><span data-stu-id="20950-197">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="20950-198">Podemos corregir esto de dos maneras:</span><span class="sxs-lookup"><span data-stu-id="20950-198">We can fix this in two ways:</span></span>

- <span data-ttu-id="20950-199">Usar opciones de serialización de JSON que conserven las referencias</span><span class="sxs-lookup"><span data-stu-id="20950-199">Use JSON serialization options that preserve references</span></span>
- <span data-ttu-id="20950-200">Realizar la resolución de identidad mientras se realiza el seguimiento del gráfico</span><span class="sxs-lookup"><span data-stu-id="20950-200">Perform identity resolution while the graph is being tracked</span></span>

#### <a name="preserve-references"></a><span data-ttu-id="20950-201">Conservación de las referencias</span><span class="sxs-lookup"><span data-stu-id="20950-201">Preserve references</span></span>

<span data-ttu-id="20950-202">Json.NET ofrece la `PreserveReferencesHandling` opción de controlar esto.</span><span class="sxs-lookup"><span data-stu-id="20950-202">Json.NET provides the `PreserveReferencesHandling` option to handle this.</span></span> <span data-ttu-id="20950-203">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-203">For example:</span></span>

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

<span data-ttu-id="20950-204">El JSON resultante tiene ahora el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="20950-204">The resulting JSON now looks like this:</span></span>

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

<span data-ttu-id="20950-205">Tenga en cuenta que este JSON ha reemplazado duplicados con referencias como `"$ref": "5"` que hacen referencia a la instancia ya existente en el gráfico.</span><span class="sxs-lookup"><span data-stu-id="20950-205">Notice that this JSON has replaced duplicates with references like `"$ref": "5"` that refer to the already existing instance in the graph.</span></span> <span data-ttu-id="20950-206">Se puede realizar un seguimiento de este gráfico mediante las llamadas simples a `Update` , como se muestra anteriormente.</span><span class="sxs-lookup"><span data-stu-id="20950-206">This graph can again be tracked using the simple calls to `Update`, as shown above.</span></span>

<span data-ttu-id="20950-207">La <xref:System.Text.Json> compatibilidad de las bibliotecas de clases base (BCL) de .net tiene una opción similar que produce el mismo resultado.</span><span class="sxs-lookup"><span data-stu-id="20950-207">The <xref:System.Text.Json> support in the .NET base class libraries (BCL) has a similar option which produces the same result.</span></span> <span data-ttu-id="20950-208">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-208">For example:</span></span>

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a><span data-ttu-id="20950-209">Resolver duplicados</span><span class="sxs-lookup"><span data-stu-id="20950-209">Resolve duplicates</span></span>

<span data-ttu-id="20950-210">Si no es posible eliminar duplicados en el proceso de serialización, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> proporciona una manera de controlar esto.</span><span class="sxs-lookup"><span data-stu-id="20950-210">If it is not possible to eliminate duplicates in the serialization process, then <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> provides a way to handle this.</span></span> <span data-ttu-id="20950-211">TrackGraph funciona como `Add` `Attach` y, `Update` salvo que genera una devolución de llamada para cada instancia de entidad antes de realizar el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="20950-211">TrackGraph works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="20950-212">Esta devolución de llamada se puede usar para realizar un seguimiento de la entidad u omitirla.</span><span class="sxs-lookup"><span data-stu-id="20950-212">This callback can be used to either track the entity or ignore it.</span></span> <span data-ttu-id="20950-213">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-213">For example:</span></span>

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

<span data-ttu-id="20950-214">Para cada entidad del gráfico, este código hará lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="20950-214">For each entity in the graph, this code will:</span></span>

- <span data-ttu-id="20950-215">Buscar el tipo de entidad y el valor de clave de la entidad</span><span class="sxs-lookup"><span data-stu-id="20950-215">Find the entity type and key value of the entity</span></span>
- <span data-ttu-id="20950-216">Buscar la entidad con esta clave en el seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="20950-216">Lookup the entity with this key in the change tracker</span></span>
  - <span data-ttu-id="20950-217">Si se encuentra la entidad, no se realiza ninguna otra acción, ya que la entidad es un duplicado.</span><span class="sxs-lookup"><span data-stu-id="20950-217">If the entity is found, then no further action is taken as the entity is a duplicate</span></span>
  - <span data-ttu-id="20950-218">Si no se encuentra la entidad, se realiza el seguimiento estableciendo el estado en `Modified`</span><span class="sxs-lookup"><span data-stu-id="20950-218">If the entity is not found, then it is tracked by setting the state to `Modified`</span></span>

<span data-ttu-id="20950-219">La salida de la ejecución de este código es:</span><span class="sxs-lookup"><span data-stu-id="20950-219">The output from running this code is:</span></span>

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> <span data-ttu-id="20950-220">En este código **se supone que todos los duplicados son idénticos**.</span><span class="sxs-lookup"><span data-stu-id="20950-220">This code **assumes that all duplicates are identical**.</span></span> <span data-ttu-id="20950-221">Esto hace que sea seguro elegir arbitrariamente uno de los duplicados para realizar el seguimiento mientras se descartan los demás.</span><span class="sxs-lookup"><span data-stu-id="20950-221">This makes it safe to arbitrarily choose one of the duplicates to track while discarding the others.</span></span> <span data-ttu-id="20950-222">Si los duplicados pueden diferir, el código deberá decidir cómo determinar cuál usar y cómo combinar los valores de propiedad y navegación juntos.</span><span class="sxs-lookup"><span data-stu-id="20950-222">If the duplicates can differ, then the code will need to decide how to determine which one to use, and how to combine property and navigation values together.</span></span>

> [!NOTE]
> <span data-ttu-id="20950-223">Para simplificar, en este código se supone que cada entidad tiene una propiedad de clave principal denominada `Id` .</span><span class="sxs-lookup"><span data-stu-id="20950-223">For simplicity, this code assumes each entity has a primary key property called `Id`.</span></span> <span data-ttu-id="20950-224">Esto se podría codificar en una interfaz o clase base abstracta.</span><span class="sxs-lookup"><span data-stu-id="20950-224">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="20950-225">Como alternativa, la propiedad o propiedades de la clave principal se pueden obtener a partir de los <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadatos de modo que este código funcione con cualquier tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-225">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

## <a name="failing-to-set-key-values"></a><span data-ttu-id="20950-226">No se pueden establecer los valores de clave</span><span class="sxs-lookup"><span data-stu-id="20950-226">Failing to set key values</span></span>

<span data-ttu-id="20950-227">Los tipos de entidad se configuran a menudo para usar [valores de clave generados automáticamente](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="20950-227">Entity types are often configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="20950-228">Este es el valor predeterminado para las propiedades Integer y GUID de las claves no compuestas.</span><span class="sxs-lookup"><span data-stu-id="20950-228">This is the default for integer and GUID properties of non-composite keys.</span></span> <span data-ttu-id="20950-229">Sin embargo, si el tipo de entidad no está configurado para usar los valores de clave generados automáticamente, debe establecerse un valor de clave explícito antes de realizar el seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-229">However, if the entity type is not configured to use automatically generated key values, then an explicit key value must be set before tracking the entity.</span></span> <span data-ttu-id="20950-230">Por ejemplo, con el siguiente tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="20950-230">For example, using the following entity type:</span></span>

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

<span data-ttu-id="20950-231">Considere el código que intenta rastrear dos nuevas instancias de entidad sin establecer los valores de clave:</span><span class="sxs-lookup"><span data-stu-id="20950-231">Consider code that attempts to tracker two new entity instances without setting key values:</span></span>

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

<span data-ttu-id="20950-232">Este código producirá:</span><span class="sxs-lookup"><span data-stu-id="20950-232">This code will throw:</span></span>

> <span data-ttu-id="20950-233">System. InvalidOperationException: no se puede realizar el seguimiento de la instancia del tipo de entidad ' PET ' porque ya se está realizando el seguimiento de otra instancia con el valor de clave ' {ID: 0} '.</span><span class="sxs-lookup"><span data-stu-id="20950-233">System.InvalidOperationException: The instance of entity type 'Pet' cannot be tracked because another instance with the key value '{Id: 0}' is already being tracked.</span></span> <span data-ttu-id="20950-234">Al adjuntar entidades existentes, asegúrese de que solo se adjunta una instancia de entidad con un valor de clave determinado.</span><span class="sxs-lookup"><span data-stu-id="20950-234">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="20950-235">La solución para esto es para establecer los valores de clave explícitamente o configurar la propiedad clave para usar los valores de clave generados.</span><span class="sxs-lookup"><span data-stu-id="20950-235">The fix for this is to either to set key values explicitly or configure the key property to use generated key values.</span></span> <span data-ttu-id="20950-236">Vea [valores generados](xref:core/modeling/generated-properties) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="20950-236">See [Generated Values](xref:core/modeling/generated-properties) for more information.</span></span>

## <a name="overusing-a-single-dbcontext-instance"></a><span data-ttu-id="20950-237">Sobreutilización de una única instancia de DbContext</span><span class="sxs-lookup"><span data-stu-id="20950-237">Overusing a single DbContext instance</span></span>

<span data-ttu-id="20950-238"><xref:Microsoft.EntityFrameworkCore.DbContext> está diseñado para representar una unidad de trabajo de corta duración, como se describe en la [configuración y la inicialización de DbContext](xref:core/dbcontext-configuration/index), y se ha elaborado en [Change Tracking en EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="20950-238"><xref:Microsoft.EntityFrameworkCore.DbContext> is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span> <span data-ttu-id="20950-239">No seguir esta guía facilita la ejecución en situaciones en las que se realiza un intento de realizar un seguimiento de varias instancias de la misma entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-239">Not following this guidance makes it is easy to run into situations where an attempt is made to track multiple instances of the same entity.</span></span> <span data-ttu-id="20950-240">Los ejemplos comunes son:</span><span class="sxs-lookup"><span data-stu-id="20950-240">Common examples are:</span></span>

- <span data-ttu-id="20950-241">Usar la misma instancia de DbContext para configurar el estado de prueba y, a continuación, ejecutar la prueba.</span><span class="sxs-lookup"><span data-stu-id="20950-241">Using the same DbContext instance to both set up test state and then execute the test.</span></span> <span data-ttu-id="20950-242">Esto suele hacer que DbContext siga realizando el seguimiento de una instancia de la entidad a partir de la configuración de pruebas, mientras que después intenta adjuntar una nueva instancia de la prueba adecuada.</span><span class="sxs-lookup"><span data-stu-id="20950-242">This often results in the DbContext still tracking one entity instance from test setup, while then attempting to attach a new instance in the test proper.</span></span> <span data-ttu-id="20950-243">En su lugar, use una instancia de DbContext diferente para configurar el estado de prueba y el código de prueba adecuado.</span><span class="sxs-lookup"><span data-stu-id="20950-243">Instead, use a different DbContext instance for setting up test state and the test code proper.</span></span>
- <span data-ttu-id="20950-244">Usar una instancia compartida DbContext en un repositorio o código similar.</span><span class="sxs-lookup"><span data-stu-id="20950-244">Using a shared DbContext instance in a repository or similar code.</span></span> <span data-ttu-id="20950-245">En su lugar, asegúrese de que el repositorio usa una única instancia de DbContext para cada unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="20950-245">Instead, make sure your repository uses a single DbContext instance for each unit-of-work.</span></span>

## <a name="identity-resolution-and-queries"></a><span data-ttu-id="20950-246">Resolución y consultas de identidad</span><span class="sxs-lookup"><span data-stu-id="20950-246">Identity resolution and queries</span></span>

<span data-ttu-id="20950-247">La resolución de identidad se produce automáticamente cuando se realiza un seguimiento de las entidades desde una consulta.</span><span class="sxs-lookup"><span data-stu-id="20950-247">Identity resolution happens automatically when entities are tracked from a query.</span></span> <span data-ttu-id="20950-248">Esto significa que, si ya se ha realizado un seguimiento de una instancia de una entidad con un valor de clave determinado, se usará esta instancia de seguimiento existente en lugar de crear una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="20950-248">This means that if an entity instance with a given key value is already tracked, then this existing tracked instance is used instead of creating a new instance.</span></span> <span data-ttu-id="20950-249">Esto tiene una consecuencia importante: si los datos han cambiado en la base de datos, no se reflejarán en los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="20950-249">This has an important consequence: if the data has changed in the database, then this will not be reflected in the results of the query.</span></span> <span data-ttu-id="20950-250">Esta es una buena razón para usar una nueva instancia de DbContext para cada unidad de trabajo, como se describe en la [configuración y la inicialización de dbcontext](xref:core/dbcontext-configuration/index), y se ha elaborado en [Change Tracking en EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="20950-250">This is a good reason to use a new DbContext instance for each unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="20950-251">Es importante entender que EF Core siempre ejecuta una consulta LINQ en un DbSet en la base de datos y solo devuelve resultados en función de lo que hay en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="20950-251">It is important to understand that EF Core always executes a LINQ query on a DbSet against the database and only returns results based on what is in the database.</span></span> <span data-ttu-id="20950-252">Sin embargo, para una consulta de seguimiento, si ya se realiza el seguimiento de las entidades devueltas, se utilizan las instancias de las que se ha realizado un seguimiento en lugar de crear instancias a partir de los datos de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="20950-252">However, for a tracking query, if the entities returned are already tracked, then the tracked instances are used instead of creating a instances from the data in the database.</span></span>

<span data-ttu-id="20950-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> o <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> se puede utilizar cuando es necesario actualizar las entidades de las que se realiza un seguimiento con los datos más recientes de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="20950-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> can be used when tracked entities need to be refreshed with the latest data from the database.</span></span> <span data-ttu-id="20950-254">Consulte [acceso a entidades](xref:core/change-tracking/entity-entries) de las que se ha realizado un seguimiento para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="20950-254">See [Accessing Tracked Entities](xref:core/change-tracking/entity-entries) for more information.</span></span>

<span data-ttu-id="20950-255">A diferencia de las consultas de seguimiento, las consultas sin seguimiento no realizan la resolución de identidades.</span><span class="sxs-lookup"><span data-stu-id="20950-255">In contrast to tracking queries, no-tracking queries do not perform identity resolution.</span></span> <span data-ttu-id="20950-256">Esto significa que las consultas sin seguimiento pueden devolver duplicados como en el caso de serialización de JSON descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="20950-256">This means that no-tracking queries can return duplicates just like in the JSON serialization case described earlier.</span></span> <span data-ttu-id="20950-257">Esto no suele ser un problema si los resultados de la consulta se van a serializar y enviar al cliente.</span><span class="sxs-lookup"><span data-stu-id="20950-257">This is usually not an issue if the query results are going to be serialized and sent to the client.</span></span>

> [!TIP]
> <span data-ttu-id="20950-258">No realice habitualmente una consulta sin seguimiento y, a continuación, asocie las entidades devueltas al mismo contexto.</span><span class="sxs-lookup"><span data-stu-id="20950-258">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="20950-259">Esto será más lento y más difícil de conseguir que el uso de una consulta de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="20950-259">This will be both slower and harder to get right than using a tracking query.</span></span>

<span data-ttu-id="20950-260">Las consultas sin seguimiento no realizan la resolución de identidad porque esto afecta al rendimiento de la transmisión por secuencias de un gran número de entidades de una consulta.</span><span class="sxs-lookup"><span data-stu-id="20950-260">No-tracking queries do not perform identity resolution because doing so impacts the performance of streaming a large number of entities from a query.</span></span> <span data-ttu-id="20950-261">Esto se debe a que la resolución de identidad requiere realizar un seguimiento de cada instancia devuelta para que se pueda usar en lugar de crear posteriormente un duplicado.</span><span class="sxs-lookup"><span data-stu-id="20950-261">This is because identity resolution requires keeping track of each instance returned so that it can be used instead of later creating a duplicate.</span></span>

<span data-ttu-id="20950-262">A partir de EF Core 5,0, las consultas sin seguimiento se pueden forzar para realizar la resolución de identidades mediante <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> .</span><span class="sxs-lookup"><span data-stu-id="20950-262">Starting with EF Core 5.0, no-tracking queries can be forced to perform identity resolution by using <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})>.</span></span> <span data-ttu-id="20950-263">La consulta realizará un seguimiento de las instancias devueltas (sin realizar el seguimiento de forma normal) y garantiza que no se crean duplicados en los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="20950-263">The query will then keep track of returned instances (without tracking them in the normal way) and ensure no duplicates are created in the query results.</span></span>

## <a name="overriding-object-equality"></a><span data-ttu-id="20950-264">Reemplazar la igualdad de objetos</span><span class="sxs-lookup"><span data-stu-id="20950-264">Overriding object equality</span></span>

<span data-ttu-id="20950-265">EF Core usa la [igualdad de referencia](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) al comparar instancias de entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-265">EF Core uses [reference equality](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) when comparing entity instances.</span></span> <span data-ttu-id="20950-266">Este es el caso incluso si los tipos de entidad invalidan <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> o cambian la igualdad de objetos.</span><span class="sxs-lookup"><span data-stu-id="20950-266">This is the case even if the entity types override <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> or otherwise change object equality.</span></span> <span data-ttu-id="20950-267">Sin embargo, hay un lugar donde la igualdad de invalidación puede afectar al comportamiento de EF Core: cuando las navegaciones de la colección usan la igualdad invalidada en lugar de la igualdad de la referencia y, por lo tanto, notifican varias instancias como iguales.</span><span class="sxs-lookup"><span data-stu-id="20950-267">However, there is one place where overriding equality can impact EF Core behavior: when collection navigations use the overridden equality instead of reference equality, and hence report multiple instances as the same.</span></span>

<span data-ttu-id="20950-268">Debido a esto, se recomienda evitar la igualdad de la entidad.</span><span class="sxs-lookup"><span data-stu-id="20950-268">Because of this it is recommended that overriding entity equality should be avoided.</span></span> <span data-ttu-id="20950-269">Si se usa, asegúrese de crear navegaciones de colección que fuercen la igualdad de referencia.</span><span class="sxs-lookup"><span data-stu-id="20950-269">If it is used, then make sure to create collection navigations that force reference equality.</span></span> <span data-ttu-id="20950-270">Por ejemplo, cree un comparador de igualdad que use la igualdad de referencia:</span><span class="sxs-lookup"><span data-stu-id="20950-270">For example, create an equality comparer that uses reference equality:</span></span>

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

<span data-ttu-id="20950-271">(A partir de .NET 5, esto se incluye en la BCL como <xref:System.Collections.Generic.ReferenceEqualityComparer> ).</span><span class="sxs-lookup"><span data-stu-id="20950-271">(Starting with .NET 5, this is included in the BCL as <xref:System.Collections.Generic.ReferenceEqualityComparer>.)</span></span>

<span data-ttu-id="20950-272">Este comparador se puede usar al crear navegaciones de colección.</span><span class="sxs-lookup"><span data-stu-id="20950-272">This comparer can then be used when creating collection navigations.</span></span> <span data-ttu-id="20950-273">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20950-273">For example:</span></span>

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a><span data-ttu-id="20950-274">Comparar propiedades de clave</span><span class="sxs-lookup"><span data-stu-id="20950-274">Comparing key properties</span></span>

<span data-ttu-id="20950-275">Además de las comparaciones de igualdad, también es necesario ordenar los valores de clave.</span><span class="sxs-lookup"><span data-stu-id="20950-275">In addition to equality comparisons, key values also need to be ordered.</span></span> <span data-ttu-id="20950-276">Esto es importante para evitar los interbloqueos al actualizar varias entidades en una única llamada a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="20950-276">This is important for avoiding deadlocks when updating multiple entities in a single call to SaveChanges.</span></span> <span data-ttu-id="20950-277">Todos los tipos que se usan para las propiedades principales, alternativas o de clave externa, así como los que se usan para los índices únicos, deben implementar <xref:System.IComparable%601> y <xref:System.IEquatable%601> .</span><span class="sxs-lookup"><span data-stu-id="20950-277">All types used for primary, alternate, or foreign key properties, as well as those used for unique indexes, must implement <xref:System.IComparable%601> and <xref:System.IEquatable%601>.</span></span> <span data-ttu-id="20950-278">Los tipos que normalmente se usan como claves (int, GUID, String, etc.) ya admiten estas interfaces.</span><span class="sxs-lookup"><span data-stu-id="20950-278">Types normally used as keys (int, Guid, string, etc.) already support these interfaces.</span></span> <span data-ttu-id="20950-279">Los tipos de clave personalizados pueden agregar estas interfaces.</span><span class="sxs-lookup"><span data-stu-id="20950-279">Custom key types may to add these interfaces.</span></span>
