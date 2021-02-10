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
# <a name="query-null-semantics"></a>Semántica de valores NULL en las consultas

## <a name="introduction"></a>Introducción

Las bases de datos SQL operan en una lógica de tres valores (`true`, `false`, `null`) al realizar comparaciones, en lugar de la lógica booleana de C#. Al traducir consultas LINQ a SQL, EF Core intenta compensar la diferencia mediante la introducción de comprobaciones de valores NULL adicionales para algunos elementos de la consulta.
Para ilustrarlo, se definirá la siguiente entidad:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

Y se emitirán varias consultas:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

Las dos primeras consultas producen comparaciones simples. En la primera consulta, las dos columnas no aceptan valores NULL, por lo que no se necesitan comprobaciones de valores NULL. En la segunda consulta, `NullableInt` podría contener `null`, pero `Id` no acepta valores NULL; la comparación de `null` con los resultados no NULL da como resultado `null`, que se filtraría mediante la operación `WHERE`. Por tanto, no se necesita ningún término adicional.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

La tercera consulta introduce una comprobación de valores NULL. Cuando `NullableInt` es `null`, la comparación `Id <> NullableInt` devuelve `null`, que se filtraría mediante la operación `WHERE`. Pero desde el punto de vista de la lógica booleana, este caso se debe devolver como parte del resultado. Por tanto, EF Core agrega la comprobación necesaria para garantizarlo.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

La cuarta y quinta consultas muestran el patrón cuando las dos columnas aceptan valores NULL. Merece la pena mencionar que la operación `<>` genera una consulta más complicada (y potencialmente más lenta) que la operación `==`.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a>Tratamiento de valores que admiten un valor NULL en las funciones

Muchas funciones de SQL solo pueden devolver un resultado `null` si algunos de sus argumentos son `null`. EF Core aprovecha esto para generar consultas más eficaces.
En la consulta siguiente se muestra la optimización:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

El código SQL generado es el siguiente (no es necesario evaluar la función `SUBSTRING`, ya que solo será NULL cuando alguno de los argumentos sea NULL):

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

La optimización también se puede usar para funciones definidas por el usuario. Vea la página de [asignación de funciones definidas por el usuario](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) para obtener más detalles.

## <a name="writing-performant-queries"></a>Escritura de consultas eficaces

- La comparación de las columnas que no aceptan valores NULL es más sencilla y rápida que la de las columnas que aceptan valores NULL. Siempre que sea posible, considere la posibilidad de marcar las columnas como que no admiten valores NULL.

- La comprobación de igualdad (`==`) es más sencilla y rápida que la de no igualdad (`!=`), ya que la consulta no necesita distinguir entre el resultado `null` y `false`. Use la comparación de igualdad siempre que sea posible. Sin embargo, simplemente la negación de la comparación `==` es en realidad lo mismo que `!=`, por lo que no se mejora el rendimiento.

- En algunos casos, es posible simplificar una comparación compleja si se filtran de forma explícita los valores `null` de una columna; por ejemplo, cuando no hay ningún valor `null` o estos valores no son relevantes en el resultado. Considere el ejemplo siguiente:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

Estas consultas generan el siguiente código SQL:

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

En la segunda consulta, los resultados `null` se filtran explícitamente de la columna `String1`. EF Core puede tratar de forma segura la columna `String1` como que no acepta valores NULL durante la comparación, lo que da lugar a una consulta más sencilla.

## <a name="using-relational-null-semantics"></a>Uso de la semántica relacional de valores NULL

Es posible deshabilitar la compensación de la comparación de valores NULL y usar directamente la semántica relacional de valores NULL. Esto se puede hacer mediante la llamada al método `UseRelationalNulls(true)` del generador de opciones dentro del método `OnConfiguring`:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> Cuando se usa la semántica relacional de valores NULL, las consultas LINQ ya no tienen el mismo significado que en C# y pueden producir resultados diferentes de los esperados. Hay que ser prudentes al usar este modo.
