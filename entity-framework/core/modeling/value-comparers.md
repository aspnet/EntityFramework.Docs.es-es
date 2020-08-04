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
# <a name="value-comparers"></a>Comparadores de valores

> [!NOTE]  
> Esta característica es nueva en EF Core 3,0.

> [!TIP]  
> El código de este documento se puede encontrar en GitHub como un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Información previa

EF Core necesita comparar los valores de propiedad cuando:

* Determinar si una propiedad se ha cambiado como parte de la [detección de cambios de actualizaciones](xref:core/saving/basic)
* Determinar si dos valores de clave son iguales al resolver relaciones 

Esto se controla automáticamente para los tipos primitivos comunes como int, bool, DateTime, etc.

En el caso de los tipos más complejos, se deben tomar las decisiones necesarias para realizar la comparación.
Por ejemplo, se podría comparar una matriz de bytes:

* Por referencia, de modo que solo se detecta una diferencia si se usa una nueva matriz de bytes
* Mediante una comparación profunda, de modo que se detecta la mutación de los bytes de la matriz

De forma predeterminada, EF Core usa el primero de estos enfoques para las matrices de bytes que no son de clave.
Es decir, solo se comparan las referencias y se detecta un cambio solo cuando una matriz de bytes existente se reemplaza por otra nueva.
Se trata de una decisión pragmática que evita la comparación profunda de muchas matrices de bytes grandes al ejecutar SaveChanges.
Pero el escenario habitual de reemplazar, por ejemplo, una imagen con una imagen diferente se administra de forma eficaz.

Por otro lado, la igualdad de referencia no funcionaría cuando se utilizan matrices de bytes para representar claves binarias.
Es muy poco probable que una propiedad FK esté establecida en la _misma instancia_ que una propiedad PK en la que se debe comparar.
Por lo tanto, EF Core utiliza comparaciones en profundidad para las matrices de bytes que actúan como claves.
No es probable que esto afecte al rendimiento, ya que las claves binarias suelen ser cortas.

### <a name="snapshots"></a>Instantáneas

Las comparaciones profundas en tipos mutables significan que EF Core necesita la capacidad de crear una "instantánea" profunda del valor de propiedad.
Simplemente copiar la referencia en su lugar daría como resultado la mutación del valor actual y de la instantánea, ya que son _el mismo objeto_.
Por lo tanto, cuando se utilizan comparaciones profundas en tipos mutables, también se requiere la instantánea profunda.

## <a name="properties-with-value-converters"></a>Propiedades con convertidores de valores

En el caso anterior, EF Core tiene compatibilidad de asignación nativa con las matrices de bytes y, por tanto, puede elegir automáticamente los valores predeterminados adecuados.
Sin embargo, si la propiedad se asigna a través de un [convertidor de valores](xref:core/modeling/value-conversions), EF Core no puede determinar siempre la comparación adecuada que se va a usar.
En su lugar, EF Core siempre usa la comparación de igualdad predeterminada definida por el tipo de la propiedad.
Esto suele ser correcto, pero puede que sea necesario invalidarlo al asignar tipos más complejos.

### <a name="simple-immutable-classes"></a>Clases inmutables simples

Considere una propiedad que utiliza un convertidor de valores para asignar una clase simple e inmutable.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Las propiedades de este tipo no necesitan comparaciones especiales ni instantáneas porque:
* La igualdad se invalida para que las distintas instancias se comparen correctamente
* El tipo es inmutable, por lo que no existe la posibilidad de alterar un valor de instantánea

Por lo tanto, en este caso, el comportamiento predeterminado de EF Core es correcto.

### <a name="simple-immutable-structs"></a>Structs inmutables simples

La asignación para Structs simples también es sencilla y no requiere comparaciones o instantáneas especiales.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core tiene compatibilidad integrada para generar comparaciones compiladas miembro a miembro de propiedades de struct.
Esto significa que no es necesario que los Structs tengan una igualdad invalidada para EF, pero todavía puede optar por [otras razones](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).
Además, no es necesario realizar una instantánea especial, ya que las estructuras son inmutables y siempre se copian de miembro a miembro.
(Esto también se aplica a las estructuras mutables, pero las [estructuras mutables deberían evitarse en general](/dotnet/csharp/write-safe-efficient-code)).

### <a name="mutable-classes"></a>Clases mutables

Se recomienda usar tipos inmutables (clases o Structs) con convertidores de valores siempre que sea posible.
Esto suele ser más eficaz y tiene una semántica más clara que el uso de un tipo mutable.

Sin embargo, es habitual usar las propiedades de los tipos que la aplicación no puede cambiar.
Por ejemplo, asignar una propiedad que contenga una lista de números: 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

La [ `List<T>` clase](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):
* Tiene igualdad de referencia; dos listas que contienen los mismos valores se tratan como diferentes.
* Es mutable; los valores de la lista se pueden agregar y quitar.

Una conversión de valor típica en una propiedad de lista puede convertir la lista a y desde JSON:

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Esto requiere establecer `ValueComparer<T>` en la propiedad para forzar EF Core usar comparaciones correctas con esta conversión:

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> Todavía no se ha implementado la API del generador de modelos ("fluida") para establecer un comparador de valores.
> En su lugar, el código anterior llama a SetValueComparer en el IMutableProperty de nivel inferior expuesto por el generador como ' Metadata '.

El `ValueComparer<T>` constructor acepta tres expresiones:
* Expresión para comprobar la igualdad
* Expresión para generar un código hash.
* Una expresión para la instantánea de un valor  

En este caso, la comparación se realiza comprobando si las secuencias de números son iguales.

Del mismo modo, el código hash se genera a partir de esta misma secuencia.
(Tenga en cuenta que se trata de un código hash sobre valores mutables y, por lo tanto, puede [causar problemas](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).
En su lugar, puede ser inmutable si es posible).

La instantánea se crea mediante la clonación de la lista con ToList.
De nuevo, esto solo es necesario si se van a mutar las listas.
En su lugar, puede ser inmutable si es posible. 

> [!NOTE]  
> Los convertidores de valores y los comparadores se construyen mediante expresiones en lugar de delegados simples.
> Esto se debe a que EF inserta estas expresiones en un árbol de expresión mucho más complejo que luego se compila en un delegado de forma de entidad.
> Conceptualmente, esto es similar a la inserción del compilador.
> Por ejemplo, una conversión simple solo puede ser una compilada en la conversión, en lugar de una llamada a otro método para realizar la conversión.    

### <a name="key-comparers"></a>Comparadores de claves

En la sección de fondo se explica por qué las comparaciones de claves pueden requerir una semántica especial.
Asegúrese de crear un comparador que sea adecuado para las claves al establecerlo en una propiedad principal, principal o de clave externa.

Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) en los casos excepcionales en los que se requiere una semántica diferente en la misma propiedad.

> [!NOTE]  
> SetStructuralComparer se ha quedado obsoleto en EF Core 5,0.
> Use SetKeyValueComparer en su lugar.

### <a name="overriding-defaults"></a>Invalidación de los valores predeterminados

En ocasiones, es posible que la comparación predeterminada usada por EF Core no sea adecuada.
Por ejemplo, la mutación de matrices de bytes no se detecta, de forma predeterminada, en EF Core.
Esto se puede invalidar si se establece un comparador diferente en la propiedad: 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core ahora comparará las secuencias de bytes y, por tanto, detectará las mutaciones de matriz de bytes.
