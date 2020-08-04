---
title: 'Comparadores de valores: EF Core'
description: Usar comparadores de valores para controlar cómo EF Core compara los valores de propiedad
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: fa5352129977d858d54d4aede746b320c91b0ad3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526789"
---
# <a name="value-comparers"></a><span data-ttu-id="4a418-103">Comparadores de valores</span><span class="sxs-lookup"><span data-stu-id="4a418-103">Value Comparers</span></span>

> [!NOTE]  
> <span data-ttu-id="4a418-104">Esta característica es nueva en EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="4a418-104">This feature is new in EF Core 3.0.</span></span>

> [!TIP]  
> <span data-ttu-id="4a418-105">El código de este documento se puede encontrar en GitHub como un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="4a418-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="4a418-106">Información previa</span><span class="sxs-lookup"><span data-stu-id="4a418-106">Background</span></span>

<span data-ttu-id="4a418-107">EF Core necesita comparar los valores de propiedad cuando:</span><span class="sxs-lookup"><span data-stu-id="4a418-107">EF Core needs to compare property values when:</span></span>

* <span data-ttu-id="4a418-108">Determinar si una propiedad se ha cambiado como parte de la [detección de cambios de actualizaciones](xref:core/saving/basic)</span><span class="sxs-lookup"><span data-stu-id="4a418-108">Determining whether a property has been changed as part of [detecting changes for updates](xref:core/saving/basic)</span></span>
* <span data-ttu-id="4a418-109">Determinar si dos valores de clave son iguales al resolver relaciones</span><span class="sxs-lookup"><span data-stu-id="4a418-109">Determining whether two key values are the same when resolving relationships</span></span> 

<span data-ttu-id="4a418-110">Esto se controla automáticamente para los tipos primitivos comunes como int, bool, DateTime, etc.</span><span class="sxs-lookup"><span data-stu-id="4a418-110">This is handled automatically for common primitive types such as int, bool, DateTime, etc.</span></span>

<span data-ttu-id="4a418-111">En el caso de los tipos más complejos, se deben tomar las decisiones necesarias para realizar la comparación.</span><span class="sxs-lookup"><span data-stu-id="4a418-111">For more complex types, choices need to be made as to how to do the comparison.</span></span>
<span data-ttu-id="4a418-112">Por ejemplo, se podría comparar una matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="4a418-112">For example, a byte array could be compared:</span></span>

* <span data-ttu-id="4a418-113">Por referencia, de modo que solo se detecta una diferencia si se usa una nueva matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="4a418-113">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="4a418-114">Mediante una comparación profunda, de modo que se detecta la mutación de los bytes de la matriz</span><span class="sxs-lookup"><span data-stu-id="4a418-114">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="4a418-115">De forma predeterminada, EF Core usa el primero de estos enfoques para las matrices de bytes que no son de clave.</span><span class="sxs-lookup"><span data-stu-id="4a418-115">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span>
<span data-ttu-id="4a418-116">Es decir, solo se comparan las referencias y se detecta un cambio solo cuando una matriz de bytes existente se reemplaza por otra nueva.</span><span class="sxs-lookup"><span data-stu-id="4a418-116">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span>
<span data-ttu-id="4a418-117">Se trata de una decisión pragmática que evita la comparación profunda de muchas matrices de bytes grandes al ejecutar SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="4a418-117">This is a pragmatic decision that avoids deep comparison of many large byte arrays when executing SaveChanges.</span></span>
<span data-ttu-id="4a418-118">Pero el escenario habitual de reemplazar, por ejemplo, una imagen con una imagen diferente se administra de forma eficaz.</span><span class="sxs-lookup"><span data-stu-id="4a418-118">But the common scenario of replacing, say, an image with a different image is handled in a performant way.</span></span>

<span data-ttu-id="4a418-119">Por otro lado, la igualdad de referencia no funcionaría cuando se utilizan matrices de bytes para representar claves binarias.</span><span class="sxs-lookup"><span data-stu-id="4a418-119">On the other hand, reference equality would not work when byte arrays are used to represent binary keys.</span></span>
<span data-ttu-id="4a418-120">Es muy poco probable que una propiedad FK esté establecida en la _misma instancia_ que una propiedad PK en la que se debe comparar.</span><span class="sxs-lookup"><span data-stu-id="4a418-120">It's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span>
<span data-ttu-id="4a418-121">Por lo tanto, EF Core utiliza comparaciones en profundidad para las matrices de bytes que actúan como claves.</span><span class="sxs-lookup"><span data-stu-id="4a418-121">Therefore, EF Core uses deep comparisons for byte arrays acting as keys.</span></span>
<span data-ttu-id="4a418-122">No es probable que esto afecte al rendimiento, ya que las claves binarias suelen ser cortas.</span><span class="sxs-lookup"><span data-stu-id="4a418-122">This is unlikely to have a big performance hit since binary keys are usually short.</span></span>

