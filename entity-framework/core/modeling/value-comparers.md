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
# <a name="value-comparers"></a>Comparadores de valores

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 3.0.

> [!TIP]
> El código de este documento se puede encontrar en GitHub como un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Información previa

El seguimiento de cambios significa que EF Core determina automáticamente qué cambios realizó la aplicación en una instancia de entidad cargada, de modo que esos cambios se pueden volver a guardar en la base de datos cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a. Normalmente, EF Core realiza esta tarea tomando una *instantánea* de la instancia cuando se carga desde la base de datos y *comparando* esa instantánea con la instancia entregada a la aplicación.

EF Core incluye lógica integrada para la creación de instantáneas y la comparación de la mayoría de los tipos estándar que se usan en las bases de datos, por lo que los usuarios no suelen tener que preocuparse de este tema. Sin embargo, cuando se asigna una propiedad a través de un [convertidor de valores](xref:core/modeling/value-conversions), EF Core debe realizar la comparación en los tipos de usuario arbitrarios, que pueden ser complejos. De forma predeterminada, EF Core usa la comparación de igualdad predeterminada definida por los tipos (por ejemplo `Equals` , el método); para la toma de instantáneas, los tipos de valor se copian para generar la instantánea, mientras que para los tipos de referencia no se realiza ninguna copia y se usa la misma instancia como la instantánea.

