---
title: Comparaciones con valores NULL en las consultas
description: Información sobre cómo se controlan en Entity Framework Core las comparaciones de valores NULL en las consultas
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983357"
---
# <a name="query-null-semantics"></a><span data-ttu-id="b5ffe-103">Semántica de valores NULL en las consultas</span><span class="sxs-lookup"><span data-stu-id="b5ffe-103">Query null semantics</span></span>

## <a name="introduction"></a><span data-ttu-id="b5ffe-104">Introducción</span><span class="sxs-lookup"><span data-stu-id="b5ffe-104">Introduction</span></span>

<span data-ttu-id="b5ffe-105">Las bases de datos SQL operan en una lógica de tres valores (`true`, `false`, `null`) al realizar comparaciones, en lugar de la lógica booleana de C#.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-105">SQL databases operate on 3-valued logic (`true`, `false`, `null`) when performing comparisons, as opposed to the boolean logic of C#.</span></span> <span data-ttu-id="b5ffe-106">Al traducir consultas LINQ a SQL, EF Core intenta compensar la diferencia mediante la introducción de comprobaciones de valores NULL adicionales para algunos elementos de la consulta.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-106">When translating LINQ queries to SQL, EF Core tries to compensate for the difference by introducing additional null checks for some elements of the query.</span></span>
<span data-ttu-id="b5ffe-107">Para ilustrarlo, se definirá la siguiente entidad:</span><span class="sxs-lookup"><span data-stu-id="b5ffe-107">To illustrate this, let's define the following entity:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

<span data-ttu-id="b5ffe-108">Y se emitirán varias consultas:</span><span class="sxs-lookup"><span data-stu-id="b5ffe-108">and issue several queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

<span data-ttu-id="b5ffe-109">Las dos primeras consultas producen comparaciones simples.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-109">The first two queries produce simple comparisons.</span></span> <span data-ttu-id="b5ffe-110">En la primera consulta, las dos columnas no aceptan valores NULL, por lo que no se necesitan comprobaciones de valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-110">In the first query, both columns are non-nullable so null checks are not needed.</span></span> <span data-ttu-id="b5ffe-111">En la segunda consulta, `NullableInt` podría contener `null`, pero `Id` no acepta valores NULL; la comparación de `null` con los resultados no NULL da como resultado `null`, que se filtraría mediante la operación `WHERE`.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-111">In the second query, `NullableInt` could contain `null`, but `Id` is non-nullable; comparing `null` to non-null yields `null` as a result, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="b5ffe-112">Por tanto, no se necesita ningún término adicional.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-112">So no additional terms are needed either.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

<span data-ttu-id="b5ffe-113">La tercera consulta introduce una comprobación de valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-113">The third query introduces a null check.</span></span> <span data-ttu-id="b5ffe-114">Cuando `NullableInt` es `null`, la comparación `Id <> NullableInt` devuelve `null`, que se filtraría mediante la operación `WHERE`.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-114">When `NullableInt` is `null` the comparison `Id <> NullableInt` yields `null`, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="b5ffe-115">Pero desde el punto de vista de la lógica booleana, este caso se debe devolver como parte del resultado.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-115">However, from the boolean logic perspective this case should be returned as part of the result.</span></span> <span data-ttu-id="b5ffe-116">Por tanto, EF Core agrega la comprobación necesaria para garantizarlo.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-116">Hence EF Core adds the necessary check to ensure that.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

<span data-ttu-id="b5ffe-117">La cuarta y quinta consultas muestran el patrón cuando las dos columnas aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-117">Queries four and five show the pattern when both columns are nullable.</span></span> <span data-ttu-id="b5ffe-118">Merece la pena mencionar que la operación `<>` genera una consulta más complicada (y potencialmente más lenta) que la operación `==`.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-118">It's worth noting that the `<>` operation produces more complicated (and potentially slower) query than the `==` operation.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a><span data-ttu-id="b5ffe-119">Tratamiento de valores que admiten un valor NULL en las funciones</span><span class="sxs-lookup"><span data-stu-id="b5ffe-119">Treatment of nullable values in functions</span></span>

<span data-ttu-id="b5ffe-120">Muchas funciones de SQL solo pueden devolver un resultado `null` si algunos de sus argumentos son `null`.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-120">Many functions in SQL can only return a `null` result if some of their arguments are `null`.</span></span> <span data-ttu-id="b5ffe-121">EF Core aprovecha esto para generar consultas más eficaces.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-121">EF Core takes advantage of this to produce more efficient queries.</span></span>
<span data-ttu-id="b5ffe-122">En la consulta siguiente se muestra la optimización:</span><span class="sxs-lookup"><span data-stu-id="b5ffe-122">The query below illustrates the optimization:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

