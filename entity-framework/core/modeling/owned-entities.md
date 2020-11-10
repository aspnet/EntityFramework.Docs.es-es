---
title: 'Tipos de entidad de propiedad: EF Core'
description: Cómo configurar tipos de entidad o agregados de propiedad al usar Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 36f756b70c9ad1727c48b5c789fd324c9dc6cd29
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429458"
---
# <a name="owned-entity-types"></a><span data-ttu-id="e2199-103">Tipos de entidad en propiedad</span><span class="sxs-lookup"><span data-stu-id="e2199-103">Owned Entity Types</span></span>

<span data-ttu-id="e2199-104">EF Core permite modelar tipos de entidad que solo pueden aparecer en las propiedades de navegación de otros tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="e2199-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="e2199-105">Se denominan _tipos de entidad de propiedad_.</span><span class="sxs-lookup"><span data-stu-id="e2199-105">These are called _owned entity types_.</span></span> <span data-ttu-id="e2199-106">La entidad que contiene un tipo de entidad propiedad es su _propietario_.</span><span class="sxs-lookup"><span data-stu-id="e2199-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="e2199-107">Las entidades propiedad son esencialmente parte del propietario y no pueden existir sin ella, son conceptualmente similares a los [agregados](https://martinfowler.com/bliki/DDD_Aggregate.html).</span><span class="sxs-lookup"><span data-stu-id="e2199-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="e2199-108">Esto significa que la entidad propiedad es por definición en el lado dependiente de la relación con el propietario.</span><span class="sxs-lookup"><span data-stu-id="e2199-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="e2199-109">Configuración explícita</span><span class="sxs-lookup"><span data-stu-id="e2199-109">Explicit configuration</span></span>

<span data-ttu-id="e2199-110">Los tipos de entidad de propiedad nunca se incluyen en EF Core en el modelo por Convención.</span><span class="sxs-lookup"><span data-stu-id="e2199-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="e2199-111">Puede usar el `OwnsOne` método en `OnModelCreating` o anotar el tipo con `OwnedAttribute` para configurar el tipo como un tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` to configure the type as an owned type.</span></span>

<span data-ttu-id="e2199-112">En este ejemplo, `StreetAddress` es un tipo sin propiedad de identidad.</span><span class="sxs-lookup"><span data-stu-id="e2199-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="e2199-113">Se usa como propiedad del tipo Order para especificar la dirección de envío de un pedido en concreto.</span><span class="sxs-lookup"><span data-stu-id="e2199-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="e2199-114">Podemos usar el `OwnedAttribute` para tratarlo como una entidad propiedad cuando se hace referencia a él desde otro tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="e2199-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="e2199-115">También es posible utilizar el `OwnsOne` método en `OnModelCreating` para especificar que la `ShippingAddress` propiedad es una entidad propiedad del `Order` tipo de entidad y para configurar aspectos adicionales si es necesario.</span><span class="sxs-lookup"><span data-stu-id="e2199-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="e2199-116">Si la `ShippingAddress` propiedad es privada en el `Order` tipo, puede usar la versión de cadena del `OwnsOne` método:</span><span class="sxs-lookup"><span data-stu-id="e2199-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="e2199-117">El modelo anterior se asigna al siguiente esquema de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="e2199-117">The model above is mapped to the following database schema:</span></span>

![Sceenshot del modelo de base de datos para la entidad que contiene la referencia de propiedad](_static/owned-entities-ownsone.png)

<span data-ttu-id="e2199-119">Vea el [proyecto de ejemplo completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) para obtener más contexto.</span><span class="sxs-lookup"><span data-stu-id="e2199-119">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

> [!TIP]
> <span data-ttu-id="e2199-120">El tipo de entidad de propiedad se puede marcar como requerido; consulte los [dependientes uno a uno obligatorios](xref:core/modeling/relationships#one-to-one) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e2199-120">The owned entity type can be marked as required, see [Required one-to-one dependents](xref:core/modeling/relationships#one-to-one) for more information.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="e2199-121">Claves IMPLÍCITAS</span><span class="sxs-lookup"><span data-stu-id="e2199-121">Implicit keys</span></span>

<span data-ttu-id="e2199-122">Los tipos de propiedad configurados con `OwnsOne` o detectados a través de una navegación de referencia siempre tienen una relación uno a uno con el propietario, por lo que no necesitan sus propios valores de clave, ya que los valores de clave externa son únicos.</span><span class="sxs-lookup"><span data-stu-id="e2199-122">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="e2199-123">En el ejemplo anterior, el `StreetAddress` tipo no necesita definir una propiedad de clave.</span><span class="sxs-lookup"><span data-stu-id="e2199-123">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="e2199-124">Para entender cómo EF Core realiza el seguimiento de estos objetos, es útil saber que se crea una clave principal como una [propiedad de sombra](xref:core/modeling/shadow-properties) para el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-124">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="e2199-125">El valor de la clave de una instancia del tipo de propiedad será el mismo que el valor de la clave de la instancia de propietario.</span><span class="sxs-lookup"><span data-stu-id="e2199-125">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="e2199-126">Colecciones de tipos de propiedad</span><span class="sxs-lookup"><span data-stu-id="e2199-126">Collections of owned types</span></span>

<span data-ttu-id="e2199-127">Para configurar una colección de tipos de propiedad, utilice `OwnsMany` en `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="e2199-127">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="e2199-128">Los tipos de propiedad necesitan una clave principal.</span><span class="sxs-lookup"><span data-stu-id="e2199-128">Owned types need a primary key.</span></span> <span data-ttu-id="e2199-129">Si no hay buenas propiedades candidatas en el tipo .NET, EF Core puede intentar crear una.</span><span class="sxs-lookup"><span data-stu-id="e2199-129">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="e2199-130">Sin embargo, cuando los tipos de propiedad se definen a través de una colección, no basta con crear simplemente una propiedad Shadow que actúe como clave externa en el propietario y la clave principal de la instancia de propiedad, como hacemos para `OwnsOne` : puede haber varias instancias de tipo de propiedad para cada propietario, por lo que la clave del propietario no es suficiente para proporcionar una identidad única para cada instancia de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-130">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="e2199-131">Las dos soluciones más directas son:</span><span class="sxs-lookup"><span data-stu-id="e2199-131">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="e2199-132">Definir una clave principal suplente en una nueva propiedad independiente de la clave externa que señala al propietario.</span><span class="sxs-lookup"><span data-stu-id="e2199-132">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="e2199-133">Los valores contenidos deben ser únicos en todos los propietarios (por ejemplo, si el elemento primario {1} tiene un elemento secundario {1} , el elemento primario {2} no puede tener un elemento secundario {1} ), por lo que el valor no tiene ningún significado inherente.</span><span class="sxs-lookup"><span data-stu-id="e2199-133">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="e2199-134">Dado que la clave externa no forma parte de la clave principal, sus valores se pueden cambiar, por lo que puede trasladar un elemento secundario de un elemento primario a otro; sin embargo, esto suele pasar por la semántica de agregado.</span><span class="sxs-lookup"><span data-stu-id="e2199-134">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="e2199-135">Usar la clave externa y una propiedad adicional como clave compuesta.</span><span class="sxs-lookup"><span data-stu-id="e2199-135">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="e2199-136">El valor de propiedad adicional ahora solo debe ser único para un elemento primario determinado (por lo que si el elemento primario tiene un elemento {1} secundario, el elemento {1,1} primario {2} todavía puede tener un elemento secundario {2,1} ).</span><span class="sxs-lookup"><span data-stu-id="e2199-136">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="e2199-137">Al convertir la parte de clave externa de la clave principal en la relación entre el propietario y la entidad propiedad, se convierte en inmutable y se refleja mejor la semántica de agregado.</span><span class="sxs-lookup"><span data-stu-id="e2199-137">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="e2199-138">Esto es lo que EF Core hace de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e2199-138">This is what EF Core does by default.</span></span>

<span data-ttu-id="e2199-139">En este ejemplo vamos a usar la `Distributor` clase.</span><span class="sxs-lookup"><span data-stu-id="e2199-139">In this example we'll use the `Distributor` class.</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="e2199-140">De forma predeterminada, la clave principal utilizada para el tipo de propiedad al que se hace referencia a través de la `ShippingCenters` propiedad de navegación será `("DistributorId", "Id")` donde sea `"DistributorId"` el FK y `"Id"` sea un `int` valor único.</span><span class="sxs-lookup"><span data-stu-id="e2199-140">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="e2199-141">Para configurar una llamada de clave principal diferente `HasKey` .</span><span class="sxs-lookup"><span data-stu-id="e2199-141">To configure a different primary key call `HasKey`.</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

<span data-ttu-id="e2199-142">El modelo anterior se asigna al siguiente esquema de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="e2199-142">The model above is mapped to the following database schema:</span></span>

![Sceenshot del modelo de base de datos para la entidad que contiene la colección de propiedad](_static/owned-entities-ownsmany.png)

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="e2199-144">Asignar tipos de propiedad con división de tabla</span><span class="sxs-lookup"><span data-stu-id="e2199-144">Mapping owned types with table splitting</span></span>

<span data-ttu-id="e2199-145">Cuando se usan bases de datos relacionales, de forma predeterminada, los tipos de propiedad de propiedad se asignan a la misma tabla que el propietario.</span><span class="sxs-lookup"><span data-stu-id="e2199-145">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="e2199-146">Esto requiere dividir la tabla en dos: se usarán algunas columnas para almacenar los datos del propietario, y algunas columnas se utilizarán para almacenar los datos de la entidad propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-146">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="e2199-147">Se trata de una característica común conocida como [División de tablas](xref:core/modeling/table-splitting).</span><span class="sxs-lookup"><span data-stu-id="e2199-147">This is a common feature known as [table splitting](xref:core/modeling/table-splitting).</span></span>

<span data-ttu-id="e2199-148">De forma predeterminada, EF Core asignará el nombre a las columnas de la base de datos para las propiedades del tipo de entidad propiedad que sigue al patrón _Navigation_OwnedEntityProperty_.</span><span class="sxs-lookup"><span data-stu-id="e2199-148">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="e2199-149">Por lo tanto, las `StreetAddress` propiedades aparecerán en la tabla ' Orders ' con los nombres ' ShippingAddress_Street ' y ' ShippingAddress_City '.</span><span class="sxs-lookup"><span data-stu-id="e2199-149">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="e2199-150">Puede usar el `HasColumnName` método para cambiar el nombre de esas columnas.</span><span class="sxs-lookup"><span data-stu-id="e2199-150">You can use the `HasColumnName` method to rename those columns.</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="e2199-151">La mayoría de los métodos de configuración de tipo de entidad normales, como [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) , se pueden llamar de la misma manera.</span><span class="sxs-lookup"><span data-stu-id="e2199-151">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="e2199-152">Compartir el mismo tipo .NET entre varios tipos de propiedad</span><span class="sxs-lookup"><span data-stu-id="e2199-152">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="e2199-153">Un tipo de entidad de propiedad puede ser del mismo tipo de .NET que otro tipo de entidad de propiedad, por lo que es posible que el tipo .NET no sea suficiente para identificar un tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-153">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="e2199-154">En esos casos, la propiedad que apunta del propietario a la entidad propiedad se convierte en la _navegación de definición_ del tipo de entidad de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-154">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="e2199-155">Desde la perspectiva de EF Core, la navegación de definición forma parte de la identidad del tipo junto con el tipo .NET.</span><span class="sxs-lookup"><span data-stu-id="e2199-155">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="e2199-156">Por ejemplo, en la siguiente clase `ShippingAddress` y `BillingAddress` son ambos del mismo tipo .net, `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="e2199-156">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="e2199-157">Para entender cómo EF Core distinguirá las instancias de de estos objetos de las que se ha realizado un seguimiento, puede ser útil pensar que la navegación que define se ha convertido en parte de la clave de la instancia junto con el valor de la clave del propietario y el tipo .NET del tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-157">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="e2199-158">Tipos de propiedad anidados</span><span class="sxs-lookup"><span data-stu-id="e2199-158">Nested owned types</span></span>

<span data-ttu-id="e2199-159">En este ejemplo `OrderDetails` `BillingAddress` , posee y `ShippingAddress` , que son ambos `StreetAddress` tipos.</span><span class="sxs-lookup"><span data-stu-id="e2199-159">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="e2199-160">Luego, `OrderDetails` es propiedad del tipo `DetailedOrder`.</span><span class="sxs-lookup"><span data-stu-id="e2199-160">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="e2199-161">Cada navegación a un tipo de propiedad define un tipo de entidad independiente con una configuración completamente independiente.</span><span class="sxs-lookup"><span data-stu-id="e2199-161">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="e2199-162">Además de los tipos de propiedad anidados, un tipo de propiedad puede hacer referencia a una entidad normal que puede ser el propietario o una entidad distinta, siempre y cuando la entidad propiedad esté en el lado dependiente.</span><span class="sxs-lookup"><span data-stu-id="e2199-162">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="e2199-163">Esta funcionalidad establece tipos de entidad de propiedad además de tipos complejos en EF6.</span><span class="sxs-lookup"><span data-stu-id="e2199-163">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="e2199-164">Configuración de tipos de propiedad</span><span class="sxs-lookup"><span data-stu-id="e2199-164">Configuring Owned Types</span></span>

<span data-ttu-id="e2199-165">Es posible encadenar el `OwnsOne` método en una llamada fluida para configurar este modelo:</span><span class="sxs-lookup"><span data-stu-id="e2199-165">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="e2199-166">Observe la `WithOwner` llamada usada para definir la propiedad de navegación que señala hacia atrás en el propietario.</span><span class="sxs-lookup"><span data-stu-id="e2199-166">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="e2199-167">Para definir una navegación al tipo de entidad Owner que no forma parte de la relación de propiedad, `WithOwner()` se debe llamar a sin ningún argumento.</span><span class="sxs-lookup"><span data-stu-id="e2199-167">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="e2199-168">También es posible lograr este resultado con `OwnedAttribute` en `OrderDetails` y `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="e2199-168">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="e2199-169">Además, observe la `Navigation` llamada a.</span><span class="sxs-lookup"><span data-stu-id="e2199-169">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="e2199-170">En EFCore 5,0, las propiedades de navegación a los tipos de propiedad se pueden configurar aún más [para las propiedades de navegación que no](xref:core/modeling/relationships#configuring-navigation-properties)son de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-170">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

<span data-ttu-id="e2199-171">El modelo anterior se asigna al siguiente esquema de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="e2199-171">The model above is mapped to the following database schema:</span></span>

![Sceenshot del modelo de base de datos para la entidad que contiene referencias anidadas de propiedad](_static/owned-entities-nested.png)

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="e2199-173">Almacenar tipos de propiedad en tablas independientes</span><span class="sxs-lookup"><span data-stu-id="e2199-173">Storing owned types in separate tables</span></span>

<span data-ttu-id="e2199-174">Además, a diferencia de los tipos complejos de EF6, los tipos de propiedad se pueden almacenar en una tabla independiente del propietario.</span><span class="sxs-lookup"><span data-stu-id="e2199-174">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="e2199-175">Para invalidar la Convención que asigna un tipo de propiedad a la misma tabla que el propietario, puede llamar a `ToTable` y proporcionar un nombre de tabla diferente.</span><span class="sxs-lookup"><span data-stu-id="e2199-175">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="e2199-176">En el ejemplo siguiente se asignarán `OrderDetails` y sus dos direcciones a una tabla independiente de `DetailedOrder` :</span><span class="sxs-lookup"><span data-stu-id="e2199-176">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="e2199-177">También es posible usar `TableAttribute` para lograr esto, pero tenga en cuenta que esto produciría un error si hay varias navegaciones al tipo de propiedad, ya que, en ese caso, varios tipos de entidad se asignarían a la misma tabla.</span><span class="sxs-lookup"><span data-stu-id="e2199-177">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="e2199-178">Consultar tipos de propiedad</span><span class="sxs-lookup"><span data-stu-id="e2199-178">Querying owned types</span></span>

<span data-ttu-id="e2199-179">Al consultar al propietario, los tipos de propiedad se incluyen de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e2199-179">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="e2199-180">No es necesario utilizar el `Include` método, incluso si los tipos de propiedad se almacenan en una tabla independiente.</span><span class="sxs-lookup"><span data-stu-id="e2199-180">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="e2199-181">En función del modelo descrito anteriormente, se obtendrá la siguiente consulta `Order` `OrderDetails` y las dos propiedad `StreetAddresses` de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="e2199-181">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="e2199-182">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="e2199-182">Limitations</span></span>

<span data-ttu-id="e2199-183">Algunas de estas limitaciones son fundamentales para el funcionamiento de los tipos de entidad de propiedad, pero otras son restricciones que podríamos ser capaces de quitar en versiones futuras:</span><span class="sxs-lookup"><span data-stu-id="e2199-183">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="e2199-184">Restricciones por diseño</span><span class="sxs-lookup"><span data-stu-id="e2199-184">By-design restrictions</span></span>

- <span data-ttu-id="e2199-185">No se puede crear un `DbSet<T>` para un tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2199-185">You cannot create a `DbSet<T>` for an owned type.</span></span>
- <span data-ttu-id="e2199-186">No se puede llamar a `Entity<T>()` con un tipo de propiedad en `ModelBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e2199-186">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`.</span></span>
- <span data-ttu-id="e2199-187">Los distintos propietarios no pueden compartir instancias de tipos de entidad con propiedad (este es un escenario conocido para objetos de valor que no se pueden implementar mediante tipos de entidad de propiedad).</span><span class="sxs-lookup"><span data-stu-id="e2199-187">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types).</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="e2199-188">Deficiencias actuales</span><span class="sxs-lookup"><span data-stu-id="e2199-188">Current shortcomings</span></span>

- <span data-ttu-id="e2199-189">Los tipos de entidad de propiedad no pueden tener jerarquías de herencia</span><span class="sxs-lookup"><span data-stu-id="e2199-189">Owned entity types cannot have inheritance hierarchies</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="e2199-190">Deficiencias en versiones anteriores</span><span class="sxs-lookup"><span data-stu-id="e2199-190">Shortcomings in previous versions</span></span>

- <span data-ttu-id="e2199-191">En EF Core 2. x las navegaciones de referencia a tipos de entidad de propiedad no pueden ser null a menos que se asignen explícitamente a una tabla independiente del propietario.</span><span class="sxs-lookup"><span data-stu-id="e2199-191">In EF Core 2.x reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner.</span></span>
- <span data-ttu-id="e2199-192">En EF Core 3. x las columnas de los tipos de entidad que se asignan a la misma tabla que el propietario siempre se marcan como valores NULL.</span><span class="sxs-lookup"><span data-stu-id="e2199-192">In EF Core 3.x the columns for owned entity types mapped to the same table as the owner are always marked as nullable.</span></span>