### <a name="snapshots"></a><span data-ttu-id="4a418-123">Instantáneas</span><span class="sxs-lookup"><span data-stu-id="4a418-123">Snapshots</span></span>

<span data-ttu-id="4a418-124">Las comparaciones profundas en tipos mutables significan que EF Core necesita la capacidad de crear una "instantánea" profunda del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="4a418-124">Deep comparisons on mutable types means that EF Core needs the ability to create a deep "snapshot" of the property value.</span></span>
<span data-ttu-id="4a418-125">Simplemente copiar la referencia en su lugar daría como resultado la mutación del valor actual y de la instantánea, ya que son _el mismo objeto_.</span><span class="sxs-lookup"><span data-stu-id="4a418-125">Just copying the reference instead would result in mutating both the current value and the snapshot, since they are _the same object_.</span></span>
<span data-ttu-id="4a418-126">Por lo tanto, cuando se utilizan comparaciones profundas en tipos mutables, también se requiere la instantánea profunda.</span><span class="sxs-lookup"><span data-stu-id="4a418-126">Therefore, when deep comparisons are used on mutable types, deep snapshotting is also required.</span></span>

## <a name="properties-with-value-converters"></a><span data-ttu-id="4a418-127">Propiedades con convertidores de valores</span><span class="sxs-lookup"><span data-stu-id="4a418-127">Properties with value converters</span></span>

<span data-ttu-id="4a418-128">En el caso anterior, EF Core tiene compatibilidad de asignación nativa con las matrices de bytes y, por tanto, puede elegir automáticamente los valores predeterminados adecuados.</span><span class="sxs-lookup"><span data-stu-id="4a418-128">In the case above, EF Core has native mapping support for byte arrays and so can automatically choose appropriate defaults.</span></span>
<span data-ttu-id="4a418-129">Sin embargo, si la propiedad se asigna a través de un [convertidor de valores](xref:core/modeling/value-conversions), EF Core no puede determinar siempre la comparación adecuada que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="4a418-129">However, if the property is mapped through a [value converter](xref:core/modeling/value-conversions), then EF Core can't always determine the appropriate comparison to use.</span></span>
<span data-ttu-id="4a418-130">En su lugar, EF Core siempre usa la comparación de igualdad predeterminada definida por el tipo de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="4a418-130">Instead, EF Core always uses the default equality comparison defined by the type of the property.</span></span>
<span data-ttu-id="4a418-131">Esto suele ser correcto, pero puede que sea necesario invalidarlo al asignar tipos más complejos.</span><span class="sxs-lookup"><span data-stu-id="4a418-131">This is often correct, but may need to be overridden when mapping more complex types.</span></span>

### <a name="simple-immutable-classes"></a><span data-ttu-id="4a418-132">Clases inmutables simples</span><span class="sxs-lookup"><span data-stu-id="4a418-132">Simple immutable classes</span></span>

<span data-ttu-id="4a418-133">Considere una propiedad que utiliza un convertidor de valores para asignar una clase simple e inmutable.</span><span class="sxs-lookup"><span data-stu-id="4a418-133">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="4a418-134">Las propiedades de este tipo no necesitan comparaciones especiales ni instantáneas porque:</span><span class="sxs-lookup"><span data-stu-id="4a418-134">Properties of this type do not need special comparisons or snapshots because:</span></span>
* <span data-ttu-id="4a418-135">La igualdad se invalida para que las distintas instancias se comparen correctamente</span><span class="sxs-lookup"><span data-stu-id="4a418-135">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="4a418-136">El tipo es inmutable, por lo que no existe la posibilidad de alterar un valor de instantánea</span><span class="sxs-lookup"><span data-stu-id="4a418-136">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="4a418-137">Por lo tanto, en este caso, el comportamiento predeterminado de EF Core es correcto.</span><span class="sxs-lookup"><span data-stu-id="4a418-137">So in this case the default behavior of EF Core is fine as it is.</span></span>

### <a name="simple-immutable-structs"></a><span data-ttu-id="4a418-138">Structs inmutables simples</span><span class="sxs-lookup"><span data-stu-id="4a418-138">Simple immutable Structs</span></span>

