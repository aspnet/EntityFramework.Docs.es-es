---
title: 'Comparadores de valores: EF Core'
description: Usar comparadores de valores para controlar cómo EF Core compara los valores de propiedad
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: 618341315de05f6efae8f43384809ed72226e18b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128516"
---
# <a name="value-comparers"></a><span data-ttu-id="cde79-103">Comparadores de valores</span><span class="sxs-lookup"><span data-stu-id="cde79-103">Value Comparers</span></span>

> [!NOTE]
> <span data-ttu-id="cde79-104">Esta característica se incluyó por primera vez en EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="cde79-104">This feature was introduced in EF Core 3.0.</span></span>

> [!TIP]
> <span data-ttu-id="cde79-105">El código de este documento se puede encontrar en GitHub como un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="cde79-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="cde79-106">Información previa</span><span class="sxs-lookup"><span data-stu-id="cde79-106">Background</span></span>

<span data-ttu-id="cde79-107">El seguimiento de cambios significa que EF Core determina automáticamente qué cambios realizó la aplicación en una instancia de entidad cargada, de modo que esos cambios se pueden volver a guardar en la base de datos cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a.</span><span class="sxs-lookup"><span data-stu-id="cde79-107">Change tracking means that EF Core automatically determines what changes were performed by the application on a loaded entity instance, so that those changes can be saved back to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="cde79-108">Normalmente, EF Core realiza esta tarea tomando una *instantánea* de la instancia cuando se carga desde la base de datos y *comparando* esa instantánea con la instancia entregada a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cde79-108">EF Core usually performs this by taking a *snapshot* of the instance when it's loaded from the database, and *comparing* that snapshot to the instance handed out to the application.</span></span>

<span data-ttu-id="cde79-109">EF Core incluye lógica integrada para la creación de instantáneas y la comparación de la mayoría de los tipos estándar que se usan en las bases de datos, por lo que los usuarios no suelen tener que preocuparse de este tema.</span><span class="sxs-lookup"><span data-stu-id="cde79-109">EF Core comes with built-in logic for snapshotting and comparing most standard types used in databases, so users don't usually need to worry about this topic.</span></span> <span data-ttu-id="cde79-110">Sin embargo, cuando se asigna una propiedad a través de un [convertidor de valores](xref:core/modeling/value-conversions), EF Core debe realizar la comparación en los tipos de usuario arbitrarios, que pueden ser complejos.</span><span class="sxs-lookup"><span data-stu-id="cde79-110">However, when a property is mapped through a [value converter](xref:core/modeling/value-conversions), EF Core needs to perform comparison on arbitrary user types, which may be complex.</span></span> <span data-ttu-id="cde79-111">De forma predeterminada, EF Core usa la comparación de igualdad predeterminada definida por los tipos (por ejemplo `Equals` , el método); para la toma de instantáneas, los tipos de valor se copian para generar la instantánea, mientras que para los tipos de referencia no se realiza ninguna copia y se usa la misma instancia como la instantánea.</span><span class="sxs-lookup"><span data-stu-id="cde79-111">By default, EF Core uses the default equality comparison defined by types (e.g. the `Equals` method); for snapshotting, value types are copied to produce the snapshot, while for reference types no copying occurs, and the same instance is used as the snapshot.</span></span>

<span data-ttu-id="cde79-112">En los casos en los que el comportamiento de comparación integrado no es adecuado, los usuarios pueden proporcionar un *comparador de valores*, que contiene la lógica para la toma de instantáneas, la comparación y el cálculo de un código hash.</span><span class="sxs-lookup"><span data-stu-id="cde79-112">In cases where the built-in comparison behavior isn't appropriate, users may provide a *value comparer*, which contains logic for snapshotting, comparing and calculating a hash code.</span></span> <span data-ttu-id="cde79-113">Por ejemplo, lo siguiente configura la conversión de valores para `List<int>` que la propiedad se convierta en una cadena JSON en la base de datos y define también un comparador de valores adecuado:</span><span class="sxs-lookup"><span data-stu-id="cde79-113">For example, the following sets up value conversion for `List<int>` property to be value converted to a JSON string in the database, and defines an appropriate value comparer as well:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="cde79-114">Vea [clases mutables](#mutable-classes) a continuación para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="cde79-114">See [mutable classes](#mutable-classes) below for further details.</span></span>

<span data-ttu-id="cde79-115">Tenga en cuenta que los comparadores de valores también se usan para determinar si dos valores de clave son iguales al resolver las relaciones; Esto se explica a continuación.</span><span class="sxs-lookup"><span data-stu-id="cde79-115">Note that value comparers are also used when determining whether two key values are the same when resolving relationships; this is explained below.</span></span>