En los casos en los que el comportamiento de comparación integrado no es adecuado, los usuarios pueden proporcionar un *comparador de valores*, que contiene la lógica para la toma de instantáneas, la comparación y el cálculo de un código hash. Por ejemplo, lo siguiente configura la conversión de valores para `List<int>` que la propiedad se convierta en una cadena JSON en la base de datos y define también un comparador de valores adecuado:

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Vea [clases mutables](#mutable-classes) a continuación para obtener más detalles.

Tenga en cuenta que los comparadores de valores también se usan para determinar si dos valores de clave son iguales al resolver las relaciones; Esto se explica a continuación.

## <a name="shallow-vs-deep-comparison"></a>Comparación superficial frente a profunda

En el caso de los tipos de valor pequeños e inmutables como `int` , la lógica predeterminada de EF Core funciona bien: el valor se copia tal cual cuando se realiza instantáneas y se compara con la comparación de igualdad integrada del tipo. Al implementar su propio comparador de valores, es importante tener en cuenta si la lógica de comparación profunda o superficial (y de la instantánea) es adecuada.

Considere las matrices de bytes, que pueden ser arbitrariamente grandes. Se pueden comparar:

* Por referencia, de modo que solo se detecta una diferencia si se usa una nueva matriz de bytes
* Mediante una comparación profunda, de modo que se detecta la mutación de los bytes de la matriz

De forma predeterminada, EF Core usa el primero de estos enfoques para las matrices de bytes que no son de clave. Es decir, solo se comparan las referencias y se detecta un cambio solo cuando una matriz de bytes existente se reemplaza por otra nueva. Se trata de una decisión pragmática que evita copiar matrices completas y compararlas de byte a byte al ejecutarse <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , y el escenario habitual de reemplazar, por ejemplo, una imagen por otra se administra de forma eficaz.

Por otro lado, la igualdad de referencia no funcionaría cuando se usan matrices de bytes para representar claves binarias, ya que es muy poco probable que una propiedad FK esté establecida en la _misma instancia_ que una propiedad PK en la que se debe comparar. Por lo tanto, EF Core utiliza comparaciones en profundidad para las matrices de bytes que actúan como claves. no es probable que esto afecte al rendimiento, ya que las claves binarias suelen ser cortas.

Tenga en cuenta que la comparación y la lógica de la instantánea elegidos deben corresponderse entre sí: la comparación profunda requiere una instantánea profunda para funcionar correctamente.

## <a name="simple-immutable-classes"></a>Clases inmutables simples

Considere una propiedad que utiliza un convertidor de valores para asignar una clase simple e inmutable.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Las propiedades de este tipo no necesitan comparaciones especiales ni instantáneas porque:

* La igualdad se invalida para que las distintas instancias se comparen correctamente
* El tipo es inmutable, por lo que no existe la posibilidad de alterar un valor de instantánea

Por lo tanto, en este caso, el comportamiento predeterminado de EF Core es correcto.

## <a name="simple-immutable-structs"></a>Structs inmutables simples

La asignación para Structs simples también es sencilla y no requiere comparaciones o instantáneas especiales.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core tiene compatibilidad integrada para generar comparaciones compiladas miembro a miembro de propiedades de struct.
Esto significa que no es necesario que los Structs tengan una igualdad invalidada para EF Core, pero todavía puede optar por [otras razones](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).
Además, no es necesario realizar una instantánea especial, ya que las estructuras son inmutables y siempre se copian de miembro a miembro.
(Esto también se aplica a las estructuras mutables, pero las [estructuras mutables deberían evitarse en general](/dotnet/csharp/write-safe-efficient-code)).

## <a name="mutable-classes"></a>Clases mutables

Se recomienda usar tipos inmutables (clases o Structs) con convertidores de valores siempre que sea posible.
Esto suele ser más eficaz y tiene una semántica más clara que el uso de un tipo mutable.

Sin embargo, es habitual usar las propiedades de los tipos que la aplicación no puede cambiar.
Por ejemplo, asignar una propiedad que contenga una lista de números:

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

La clase <xref:System.Collections.Generic.List%601>:

* Tiene igualdad de referencia; dos listas que contienen los mismos valores se tratan como diferentes.
* Es mutable; los valores de la lista se pueden agregar y quitar.

Una conversión de valor típica en una propiedad de lista puede convertir la lista a y desde JSON:

### <a name="ef-core-50"></a>[EF Core 5.0](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[Versiones anteriores](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

El <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructor acepta tres expresiones:

* Expresión para comprobar la igualdad
* Expresión para generar un código hash.
* Una expresión para la instantánea de un valor

En este caso, la comparación se realiza comprobando si las secuencias de números son iguales.

Del mismo modo, el código hash se genera a partir de esta misma secuencia.
(Tenga en cuenta que se trata de un código hash sobre valores mutables y, por lo tanto, puede [causar problemas](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).
En su lugar, puede ser inmutable si es posible).

La instantánea se crea mediante la clonación de la lista con `ToList` .
De nuevo, esto solo es necesario si se van a mutar las listas.
En su lugar, puede ser inmutable si es posible.

> [!NOTE]
> Los convertidores de valores y los comparadores se construyen mediante expresiones en lugar de delegados simples.
> Esto se debe a que EF Core inserta estas expresiones en un árbol de expresión mucho más complejo que se compila en un delegado de forma de entidad.
> Conceptualmente, esto es similar a la inserción del compilador.
> Por ejemplo, una conversión simple solo puede ser una compilada en la conversión, en lugar de una llamada a otro método para realizar la conversión.

## <a name="key-comparers"></a>Comparadores de claves

En la sección de fondo se explica por qué las comparaciones de claves pueden requerir una semántica especial.
Asegúrese de crear un comparador que sea adecuado para las claves al establecerlo en una propiedad principal, principal o de clave externa.

Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> en los casos poco frecuentes en los que se requiere una semántica diferente en la misma propiedad.

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> se ha quedado obsoleto en EF Core 5,0. En su lugar, use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>.

## <a name="overriding-the-default-comparer"></a>Reemplazar el comparador predeterminado

En ocasiones, es posible que la comparación predeterminada usada por EF Core no sea adecuada.
Por ejemplo, la mutación de matrices de bytes no se detecta, de forma predeterminada, en EF Core.
Esto se puede invalidar si se establece un comparador diferente en la propiedad:

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core ahora comparará las secuencias de bytes y, por tanto, detectará las mutaciones de matriz de bytes.
