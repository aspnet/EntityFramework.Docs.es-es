---
title: 'Operadores de consulta complejos: EF Core'
description: Información detallada sobre los operadores de consulta LINQ más complejos cuando se usa Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 2e187a2a-4072-4198-9040-aaad68e424fd
uid: core/querying/complex-query-operators
ms.openlocfilehash: 61a46361cc4bc1635174aaf2ca3260fa67fc6aaf
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616450"
---
# <a name="complex-query-operators"></a>Operadores de consulta complejos

Language Integrated Query (LINQ) contiene muchos operadores complejos, que combinan varios orígenes de datos o realizan procesamientos complejos. No todos los operadores de LINQ tienen traducciones adecuadas en el lado servidor. En ocasiones, una consulta en un formato se traduce en el servidor, pero si se escribe en otro formato, no se traduce aunque el resultado sea el mismo. En esta página se describen algunos de los operadores complejos y sus variaciones admitidas. En futuras versiones, es posible que se reconozcan más patrones y se agreguen sus correspondientes traducciones. También es importante tener en cuenta que la compatibilidad con la traducción varía entre proveedores. Es posible que una consulta determinada, que se traduzca en SqlServer, no funcione para bases de datos SQLite.

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) de este artículo en GitHub.

## <a name="join"></a>Join

El operador Join de LINQ permite conectar dos orígenes de datos en función del selector de claves de cada origen, lo que genera una tupla de valores cuando la clave coincide. Se traduce de forma natural a `INNER JOIN` en las bases de datos relacionales. Aunque Join de LINQ tiene selectores de clave externa e interna, la base de datos requiere una única condición de combinación. Por tanto, EF Core genera una condición de combinación que compara el selector de clave externa con el selector de clave interna para determinar si son iguales. Además, si los selectores de clave son tipos anónimos, EF Core genera una condición de combinación para comparar los componentes de igualdad.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a>GroupJoin

El operador GroupJoin de LINQ permite conectar dos orígenes de datos de forma similar a Join, pero crea un grupo de valores internos para los elementos externos coincidentes. Al ejecutar una consulta como la del ejemplo siguiente se genera un resultado de `Blog` & `IEnumerable<Post>`. Como las bases de datos (especialmente las relacionales) no tienen una manera de representar una colección de objetos del lado cliente, en muchos casos GroupJoin no se traduce en el servidor. Requiere que se obtengan todos los datos del servidor para ejecutar GroupJoin sin un selector especial (la primera de las consultas siguientes). Pero si el selector limita los datos que se seleccionan, la captura de todos los datos del servidor puede causar problemas de rendimiento (la segunda de las consultas siguientes). Por eso EF Core no traduce GroupJoin.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

El operador SelectMany de LINQ permite enumerar un selector de colecciones para cada elemento externo y generar tuplas de valores de cada origen de datos. En cierto modo es una combinación, pero sin ninguna condición, por lo que todos los elementos externos se conectan con un elemento del origen de la colección. En función de cómo se relacione el selector de colecciones con el origen de datos externo, SelectMany puede traducirse en varias consultas diferentes en el lado servidor.

### <a name="collection-selector-doesnt-reference-outer"></a>El selector de colecciones no hace referencia al elemento externo

Cuando el selector de colecciones no hace referencia a nada del origen externo, el resultado es un producto cartesiano de ambos orígenes de datos. Se traduce a `CROSS JOIN` en las bases de datos relacionales.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>El selector de colecciones hace referencia al elemento externo en una cláusula WHERE

Cuando el selector de colecciones tiene una cláusula WHERE, que hace referencia al elemento exterior, EF Core lo traduce a una combinación de base de datos y usa el predicado como condición de combinación. Normalmente, este caso se produce cuando se usa la navegación de colección en el elemento exterior como selector de colecciones. Si la colección está vacía para un elemento externo, no se generarán resultados para ese elemento externo. Pero si se aplica `DefaultIfEmpty` en el selector de colecciones, el elemento exterior se conectará con un valor predeterminado del elemento interno. Debido a esta distinción, este tipo de consultas se traduce a `INNER JOIN` en ausencia de `DefaultIfEmpty` y `LEFT JOIN` cuando se aplica `DefaultIfEmpty`.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>El selector de colecciones hace referencia al elemento externo en una cláusula distinta de WHERE

