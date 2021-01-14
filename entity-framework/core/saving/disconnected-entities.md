---
title: 'Entidades desconectadas: EF Core'
description: Trabajo con entidades desconectadas y sin seguimiento en varias instancias de contexto en Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: 4ddae08b68aa99fb81de77fed8a195c60927e0bb
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128854"
---
# <a name="disconnected-entities"></a><span data-ttu-id="29c38-103">Entidades desconectadas</span><span class="sxs-lookup"><span data-stu-id="29c38-103">Disconnected entities</span></span>

<span data-ttu-id="29c38-104">Una DbContext realizará seguimiento automático de las entidades que se devuelven de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="29c38-104">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="29c38-105">De ese modo, los cambios hechos en estas entidades se detectarán cuando se llame a SaveChanges y la base de datos se actualizará según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="29c38-105">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="29c38-106">Consulte [Basic Save](xref:core/saving/basic) (Guardado básico) y [Related Data](xref:core/saving/related-data) (Datos relacionados) para información detallada.</span><span class="sxs-lookup"><span data-stu-id="29c38-106">See [Basic Save](xref:core/saving/basic) and [Related Data](xref:core/saving/related-data) for details.</span></span>

<span data-ttu-id="29c38-107">Sin embargo, en algunas ocasiones las entidades se consultan mediante el uso de una instancia de contexto y luego se guardan con una instancia distinta.</span><span class="sxs-lookup"><span data-stu-id="29c38-107">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="29c38-108">Esto suele ocurrir en escenarios "desconectados", como una aplicación web, en los que las entidades se consultan, se envían al cliente, se modifican, se envían de vuelta al servidor en una solicitud y, a continuación, se guardan.</span><span class="sxs-lookup"><span data-stu-id="29c38-108">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="29c38-109">En este caso, la segunda instancia de contexto debe saber si las entidades son nuevas (y se deben insertar) o existentes (y se deben actualizar).</span><span class="sxs-lookup"><span data-stu-id="29c38-109">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

<!-- markdownlint-disable MD028 -->
> [!TIP]
> <span data-ttu-id="29c38-110">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="29c38-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) on GitHub.</span></span>

> [!TIP]
> <span data-ttu-id="29c38-111">EF Core solo puede hacer seguimiento de una instancia de una entidad con un valor de clave principal determinado.</span><span class="sxs-lookup"><span data-stu-id="29c38-111">EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="29c38-112">La mejor manera de evitar que esto se convierta en un problema es usar un contexto de corta duración para cada unidad de trabajo de manera que el contexto empiece vacío, tenga entidades asociadas, guarde esas entidades y, luego, se elimine y descarte el contexto.</span><span class="sxs-lookup"><span data-stu-id="29c38-112">The best way to avoid this being an issue is to use a short-lived context for each unit-of-work such that the context starts empty, has entities attached to it, saves those entities, and then the context is disposed and discarded.</span></span>
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a><span data-ttu-id="29c38-113">Identificación de unidades nuevas</span><span class="sxs-lookup"><span data-stu-id="29c38-113">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="29c38-114">El cliente identifica las unidades nuevas</span><span class="sxs-lookup"><span data-stu-id="29c38-114">Client identifies new entities</span></span>

<span data-ttu-id="29c38-115">El caso más sencillo de abordar es cuando el cliente informa al servidor si la entidad es nueva o existente.</span><span class="sxs-lookup"><span data-stu-id="29c38-115">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="29c38-116">Por ejemplo, a menudo la solicitud para insertar una entidad nueva es distinta de la solicitud para actualizar una entidad existente.</span><span class="sxs-lookup"><span data-stu-id="29c38-116">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="29c38-117">En el resto de esta sección se analizan los casos en los que resulta necesario determinar de otro modo si se debe realizar una inserción o una actualización.</span><span class="sxs-lookup"><span data-stu-id="29c38-117">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="29c38-118">Con claves generadas automáticamente</span><span class="sxs-lookup"><span data-stu-id="29c38-118">With auto-generated keys</span></span>