## <a name="shallow-vs-deep-comparison"></a><span data-ttu-id="cde79-116">Comparación superficial frente a profunda</span><span class="sxs-lookup"><span data-stu-id="cde79-116">Shallow vs. deep comparison</span></span>

<span data-ttu-id="cde79-117">En el caso de los tipos de valor pequeños e inmutables como `int` , la lógica predeterminada de EF Core funciona bien: el valor se copia tal cual cuando se realiza instantáneas y se compara con la comparación de igualdad integrada del tipo.</span><span class="sxs-lookup"><span data-stu-id="cde79-117">For small, immutable value types such as `int`, EF Core's default logic works well: the value is copied as-is when snapshotted, and compared with the type's built-in equality comparison.</span></span> <span data-ttu-id="cde79-118">Al implementar su propio comparador de valores, es importante tener en cuenta si la lógica de comparación profunda o superficial (y de la instantánea) es adecuada.</span><span class="sxs-lookup"><span data-stu-id="cde79-118">When implementing your own value comparer, it's important to consider whether deep or shallow comparison (and snapshotting) logic is appropriate.</span></span>

<span data-ttu-id="cde79-119">Considere las matrices de bytes, que pueden ser arbitrariamente grandes.</span><span class="sxs-lookup"><span data-stu-id="cde79-119">Consider byte arrays, which can be arbitrarily large.</span></span> <span data-ttu-id="cde79-120">Se pueden comparar:</span><span class="sxs-lookup"><span data-stu-id="cde79-120">These could be compared:</span></span>

* <span data-ttu-id="cde79-121">Por referencia, de modo que solo se detecta una diferencia si se usa una nueva matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="cde79-121">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="cde79-122">Mediante una comparación profunda, de modo que se detecta la mutación de los bytes de la matriz</span><span class="sxs-lookup"><span data-stu-id="cde79-122">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="cde79-123">De forma predeterminada, EF Core usa el primero de estos enfoques para las matrices de bytes que no son de clave.</span><span class="sxs-lookup"><span data-stu-id="cde79-123">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span> <span data-ttu-id="cde79-124">Es decir, solo se comparan las referencias y se detecta un cambio solo cuando una matriz de bytes existente se reemplaza por otra nueva.</span><span class="sxs-lookup"><span data-stu-id="cde79-124">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span> <span data-ttu-id="cde79-125">Se trata de una decisión pragmática que evita copiar matrices completas y compararlas de byte a byte al ejecutarse <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , y el escenario habitual de reemplazar, por ejemplo, una imagen por otra se administra de forma eficaz.</span><span class="sxs-lookup"><span data-stu-id="cde79-125">This is a pragmatic decision that avoids copying entire arrays and comparing them byte-to-byte when executing <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, and the common scenario of replacing, say, one image with another is handled in a performant way.</span></span>

<span data-ttu-id="cde79-126">Por otro lado, la igualdad de referencia no funcionaría cuando se usan matrices de bytes para representar claves binarias, ya que es muy poco probable que una propiedad FK esté establecida en la _misma instancia_ que una propiedad PK en la que se debe comparar.</span><span class="sxs-lookup"><span data-stu-id="cde79-126">On the other hand, reference equality would not work when byte arrays are used to represent binary keys, since it's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span> <span data-ttu-id="cde79-127">Por lo tanto, EF Core utiliza comparaciones en profundidad para las matrices de bytes que actúan como claves. no es probable que esto afecte al rendimiento, ya que las claves binarias suelen ser cortas.</span><span class="sxs-lookup"><span data-stu-id="cde79-127">Therefore, EF Core uses deep comparisons for byte arrays acting as keys; this is unlikely to have a big performance hit since binary keys are usually short.</span></span>

<span data-ttu-id="cde79-128">Tenga en cuenta que la comparación y la lógica de la instantánea elegidos deben corresponderse entre sí: la comparación profunda requiere una instantánea profunda para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="cde79-128">Note that the chosen comparison and snapshotting logic must correspond to each other: deep comparison requires deep snapshotting to function correctly.</span></span>

## <a name="simple-immutable-classes"></a><span data-ttu-id="cde79-129">Clases inmutables simples</span><span class="sxs-lookup"><span data-stu-id="cde79-129">Simple immutable classes</span></span>

<span data-ttu-id="cde79-130">Considere una propiedad que utiliza un convertidor de valores para asignar una clase simple e inmutable.</span><span class="sxs-lookup"><span data-stu-id="cde79-130">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="cde79-131">Las propiedades de este tipo no necesitan comparaciones especiales ni instantáneas porque:</span><span class="sxs-lookup"><span data-stu-id="cde79-131">Properties of this type do not need special comparisons or snapshots because:</span></span>