Cuando el selector de colecciones hace referencia al elemento exterior, que no está en una cláusula WHERE (como en el caso anterior), no se traduce a una combinación de base de datos. Por este motivo es necesario evaluar el selector de colecciones para cada elemento exterior. Se traduce a operaciones `APPLY` en muchas bases de datos relacionales. Si la colección está vacía para un elemento externo, no se generarán resultados para ese elemento externo. Pero si se aplica `DefaultIfEmpty` en el selector de colecciones, el elemento exterior se conectará con un valor predeterminado del elemento interno. Debido a esta distinción, este tipo de consultas se traduce a `CROSS APPLY` en ausencia de `DefaultIfEmpty` y `OUTER APPLY` cuando se aplica `DefaultIfEmpty`. Algunas bases de datos como SQLite no admiten los operadores `APPLY`, por lo que este tipo de consulta no se puede traducir.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a>GroupBy

Los operadores GroupBy de LINQ crean un resultado de tipo `IGrouping<TKey, TElement>`, donde `TKey` y `TElement` podrían ser cualquier tipo arbitrario. Además, `IGrouping` implementa `IEnumerable<TElement>`, lo que significa que se puede redactar sobre este elemento con cualquier operador de LINQ después de la agrupación. Como ninguna estructura de base de datos puede representar una instancia de `IGrouping`, en la mayoría de los casos los operadores GroupBy no tienen ninguna traducción. Cuando se aplica un operador de agregado a cada grupo, lo que devuelve un valor escalar, se puede traducir a `GROUP BY` de SQL en las bases de datos relacionales. `GROUP BY` de SQL también es restrictivo. Requiere que se agrupe solo por valores escalares. La proyección solo puede contener columnas de clave de agrupación o cualquier agregado aplicado en una columna. EF Core identifica este patrón y lo traduce al servidor, como en el ejemplo siguiente:

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

EF Core también traduce las consultas en las que un operador de agregado en la agrupación aparece en un operador Where o OrderBy (u otro orden) de LINQ. Usa la cláusula `HAVING` en SQL para la cláusula WHERE. La parte de la consulta antes de aplicar el operador GroupBy puede ser cualquier consulta compleja, siempre que se pueda traducir al servidor. Además, una vez que se aplican operadores de agregado en una consulta de agrupación para quitar agrupaciones del origen resultante, se puede redactar sobre ella como cualquier otra consulta.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

Los operadores de agregado que admite EF Core son los siguientes

- Media
- Count
- LongCount
- Max
- Min
- Sum

## <a name="left-join"></a>Left Join

Aunque Left Join no es un operador de LINQ, las bases de datos relacionales tienen el concepto de combinación izquierda que se usa con frecuencia en las consultas. Un patrón determinado en las consultas LINQ proporciona el mismo resultado que `LEFT JOIN` en el servidor. EF Core identifica estos patrones y genera la operación `LEFT JOIN` equivalente en el lado servidor. El patrón implica la creación de GroupJoin entre los dos orígenes de datos y, después, la reducción de la agrupación mediante el operador SelectMany con DefaultIfEmpty en el origen de agrupación para que coincida con NULL cuando el elemento interior no tiene un elemento relacionado. En el ejemplo siguiente se muestra el aspecto de este patrón y lo que genera.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

En el patrón anterior se crea una estructura compleja en el árbol de expresión. Por eso, EF Core requiere que se reduzcan los resultados de agrupación del operador GroupJoin en un paso inmediatamente después del operador. Aunque se use GroupJoin-DefaultIfEmpty-SelectMany, pero en otro patrón, es posible que no se identifique como una combinación izquierda.