<span data-ttu-id="4a418-139">La asignación para Structs simples también es sencilla y no requiere comparaciones o instantáneas especiales.</span><span class="sxs-lookup"><span data-stu-id="4a418-139">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="4a418-140">EF Core tiene compatibilidad integrada para generar comparaciones compiladas miembro a miembro de propiedades de struct.</span><span class="sxs-lookup"><span data-stu-id="4a418-140">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="4a418-141">Esto significa que no es necesario que los Structs tengan una igualdad invalidada para EF, pero todavía puede optar por [otras razones](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span><span class="sxs-lookup"><span data-stu-id="4a418-141">This means structs don't need to have equality overridden for EF, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="4a418-142">Además, no es necesario realizar una instantánea especial, ya que las estructuras son inmutables y siempre se copian de miembro a miembro.</span><span class="sxs-lookup"><span data-stu-id="4a418-142">Also, special snapshotting is not needed since structs immutable and are always memberwise copied anyway.</span></span>
<span data-ttu-id="4a418-143">(Esto también se aplica a las estructuras mutables, pero las [estructuras mutables deberían evitarse en general](/dotnet/csharp/write-safe-efficient-code)).</span><span class="sxs-lookup"><span data-stu-id="4a418-143">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

### <a name="mutable-classes"></a><span data-ttu-id="4a418-144">Clases mutables</span><span class="sxs-lookup"><span data-stu-id="4a418-144">Mutable classes</span></span>

<span data-ttu-id="4a418-145">Se recomienda usar tipos inmutables (clases o Structs) con convertidores de valores siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="4a418-145">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="4a418-146">Esto suele ser más eficaz y tiene una semántica más clara que el uso de un tipo mutable.</span><span class="sxs-lookup"><span data-stu-id="4a418-146">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="4a418-147">Sin embargo, es habitual usar las propiedades de los tipos que la aplicación no puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="4a418-147">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="4a418-148">Por ejemplo, asignar una propiedad que contenga una lista de números:</span><span class="sxs-lookup"><span data-stu-id="4a418-148">For example, mapping a property containing a list of numbers:</span></span> 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="4a418-149">La [ `List<T>` clase](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span><span class="sxs-lookup"><span data-stu-id="4a418-149">The [`List<T>` class](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span></span>
* <span data-ttu-id="4a418-150">Tiene igualdad de referencia; dos listas que contienen los mismos valores se tratan como diferentes.</span><span class="sxs-lookup"><span data-stu-id="4a418-150">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="4a418-151">Es mutable; los valores de la lista se pueden agregar y quitar.</span><span class="sxs-lookup"><span data-stu-id="4a418-151">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="4a418-152">Una conversión de valor típica en una propiedad de lista puede convertir la lista a y desde JSON:</span><span class="sxs-lookup"><span data-stu-id="4a418-152">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="4a418-153">Esto requiere establecer `ValueComparer<T>` en la propiedad para forzar EF Core usar comparaciones correctas con esta conversión:</span><span class="sxs-lookup"><span data-stu-id="4a418-153">This then requires setting a `ValueComparer<T>` on the property to force EF Core use correct comparisons with this conversion:</span></span>

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> <span data-ttu-id="4a418-154">Todavía no se ha implementado la API del generador de modelos ("fluida") para establecer un comparador de valores.</span><span class="sxs-lookup"><span data-stu-id="4a418-154">The model builder ("fluent") API to set a value comparer has not yet been implemented.</span></span>
> <span data-ttu-id="4a418-155">En su lugar, el código anterior llama a SetValueComparer en el IMutableProperty de nivel inferior expuesto por el generador como ' Metadata '.</span><span class="sxs-lookup"><span data-stu-id="4a418-155">Instead, the code above calls SetValueComparer on the lower-level IMutableProperty exposed by the builder as 'Metadata'.</span></span>

<span data-ttu-id="4a418-156">El `ValueComparer<T>` constructor acepta tres expresiones:</span><span class="sxs-lookup"><span data-stu-id="4a418-156">The `ValueComparer<T>` constructor accepts three expressions:</span></span>
* <span data-ttu-id="4a418-157">Expresión para comprobar la igualdad</span><span class="sxs-lookup"><span data-stu-id="4a418-157">An expression for checking equality</span></span>
* <span data-ttu-id="4a418-158">Expresión para generar un código hash.</span><span class="sxs-lookup"><span data-stu-id="4a418-158">An expression for generating a hash code</span></span>
* <span data-ttu-id="4a418-159">Una expresión para la instantánea de un valor</span><span class="sxs-lookup"><span data-stu-id="4a418-159">An expression to snapshot a value</span></span>  

<span data-ttu-id="4a418-160">En este caso, la comparación se realiza comprobando si las secuencias de números son iguales.</span><span class="sxs-lookup"><span data-stu-id="4a418-160">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="4a418-161">Del mismo modo, el código hash se genera a partir de esta misma secuencia.</span><span class="sxs-lookup"><span data-stu-id="4a418-161">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="4a418-162">(Tenga en cuenta que se trata de un código hash sobre valores mutables y, por lo tanto, puede [causar problemas](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span><span class="sxs-lookup"><span data-stu-id="4a418-162">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="4a418-163">En su lugar, puede ser inmutable si es posible).</span><span class="sxs-lookup"><span data-stu-id="4a418-163">Be immutable instead if you can.)</span></span>

<span data-ttu-id="4a418-164">La instantánea se crea mediante la clonación de la lista con ToList.</span><span class="sxs-lookup"><span data-stu-id="4a418-164">The snapshot is created by cloning the list with ToList.</span></span>
<span data-ttu-id="4a418-165">De nuevo, esto solo es necesario si se van a mutar las listas.</span><span class="sxs-lookup"><span data-stu-id="4a418-165">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="4a418-166">En su lugar, puede ser inmutable si es posible.</span><span class="sxs-lookup"><span data-stu-id="4a418-166">Be immutable instead if you can.</span></span> 

> [!NOTE]  
> <span data-ttu-id="4a418-167">Los convertidores de valores y los comparadores se construyen mediante expresiones en lugar de delegados simples.</span><span class="sxs-lookup"><span data-stu-id="4a418-167">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="4a418-168">Esto se debe a que EF inserta estas expresiones en un árbol de expresión mucho más complejo que luego se compila en un delegado de forma de entidad.</span><span class="sxs-lookup"><span data-stu-id="4a418-168">This is because EF inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="4a418-169">Conceptualmente, esto es similar a la inserción del compilador.</span><span class="sxs-lookup"><span data-stu-id="4a418-169">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="4a418-170">Por ejemplo, una conversión simple solo puede ser una compilada en la conversión, en lugar de una llamada a otro método para realizar la conversión.</span><span class="sxs-lookup"><span data-stu-id="4a418-170">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>    

### <a name="key-comparers"></a><span data-ttu-id="4a418-171">Comparadores de claves</span><span class="sxs-lookup"><span data-stu-id="4a418-171">Key comparers</span></span>

<span data-ttu-id="4a418-172">En la sección de fondo se explica por qué las comparaciones de claves pueden requerir una semántica especial.</span><span class="sxs-lookup"><span data-stu-id="4a418-172">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="4a418-173">Asegúrese de crear un comparador que sea adecuado para las claves al establecerlo en una propiedad principal, principal o de clave externa.</span><span class="sxs-lookup"><span data-stu-id="4a418-173">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="4a418-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) en los casos excepcionales en los que se requiere una semántica diferente en la misma propiedad.</span><span class="sxs-lookup"><span data-stu-id="4a418-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]  
> <span data-ttu-id="4a418-175">SetStructuralComparer se ha quedado obsoleto en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4a418-175">SetStructuralComparer has been obsoleted in EF Core 5.0.</span></span>
> <span data-ttu-id="4a418-176">Use SetKeyValueComparer en su lugar.</span><span class="sxs-lookup"><span data-stu-id="4a418-176">Use SetKeyValueComparer instead.</span></span>

### <a name="overriding-defaults"></a><span data-ttu-id="4a418-177">Invalidación de los valores predeterminados</span><span class="sxs-lookup"><span data-stu-id="4a418-177">Overriding defaults</span></span>

<span data-ttu-id="4a418-178">En ocasiones, es posible que la comparación predeterminada usada por EF Core no sea adecuada.</span><span class="sxs-lookup"><span data-stu-id="4a418-178">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="4a418-179">Por ejemplo, la mutación de matrices de bytes no se detecta, de forma predeterminada, en EF Core.</span><span class="sxs-lookup"><span data-stu-id="4a418-179">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="4a418-180">Esto se puede invalidar si se establece un comparador diferente en la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4a418-180">This can be overridden by setting a different comparer on the property:</span></span> 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="4a418-181">EF Core ahora comparará las secuencias de bytes y, por tanto, detectará las mutaciones de matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="4a418-181">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