<span data-ttu-id="29c38-119">El valor de una clave generada automáticamente a menudo se puede usar para determinar si una entidad se debe insertar o actualizar.</span><span class="sxs-lookup"><span data-stu-id="29c38-119">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="29c38-120">Si no se estableció la clave (es decir, si todavía tiene el valor predeterminado de CLR de NULL, cero, etc.), la entidad debe ser nueva y se debe insertar.</span><span class="sxs-lookup"><span data-stu-id="29c38-120">If the key has not been set (that is, it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="29c38-121">Por el contrario, si el valor de la clave sí se estableció, ya se debe haber guardado anteriormente y ahora se debe actualizar.</span><span class="sxs-lookup"><span data-stu-id="29c38-121">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="29c38-122">En otras palabras, si la clave tiene un valor, es porque la entidad ya se consultó, se envió al cliente y ahora vuelve para que la actualicen.</span><span class="sxs-lookup"><span data-stu-id="29c38-122">In other words, if the key has a value, then the entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="29c38-123">Resulta sencillo comprobar si una clave no se estableció cuando se conoce el tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="29c38-123">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="29c38-124">Sin embargo, EF también tiene una manera integrada de hacer esto con cualquier tipo de entidad y cualquier tipo de clave:</span><span class="sxs-lookup"><span data-stu-id="29c38-124">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]
> <span data-ttu-id="29c38-125">Las claves se establecen tan pronto como el contexto hace seguimiento de las entidades, incluso si la entidad tiene el estado Added (Agregada).</span><span class="sxs-lookup"><span data-stu-id="29c38-125">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="29c38-126">Esto resulta útil cuando se recorre un grafo de entidades y se decide qué hacer con cada una de ellas, como cuándo usar TrackGraph API.</span><span class="sxs-lookup"><span data-stu-id="29c38-126">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="29c38-127">El valor de la clave solo se debe usar como se indica aquí _antes_ de cualquier llamada para hacer seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="29c38-127">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="29c38-128">Con otras claves</span><span class="sxs-lookup"><span data-stu-id="29c38-128">With other keys</span></span>

<span data-ttu-id="29c38-129">Es necesario algún otro mecanismo para identificar las entidades nuevas cuando los valores de clave no se generan automáticamente.</span><span class="sxs-lookup"><span data-stu-id="29c38-129">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="29c38-130">Aquí existen dos enfoques generales:</span><span class="sxs-lookup"><span data-stu-id="29c38-130">There are two general approaches to this:</span></span>

* <span data-ttu-id="29c38-131">Consulta para la entidad</span><span class="sxs-lookup"><span data-stu-id="29c38-131">Query for the entity</span></span>
* <span data-ttu-id="29c38-132">Paso de una marca desde el cliente</span><span class="sxs-lookup"><span data-stu-id="29c38-132">Pass a flag from the client</span></span>

<span data-ttu-id="29c38-133">Para consulta la entidad, simplemente use el método Find:</span><span class="sxs-lookup"><span data-stu-id="29c38-133">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="29c38-134">Mostrar el código completo para pasar una marca desde un cliente va más allá del ámbito del presente documento.</span><span class="sxs-lookup"><span data-stu-id="29c38-134">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="29c38-135">En una aplicación web, habitualmente significa hacer distintas solicitudes para acciones diferentes o pasar algún estado en la solicitud para luego extraerlo en el controlador.</span><span class="sxs-lookup"><span data-stu-id="29c38-135">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="29c38-136">Guardado de entidades únicas</span><span class="sxs-lookup"><span data-stu-id="29c38-136">Saving single entities</span></span>

<span data-ttu-id="29c38-137">Cuando se sabe si es necesario o no realizar una inserción o una actualización, las acciones de agregar o actualizar se pueden usar correctamente:</span><span class="sxs-lookup"><span data-stu-id="29c38-137">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="29c38-138">Sin embargo, si la entidad usa valores de clave generados automáticamente, el método Update se puede usar para ambos casos:</span><span class="sxs-lookup"><span data-stu-id="29c38-138">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="29c38-139">Habitualmente, el método Update marca la entidad para actualización y no para inserción.</span><span class="sxs-lookup"><span data-stu-id="29c38-139">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="29c38-140">Sin embargo, si la entidad tiene una clave generada automáticamente y no se estableció ningún valor de clave, la entidad se marca automáticamente para inserción.</span><span class="sxs-lookup"><span data-stu-id="29c38-140">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