<span data-ttu-id="b5ffe-123">El código SQL generado es el siguiente (no es necesario evaluar la función `SUBSTRING`, ya que solo será NULL cuando alguno de los argumentos sea NULL):</span><span class="sxs-lookup"><span data-stu-id="b5ffe-123">The generated SQL is as follows (we don't need to evaluate the `SUBSTRING` function since it will be only null when either of the arguments to it is null.):</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

<span data-ttu-id="b5ffe-124">La optimización también se puede usar para funciones definidas por el usuario.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-124">The optimization can also be used for user-defined functions.</span></span> <span data-ttu-id="b5ffe-125">Vea la página de [asignación de funciones definidas por el usuario](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-125">See [user defined function mapping](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) page for more details.</span></span>

## <a name="writing-performant-queries"></a><span data-ttu-id="b5ffe-126">Escritura de consultas eficaces</span><span class="sxs-lookup"><span data-stu-id="b5ffe-126">Writing performant queries</span></span>

- <span data-ttu-id="b5ffe-127">La comparación de las columnas que no aceptan valores NULL es más sencilla y rápida que la de las columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-127">Comparing non-nullable columns is simpler and faster than comparing nullable columns.</span></span> <span data-ttu-id="b5ffe-128">Siempre que sea posible, considere la posibilidad de marcar las columnas como que no admiten valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-128">Consider marking columns as non-nullable whenever possible.</span></span>

- <span data-ttu-id="b5ffe-129">La comprobación de igualdad (`==`) es más sencilla y rápida que la de no igualdad (`!=`), ya que la consulta no necesita distinguir entre el resultado `null` y `false`.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-129">Checking for equality (`==`) is simpler and faster than checking for non-equality (`!=`), because query doesn't need to distinguish between `null` and `false` result.</span></span> <span data-ttu-id="b5ffe-130">Use la comparación de igualdad siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-130">Use equality comparison whenever possible.</span></span> <span data-ttu-id="b5ffe-131">Sin embargo, simplemente la negación de la comparación `==` es en realidad lo mismo que `!=`, por lo que no se mejora el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-131">However, simply negating `==` comparison is effectively the same as `!=`, so it doesn't result in performance improvement.</span></span>

- <span data-ttu-id="b5ffe-132">En algunos casos, es posible simplificar una comparación compleja si se filtran de forma explícita los valores `null` de una columna; por ejemplo, cuando no hay ningún valor `null` o estos valores no son relevantes en el resultado.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-132">In some cases, it is possible to simplify a complex comparison by filtering out `null` values from a column explicitly - for example when no `null` values are present or these values are not relevant in the result.</span></span> <span data-ttu-id="b5ffe-133">Considere el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b5ffe-133">Consider the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

<span data-ttu-id="b5ffe-134">Estas consultas generan el siguiente código SQL:</span><span class="sxs-lookup"><span data-stu-id="b5ffe-134">These queries produce the following SQL:</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

<span data-ttu-id="b5ffe-135">En la segunda consulta, los resultados `null` se filtran explícitamente de la columna `String1`.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-135">In the second query, `null` results are filtered out from `String1` column explicitly.</span></span> <span data-ttu-id="b5ffe-136">EF Core puede tratar de forma segura la columna `String1` como que no acepta valores NULL durante la comparación, lo que da lugar a una consulta más sencilla.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-136">EF Core can safely treat the `String1` column as non-nullable during comparison, resulting in a simpler query.</span></span>

## <a name="using-relational-null-semantics"></a><span data-ttu-id="b5ffe-137">Uso de la semántica relacional de valores NULL</span><span class="sxs-lookup"><span data-stu-id="b5ffe-137">Using relational null semantics</span></span>

<span data-ttu-id="b5ffe-138">Es posible deshabilitar la compensación de la comparación de valores NULL y usar directamente la semántica relacional de valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-138">It's possible to disable the null comparison compensation and use relational null semantics directly.</span></span> <span data-ttu-id="b5ffe-139">Esto se puede hacer mediante la llamada al método `UseRelationalNulls(true)` del generador de opciones dentro del método `OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="b5ffe-139">This can be done by calling `UseRelationalNulls(true)` method on the options builder inside `OnConfiguring` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> <span data-ttu-id="b5ffe-140">Cuando se usa la semántica relacional de valores NULL, las consultas LINQ ya no tienen el mismo significado que en C# y pueden producir resultados diferentes de los esperados.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-140">When using relational null semantics, your LINQ queries no longer have the same meaning as they do in C#, and may yield different results than expected.</span></span> <span data-ttu-id="b5ffe-141">Hay que ser prudentes al usar este modo.</span><span class="sxs-lookup"><span data-stu-id="b5ffe-141">Exercise caution when using this mode.</span></span>
