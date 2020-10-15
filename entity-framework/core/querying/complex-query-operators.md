---
title: 'Operadores de consulta complejos: EF Core'
description: Información detallada sobre los operadores de consulta LINQ más complejos cuando se usa Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 03375e6c46a68a719df82572333f0a57e7de6262
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062625"
---
# <a name="complex-query-operators"></a><span data-ttu-id="6bb8a-103">Operadores de consulta complejos</span><span class="sxs-lookup"><span data-stu-id="6bb8a-103">Complex Query Operators</span></span>

<span data-ttu-id="6bb8a-104">Language Integrated Query (LINQ) contiene muchos operadores complejos, que combinan varios orígenes de datos o realizan procesamientos complejos.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-104">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="6bb8a-105">No todos los operadores de LINQ tienen traducciones adecuadas en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-105">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="6bb8a-106">En ocasiones, una consulta en un formato se traduce en el servidor, pero si se escribe en otro formato, no se traduce aunque el resultado sea el mismo.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-106">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="6bb8a-107">En esta página se describen algunos de los operadores complejos y sus variaciones admitidas.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-107">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="6bb8a-108">En futuras versiones, es posible que se reconozcan más patrones y se agreguen sus correspondientes traducciones.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-108">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="6bb8a-109">También es importante tener en cuenta que la compatibilidad con la traducción varía entre proveedores.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-109">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="6bb8a-110">Es posible que una consulta determinada, que se traduzca en SqlServer, no funcione para bases de datos SQLite.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-110">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="6bb8a-111">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ComplexQuery) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-111">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ComplexQuery) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="6bb8a-112">Join</span><span class="sxs-lookup"><span data-stu-id="6bb8a-112">Join</span></span>

<span data-ttu-id="6bb8a-113">El operador Join de LINQ permite conectar dos orígenes de datos en función del selector de claves de cada origen, lo que genera una tupla de valores cuando la clave coincide.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-113">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="6bb8a-114">Se traduce de forma natural a `INNER JOIN` en las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-114">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="6bb8a-115">Aunque Join de LINQ tiene selectores de clave externa e interna, la base de datos requiere una única condición de combinación.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-115">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="6bb8a-116">Por tanto, EF Core genera una condición de combinación que compara el selector de clave externa con el selector de clave interna para determinar si son iguales.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-116">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="6bb8a-117">Además, si los selectores de clave son tipos anónimos, EF Core genera una condición de combinación para comparar los componentes de igualdad.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-117">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#Join)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="6bb8a-118">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="6bb8a-118">GroupJoin</span></span>

<span data-ttu-id="6bb8a-119">El operador GroupJoin de LINQ permite conectar dos orígenes de datos de forma similar a Join, pero crea un grupo de valores internos para los elementos externos coincidentes.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-119">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="6bb8a-120">Al ejecutar una consulta como la del ejemplo siguiente se genera un resultado de `Blog` & `IEnumerable<Post>`.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-120">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="6bb8a-121">Como las bases de datos (especialmente las relacionales) no tienen una manera de representar una colección de objetos del lado cliente, en muchos casos GroupJoin no se traduce en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-121">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="6bb8a-122">Requiere que se obtengan todos los datos del servidor para ejecutar GroupJoin sin un selector especial (la primera de las consultas siguientes).</span><span class="sxs-lookup"><span data-stu-id="6bb8a-122">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="6bb8a-123">Pero si el selector limita los datos que se seleccionan, la captura de todos los datos del servidor puede causar problemas de rendimiento (la segunda de las consultas siguientes).</span><span class="sxs-lookup"><span data-stu-id="6bb8a-123">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="6bb8a-124">Por eso EF Core no traduce GroupJoin.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-124">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="6bb8a-125">SelectMany</span><span class="sxs-lookup"><span data-stu-id="6bb8a-125">SelectMany</span></span>

