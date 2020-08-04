---
title: 'Índices: EF Core'
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 9565b499ababace3595153e7159e017d2df1cc5a
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526750"
---
# <a name="indexes"></a>Índices

Los índices son un concepto común en muchos almacenes de datos. Aunque su implementación en el almacén de datos puede variar, se usan para realizar búsquedas basadas en una columna (o conjunto de columnas) más eficaces.

Los índices no se pueden crear con anotaciones de datos. Puede usar la API fluida para especificar un índice en una sola columna de la siguiente manera:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

También puede especificar un índice en más de una columna:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> Por Convención, se crea un índice en cada propiedad (o conjunto de propiedades) que se usa como clave externa.
>
> EF Core solo admite un índice por conjunto de propiedades distinto. Si usa la API fluida para configurar un índice en un conjunto de propiedades que ya tiene definido un índice, ya sea por Convención o por configuración anterior, cambiará la definición de ese índice. Esto resulta útil si desea seguir configurando un índice creado por la Convención.

## <a name="index-uniqueness"></a>Unicidad del índice

De forma predeterminada, los índices no son únicos: se permite que varias filas tengan los mismos valores para el conjunto de columnas del índice. Puede crear un índice único como se indica a continuación:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

Si se intenta insertar más de una entidad con los mismos valores para el conjunto de columnas del índice, se producirá una excepción.

## <a name="index-name"></a>Nombre del índice

Por Convención, los índices creados en una base de datos relacional se denominan `IX_<type name>_<property name>` . En el caso de los índices compuestos, `<property name>` se convierte en una lista de nombres de propiedad separados por guiones bajos.

Puede usar la API fluida para establecer el nombre del índice creado en la base de datos:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a>Filtro de índice

Algunas bases de datos relacionales permiten especificar un índice parcial o filtrado. Esto permite indizar solo un subconjunto de los valores de una columna, reduciendo el tamaño del índice y mejorando el rendimiento y el uso del espacio en disco. Para obtener más información sobre SQL Server los índices filtrados, [vea la documentación](/sql/relational-databases/indexes/create-filtered-indexes)de.

Puede usar la API fluida para especificar un filtro en un índice, proporcionado como una expresión SQL:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

Al usar el SQL Server el proveedor EF agrega un `'IS NOT NULL'` filtro para todas las columnas que aceptan valores NULL que forman parte de un índice único. Para invalidar esta Convención, puede proporcionar un `null` valor.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>Columnas incluidas

Algunas bases de datos relacionales permiten configurar un conjunto de columnas que se incluyen en el índice, pero que no forman parte de su "clave". Esto puede mejorar significativamente el rendimiento de las consultas cuando todas las columnas de la consulta se incluyen en el índice como columnas de clave o sin clave, ya que no es necesario tener acceso a la tabla en sí. Para obtener más información sobre SQL Server columnas incluidas, [vea la documentación](/sql/relational-databases/indexes/create-indexes-with-included-columns)de.

En el ejemplo siguiente, la `Url` columna forma parte de la clave de índice, por lo que cualquier filtrado de consultas en esa columna puede utilizar el índice. Pero además, las consultas que solo tienen acceso a las `Title` `PublishedOn` columnas y no tendrán que acceder a la tabla y se ejecutarán de forma más eficaz:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