<span data-ttu-id="29c38-141">Si la entidad no usa claves generadas automáticamente, la aplicación debe decidir si la entidad se debe inserta ro actualizar. Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="29c38-141">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="29c38-142">Estos son los pasos:</span><span class="sxs-lookup"><span data-stu-id="29c38-142">The steps here are:</span></span>

* <span data-ttu-id="29c38-143">Si Find devuelve un valor NULL, la base de datos todavía no contiene el blog con su identificador, por lo que llamamos a Add para marcarlo para inserción.</span><span class="sxs-lookup"><span data-stu-id="29c38-143">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="29c38-144">Si Find devuelve una entidad es porque existe en la base de datos y ahora el contexto hace seguimiento de esa entidad existente.</span><span class="sxs-lookup"><span data-stu-id="29c38-144">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="29c38-145">Luego usamos SetValues para establecer los valores de todas las propiedades de esta entidad en los valores que provienen del cliente.</span><span class="sxs-lookup"><span data-stu-id="29c38-145">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="29c38-146">La llamada a SetValues marcará la entidad para actualizarla según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="29c38-146">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]
> <span data-ttu-id="29c38-147">SetValues solo marcará como modificadas las propiedades que tengan valores distintos a los de la entidad con seguimiento.</span><span class="sxs-lookup"><span data-stu-id="29c38-147">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="29c38-148">Esto significa que, cuando se envíe la actualización, solo se actualizarán las columnas que se hayan modificado realmente.</span><span class="sxs-lookup"><span data-stu-id="29c38-148">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="29c38-149">(Si no se modificó nada, no se enviará ninguna actualización).</span><span class="sxs-lookup"><span data-stu-id="29c38-149">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="29c38-150">Trabajo con grafos</span><span class="sxs-lookup"><span data-stu-id="29c38-150">Working with graphs</span></span>

### <a name="identity-resolution"></a><span data-ttu-id="29c38-151">Resolución de identidad</span><span class="sxs-lookup"><span data-stu-id="29c38-151">Identity resolution</span></span>

<span data-ttu-id="29c38-152">Como se indicó anteriormente, EF Core solo puede hacer seguimiento de una instancia de una entidad con un valor de clave principal determinado.</span><span class="sxs-lookup"><span data-stu-id="29c38-152">As noted above, EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="29c38-153">Cuando se trabaja con grafos, idealmente el grafo se debe crear de manera tal que se mantenga esta invariable y el contexto se debe usar solo para una unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="29c38-153">When working with graphs the graph should ideally be created such that this invariant is maintained, and the context should be used for only one unit-of-work.</span></span> <span data-ttu-id="29c38-154">Si el grafo contiene duplicados, será necesario procesarlo antes de enviarlo a EF para consolidar varias instancias en una sola.</span><span class="sxs-lookup"><span data-stu-id="29c38-154">If the graph does contain duplicates, then it will be necessary to process the graph before sending it to EF to consolidate multiple instances into one.</span></span> <span data-ttu-id="29c38-155">Es posible que esta acción no sea trivial cuando haya instancias con valores y relaciones en conflicto, por lo que la consolidación de los duplicados se debe hacer tan pronto como sea posible en la canalización de aplicación para evitar la resolución de conflictos.</span><span class="sxs-lookup"><span data-stu-id="29c38-155">This may not be trivial where instances have conflicting values and relationships, so consolidating duplicates should be done as soon as possible in your application pipeline to avoid conflict resolution.</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="29c38-156">Todas las entidades nuevas o todas las entidades existentes</span><span class="sxs-lookup"><span data-stu-id="29c38-156">All new/all existing entities</span></span>