<span data-ttu-id="6bb8a-126">El operador SelectMany de LINQ permite enumerar un selector de colecciones para cada elemento externo y generar tuplas de valores de cada origen de datos.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-126">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="6bb8a-127">En cierto modo es una combinación, pero sin ninguna condición, por lo que todos los elementos externos se conectan con un elemento del origen de la colección.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-127">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="6bb8a-128">En función de cómo se relacione el selector de colecciones con el origen de datos externo, SelectMany puede traducirse en varias consultas diferentes en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-128">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="6bb8a-129">El selector de colecciones no hace referencia al elemento externo</span><span class="sxs-lookup"><span data-stu-id="6bb8a-129">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="6bb8a-130">Cuando el selector de colecciones no hace referencia a nada del origen externo, el resultado es un producto cartesiano de ambos orígenes de datos.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-130">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="6bb8a-131">Se traduce a `CROSS JOIN` en las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-131">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToCrossJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="6bb8a-132">El selector de colecciones hace referencia al elemento externo en una cláusula WHERE</span><span class="sxs-lookup"><span data-stu-id="6bb8a-132">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="6bb8a-133">Cuando el selector de colecciones tiene una cláusula WHERE, que hace referencia al elemento exterior, EF Core lo traduce a una combinación de base de datos y usa el predicado como condición de combinación.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-133">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="6bb8a-134">Normalmente, este caso se produce cuando se usa la navegación de colección en el elemento exterior como selector de colecciones.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-134">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="6bb8a-135">Si la colección está vacía para un elemento externo, no se generarán resultados para ese elemento externo.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-135">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="6bb8a-136">Pero si se aplica `DefaultIfEmpty` en el selector de colecciones, el elemento exterior se conectará con un valor predeterminado del elemento interno.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-136">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="6bb8a-137">Debido a esta distinción, este tipo de consultas se traduce a `INNER JOIN` en ausencia de `DefaultIfEmpty` y `LEFT JOIN` cuando se aplica `DefaultIfEmpty`.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-137">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="6bb8a-138">El selector de colecciones hace referencia al elemento externo en una cláusula distinta de WHERE</span><span class="sxs-lookup"><span data-stu-id="6bb8a-138">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="6bb8a-139">Cuando el selector de colecciones hace referencia al elemento exterior, que no está en una cláusula WHERE (como en el caso anterior), no se traduce a una combinación de base de datos.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-139">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="6bb8a-140">Por este motivo es necesario evaluar el selector de colecciones para cada elemento exterior.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-140">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="6bb8a-141">Se traduce a operaciones `APPLY` en muchas bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-141">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="6bb8a-142">Si la colección está vacía para un elemento externo, no se generarán resultados para ese elemento externo.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-142">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="6bb8a-143">Pero si se aplica `DefaultIfEmpty` en el selector de colecciones, el elemento exterior se conectará con un valor predeterminado del elemento interno.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-143">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="6bb8a-144">Debido a esta distinción, este tipo de consultas se traduce a `CROSS APPLY` en ausencia de `DefaultIfEmpty` y `OUTER APPLY` cuando se aplica `DefaultIfEmpty`.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-144">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="6bb8a-145">Algunas bases de datos como SQLite no admiten los operadores `APPLY`, por lo que este tipo de consulta no se puede traducir.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-145">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToApply)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="6bb8a-146">GroupBy</span><span class="sxs-lookup"><span data-stu-id="6bb8a-146">GroupBy</span></span>