* <span data-ttu-id="cde79-132">La igualdad se invalida para que las distintas instancias se comparen correctamente</span><span class="sxs-lookup"><span data-stu-id="cde79-132">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="cde79-133">El tipo es inmutable, por lo que no existe la posibilidad de alterar un valor de instantánea</span><span class="sxs-lookup"><span data-stu-id="cde79-133">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="cde79-134">Por lo tanto, en este caso, el comportamiento predeterminado de EF Core es correcto.</span><span class="sxs-lookup"><span data-stu-id="cde79-134">So in this case the default behavior of EF Core is fine as it is.</span></span>

## <a name="simple-immutable-structs"></a><span data-ttu-id="cde79-135">Structs inmutables simples</span><span class="sxs-lookup"><span data-stu-id="cde79-135">Simple immutable structs</span></span>

<span data-ttu-id="cde79-136">La asignación para Structs simples también es sencilla y no requiere comparaciones o instantáneas especiales.</span><span class="sxs-lookup"><span data-stu-id="cde79-136">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="cde79-137">EF Core tiene compatibilidad integrada para generar comparaciones compiladas miembro a miembro de propiedades de struct.</span><span class="sxs-lookup"><span data-stu-id="cde79-137">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="cde79-138">Esto significa que no es necesario que los Structs tengan una igualdad invalidada para EF Core, pero todavía puede optar por [otras razones](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span><span class="sxs-lookup"><span data-stu-id="cde79-138">This means structs don't need to have equality overridden for EF Core, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="cde79-139">Además, no es necesario realizar una instantánea especial, ya que las estructuras son inmutables y siempre se copian de miembro a miembro.</span><span class="sxs-lookup"><span data-stu-id="cde79-139">Also, special snapshotting is not needed since structs are immutable and are always copied memberwise anyway.</span></span>
<span data-ttu-id="cde79-140">(Esto también se aplica a las estructuras mutables, pero las [estructuras mutables deberían evitarse en general](/dotnet/csharp/write-safe-efficient-code)).</span><span class="sxs-lookup"><span data-stu-id="cde79-140">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

## <a name="mutable-classes"></a><span data-ttu-id="cde79-141">Clases mutables</span><span class="sxs-lookup"><span data-stu-id="cde79-141">Mutable classes</span></span>

<span data-ttu-id="cde79-142">Se recomienda usar tipos inmutables (clases o Structs) con convertidores de valores siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="cde79-142">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="cde79-143">Esto suele ser más eficaz y tiene una semántica más clara que el uso de un tipo mutable.</span><span class="sxs-lookup"><span data-stu-id="cde79-143">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="cde79-144">Sin embargo, es habitual usar las propiedades de los tipos que la aplicación no puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="cde79-144">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="cde79-145">Por ejemplo, asignar una propiedad que contenga una lista de números:</span><span class="sxs-lookup"><span data-stu-id="cde79-145">For example, mapping a property containing a list of numbers:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="cde79-146">La clase <xref:System.Collections.Generic.List%601>:</span><span class="sxs-lookup"><span data-stu-id="cde79-146">The <xref:System.Collections.Generic.List%601> class:</span></span>

* <span data-ttu-id="cde79-147">Tiene igualdad de referencia; dos listas que contienen los mismos valores se tratan como diferentes.</span><span class="sxs-lookup"><span data-stu-id="cde79-147">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="cde79-148">Es mutable; los valores de la lista se pueden agregar y quitar.</span><span class="sxs-lookup"><span data-stu-id="cde79-148">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="cde79-149">Una conversión de valor típica en una propiedad de lista puede convertir la lista a y desde JSON:</span><span class="sxs-lookup"><span data-stu-id="cde79-149">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

### <a name="ef-core-50"></a>[<span data-ttu-id="cde79-150">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="cde79-150">EF Core 5.0</span></span>](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[<span data-ttu-id="cde79-151">Versiones anteriores</span><span class="sxs-lookup"><span data-stu-id="cde79-151">Older versions</span></span>](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

<span data-ttu-id="cde79-152">El <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructor acepta tres expresiones:</span><span class="sxs-lookup"><span data-stu-id="cde79-152">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructor accepts three expressions:</span></span>

* <span data-ttu-id="cde79-153">Expresión para comprobar la igualdad</span><span class="sxs-lookup"><span data-stu-id="cde79-153">An expression for checking equality</span></span>
* <span data-ttu-id="cde79-154">Expresión para generar un código hash.</span><span class="sxs-lookup"><span data-stu-id="cde79-154">An expression for generating a hash code</span></span>
* <span data-ttu-id="cde79-155">Una expresión para la instantánea de un valor</span><span class="sxs-lookup"><span data-stu-id="cde79-155">An expression to snapshot a value</span></span>

<span data-ttu-id="cde79-156">En este caso, la comparación se realiza comprobando si las secuencias de números son iguales.</span><span class="sxs-lookup"><span data-stu-id="cde79-156">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="cde79-157">Del mismo modo, el código hash se genera a partir de esta misma secuencia.</span><span class="sxs-lookup"><span data-stu-id="cde79-157">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="cde79-158">(Tenga en cuenta que se trata de un código hash sobre valores mutables y, por lo tanto, puede [causar problemas](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span><span class="sxs-lookup"><span data-stu-id="cde79-158">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="cde79-159">En su lugar, puede ser inmutable si es posible).</span><span class="sxs-lookup"><span data-stu-id="cde79-159">Be immutable instead if you can.)</span></span>

<span data-ttu-id="cde79-160">La instantánea se crea mediante la clonación de la lista con `ToList` .</span><span class="sxs-lookup"><span data-stu-id="cde79-160">The snapshot is created by cloning the list with `ToList`.</span></span>
<span data-ttu-id="cde79-161">De nuevo, esto solo es necesario si se van a mutar las listas.</span><span class="sxs-lookup"><span data-stu-id="cde79-161">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="cde79-162">En su lugar, puede ser inmutable si es posible.</span><span class="sxs-lookup"><span data-stu-id="cde79-162">Be immutable instead if you can.</span></span>

> [!NOTE]
> <span data-ttu-id="cde79-163">Los convertidores de valores y los comparadores se construyen mediante expresiones en lugar de delegados simples.</span><span class="sxs-lookup"><span data-stu-id="cde79-163">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="cde79-164">Esto se debe a que EF Core inserta estas expresiones en un árbol de expresión mucho más complejo que se compila en un delegado de forma de entidad.</span><span class="sxs-lookup"><span data-stu-id="cde79-164">This is because EF Core inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="cde79-165">Conceptualmente, esto es similar a la inserción del compilador.</span><span class="sxs-lookup"><span data-stu-id="cde79-165">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="cde79-166">Por ejemplo, una conversión simple solo puede ser una compilada en la conversión, en lugar de una llamada a otro método para realizar la conversión.</span><span class="sxs-lookup"><span data-stu-id="cde79-166">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>

## <a name="key-comparers"></a><span data-ttu-id="cde79-167">Comparadores de claves</span><span class="sxs-lookup"><span data-stu-id="cde79-167">Key comparers</span></span>

<span data-ttu-id="cde79-168">En la sección de fondo se explica por qué las comparaciones de claves pueden requerir una semántica especial.</span><span class="sxs-lookup"><span data-stu-id="cde79-168">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="cde79-169">Asegúrese de crear un comparador que sea adecuado para las claves al establecerlo en una propiedad principal, principal o de clave externa.</span><span class="sxs-lookup"><span data-stu-id="cde79-169">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="cde79-170">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> en los casos poco frecuentes en los que se requiere una semántica diferente en la misma propiedad.</span><span class="sxs-lookup"><span data-stu-id="cde79-170">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]
> <span data-ttu-id="cde79-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> se ha quedado obsoleto en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="cde79-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> has been obsoleted in EF Core 5.0.</span></span> <span data-ttu-id="cde79-172">En su lugar, use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>.</span><span class="sxs-lookup"><span data-stu-id="cde79-172">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> instead.</span></span>

## <a name="overriding-the-default-comparer"></a><span data-ttu-id="cde79-173">Reemplazar el comparador predeterminado</span><span class="sxs-lookup"><span data-stu-id="cde79-173">Overriding the default comparer</span></span>

<span data-ttu-id="cde79-174">En ocasiones, es posible que la comparación predeterminada usada por EF Core no sea adecuada.</span><span class="sxs-lookup"><span data-stu-id="cde79-174">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="cde79-175">Por ejemplo, la mutación de matrices de bytes no se detecta, de forma predeterminada, en EF Core.</span><span class="sxs-lookup"><span data-stu-id="cde79-175">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="cde79-176">Esto se puede invalidar si se establece un comparador diferente en la propiedad:</span><span class="sxs-lookup"><span data-stu-id="cde79-176">This can be overridden by setting a different comparer on the property:</span></span>

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="cde79-177">EF Core ahora comparará las secuencias de bytes y, por tanto, detectará las mutaciones de matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="cde79-177">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