<span data-ttu-id="29c38-157">Un ejemplo de trabajar con grafos es insertar o actualizar un blog junto con su colección de entradas asociadas.</span><span class="sxs-lookup"><span data-stu-id="29c38-157">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="29c38-158">Si las entidades del grafo se deben insertar o actualizar en su totalidad, el proceso es el mismo que se describió anteriormente para las entidades únicas.</span><span class="sxs-lookup"><span data-stu-id="29c38-158">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="29c38-159">Por ejemplo, un grafo de blogs y entradas creado de esta manera:</span><span class="sxs-lookup"><span data-stu-id="29c38-159">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="29c38-160">se puede insertar así:</span><span class="sxs-lookup"><span data-stu-id="29c38-160">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="29c38-161">La llamada a Add marcará el blog y todas las entradas para su inserción.</span><span class="sxs-lookup"><span data-stu-id="29c38-161">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="29c38-162">Del mismo modo, si es necesario actualizar todas las entidades de un grafo, se puede usar Update:</span><span class="sxs-lookup"><span data-stu-id="29c38-162">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="29c38-163">El blog y todas las entradas se marcarán para su actualización.</span><span class="sxs-lookup"><span data-stu-id="29c38-163">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="29c38-164">Combinación de entidades nuevas y entidades existentes</span><span class="sxs-lookup"><span data-stu-id="29c38-164">Mix of new and existing entities</span></span>

<span data-ttu-id="29c38-165">Con las claves generadas automáticamente, Update se puede volver a usar tanto para inserciones como para actualizaciones, incluso si el grafo contiene una combinación de entidades que requiere inserción y las que se deben actualizar:</span><span class="sxs-lookup"><span data-stu-id="29c38-165">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="29c38-166">Update marcará una entidad en el grafo, ya sea el blog o una entrada, para inserción si no tiene establecido un valor de clave, mientras que todas las demás entidades se marcarán para actualización.</span><span class="sxs-lookup"><span data-stu-id="29c38-166">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="29c38-167">Como antes, cuando no se usan claves generadas automáticamente, es posible usar una consulta y algún procesamiento:</span><span class="sxs-lookup"><span data-stu-id="29c38-167">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="29c38-168">Control de eliminaciones</span><span class="sxs-lookup"><span data-stu-id="29c38-168">Handling deletes</span></span>

<span data-ttu-id="29c38-169">Puede ser difícil controlar las eliminaciones porque, habitualmente, la ausencia de una entidad implica que se debe eliminar.</span><span class="sxs-lookup"><span data-stu-id="29c38-169">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="29c38-170">Una manera de solucionar esto es usar las "eliminaciones temporales" en que la entidad se marca como eliminada en lugar de eliminarla realmente.</span><span class="sxs-lookup"><span data-stu-id="29c38-170">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="29c38-171">Luego, las eliminaciones pasan a ser iguales a las actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="29c38-171">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="29c38-172">Las eliminaciones temporales se pueden implementar usando [filtros de consulta](xref:core/querying/filters).</span><span class="sxs-lookup"><span data-stu-id="29c38-172">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="29c38-173">En el caso de las eliminaciones reales, un patrón común es usar una extensión del modelo de consulta para realizar lo que esencialmente es una diferencia de grafo.</span><span class="sxs-lookup"><span data-stu-id="29c38-173">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff.</span></span> <span data-ttu-id="29c38-174">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="29c38-174">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="29c38-175">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="29c38-175">TrackGraph</span></span>

<span data-ttu-id="29c38-176">De manera interna, Add, Attach y Update usan el recorrido de grafo con una determinación hecha para cada entidad a fin de saber si se debe marcar como Added (para inserción), Modified (para actualización), Unchanged (para no hacer nada) o Deleted (para eliminación).</span><span class="sxs-lookup"><span data-stu-id="29c38-176">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="29c38-177">Este mecanismo se expone a través de TrackGraph API.</span><span class="sxs-lookup"><span data-stu-id="29c38-177">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="29c38-178">Por ejemplo, supongamos que cuando el cliente envió de vuelta un grafo de entidades, estableció alguna marca en cada entidad para indicar cómo se debe controlar.</span><span class="sxs-lookup"><span data-stu-id="29c38-178">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="29c38-179">Entonces se puede usar TrackGraph para procesar esta marca:</span><span class="sxs-lookup"><span data-stu-id="29c38-179">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="29c38-180">Las marcas solo se muestran como parte de la entidad para simplificar el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="29c38-180">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="29c38-181">Habitualmente, las marcas serían parte de una DTO o alguno otro estado incluido en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="29c38-181">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