<span data-ttu-id="6bb8a-147">Los operadores GroupBy de LINQ crean un resultado de tipo `IGrouping<TKey, TElement>`, donde `TKey` y `TElement` podrían ser cualquier tipo arbitrario.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-147">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="6bb8a-148">Además, `IGrouping` implementa `IEnumerable<TElement>`, lo que significa que se puede redactar sobre este elemento con cualquier operador de LINQ después de la agrupación.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-148">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="6bb8a-149">Como ninguna estructura de base de datos puede representar una instancia de `IGrouping`, en la mayoría de los casos los operadores GroupBy no tienen ninguna traducción.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-149">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="6bb8a-150">Cuando se aplica un operador de agregado a cada grupo, lo que devuelve un valor escalar, se puede traducir a `GROUP BY` de SQL en las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-150">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="6bb8a-151">`GROUP BY` de SQL también es restrictivo.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-151">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="6bb8a-152">Requiere que se agrupe solo por valores escalares.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-152">It requires you to group only by scalar values.</span></span> <span data-ttu-id="6bb8a-153">La proyección solo puede contener columnas de clave de agrupación o cualquier agregado aplicado en una columna.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-153">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="6bb8a-154">EF Core identifica este patrón y lo traduce al servidor, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6bb8a-154">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupBy)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="6bb8a-155">EF Core también traduce las consultas en las que un operador de agregado en la agrupación aparece en un operador Where o OrderBy (u otro orden) de LINQ.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-155">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="6bb8a-156">Usa la cláusula `HAVING` en SQL para la cláusula WHERE.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-156">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="6bb8a-157">La parte de la consulta antes de aplicar el operador GroupBy puede ser cualquier consulta compleja, siempre que se pueda traducir al servidor.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-157">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="6bb8a-158">Además, una vez que se aplican operadores de agregado en una consulta de agrupación para quitar agrupaciones del origen resultante, se puede redactar sobre ella como cualquier otra consulta.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-158">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupByFilter)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="6bb8a-159">Los operadores de agregado que admite EF Core son los siguientes</span><span class="sxs-lookup"><span data-stu-id="6bb8a-159">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="6bb8a-160">Media</span><span class="sxs-lookup"><span data-stu-id="6bb8a-160">Average</span></span>
- <span data-ttu-id="6bb8a-161">Count</span><span class="sxs-lookup"><span data-stu-id="6bb8a-161">Count</span></span>
- <span data-ttu-id="6bb8a-162">LongCount</span><span class="sxs-lookup"><span data-stu-id="6bb8a-162">LongCount</span></span>
- <span data-ttu-id="6bb8a-163">Max</span><span class="sxs-lookup"><span data-stu-id="6bb8a-163">Max</span></span>
- <span data-ttu-id="6bb8a-164">Min</span><span class="sxs-lookup"><span data-stu-id="6bb8a-164">Min</span></span>
- <span data-ttu-id="6bb8a-165">Sum</span><span class="sxs-lookup"><span data-stu-id="6bb8a-165">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="6bb8a-166">Left Join</span><span class="sxs-lookup"><span data-stu-id="6bb8a-166">Left Join</span></span>

<span data-ttu-id="6bb8a-167">Aunque Left Join no es un operador de LINQ, las bases de datos relacionales tienen el concepto de combinación izquierda que se usa con frecuencia en las consultas.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-167">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="6bb8a-168">Un patrón determinado en las consultas LINQ proporciona el mismo resultado que `LEFT JOIN` en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-168">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="6bb8a-169">EF Core identifica estos patrones y genera la operación `LEFT JOIN` equivalente en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-169">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="6bb8a-170">El patrón implica la creación de GroupJoin entre los dos orígenes de datos y, después, la reducción de la agrupación mediante el operador SelectMany con DefaultIfEmpty en el origen de agrupación para que coincida con NULL cuando el elemento interior no tiene un elemento relacionado.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-170">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="6bb8a-171">En el ejemplo siguiente se muestra el aspecto de este patrón y lo que genera.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-171">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#LeftJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="6bb8a-172">En el patrón anterior se crea una estructura compleja en el árbol de expresión.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-172">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="6bb8a-173">Por eso, EF Core requiere que se reduzcan los resultados de agrupación del operador GroupJoin en un paso inmediatamente después del operador.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-173">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="6bb8a-174">Aunque se use GroupJoin-DefaultIfEmpty-SelectMany, pero en otro patrón, es posible que no se identifique como una combinación izquierda.</span><span class="sxs-lookup"><span data-stu-id="6bb8a-174">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
