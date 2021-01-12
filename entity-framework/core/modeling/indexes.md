---
title: 'Índices: EF Core'
description: Configurar índices en un modelo de Entity Framework Core
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128542"
---
# <a name="indexes"></a>Índices

Los índices son un concepto común en muchos almacenes de datos. Aunque su implementación en el almacén de datos puede variar, se usan para realizar búsquedas basadas en una columna (o conjunto de columnas) más eficaces. Vea la [sección índices](xref:core/performance/efficient-querying#use-indexes-properly) de la documentación de rendimiento para obtener más información sobre el uso de índices correctos.

Puede especificar un índice en una columna de la manera siguiente:

## <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> La configuración de índices a través de anotaciones de datos se ha introducido en EF Core 5,0.

## <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> Por Convención, se crea un índice en cada propiedad (o conjunto de propiedades) que se usa como clave externa.
>
> EF Core solo admite un índice por conjunto de propiedades distinto. Si configura un índice en un conjunto de propiedades que ya tiene definido un índice, ya sea por Convención o por configuración anterior, cambiará la definición de ese índice. Esto resulta útil si desea seguir configurando un índice creado por la Convención.

## <a name="composite-index"></a>Índice compuesto

Un índice también puede abarcar más de una columna:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

**_

Los índices de varias columnas, también conocidos como índices de _composite *, agilizan las consultas que filtran las columnas del índice, pero también las consultas que solo filtran las *primeras* columnas que se incluyen en el índice. Vea los [documentos de rendimiento](xref:core/performance/efficient-querying#use-indexes-properly) para obtener más información.

## <a name="index-uniqueness"></a>Unicidad del índice

De forma predeterminada, los índices no son únicos: se permite que varias filas tengan los mismos valores para el conjunto de columnas del índice. Puede crear un índice único como se indica a continuación:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

Si se intenta insertar más de una entidad con los mismos valores para el conjunto de columnas del índice, se producirá una excepción.

## <a name="index-name"></a>Nombre del índice

Por Convención, los índices creados en una base de datos relacional se denominan `IX_<type name>_<property name>` . En el caso de los índices compuestos, `<property name>` se convierte en una lista de nombres de propiedad separados por guiones bajos.

Puede establecer el nombre del índice creado en la base de datos:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

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
