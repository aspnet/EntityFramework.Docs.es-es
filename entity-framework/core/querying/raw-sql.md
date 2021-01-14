---
title: 'Consultas SQL sin formato: EF Core'
description: Uso de SQL sin formato para consultas en Entity Framework Core
author: smitpatel
ms.date: 10/08/2019
uid: core/querying/raw-sql
ms.openlocfilehash: 56724f9fddc9126fd4dfe0f348a0c525b43a3478
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128386"
---
# <a name="raw-sql-queries"></a>Consultas SQL sin formato

Entity Framework Core le permite descender hasta las consultas SQL sin formato cuando trabaja con una base de datos relacional. Las consultas SQL sin formato son útiles si la consulta que quiere no se puede expresar mediante LINQ. Las consultas SQL sin formato también se utilizan si el uso de una consulta LINQ genera una consulta SQL ineficaz. Las consultas SQL sin formato pueden devolver tipos de entidad normales o [tipos de entidad sin clave](xref:core/modeling/keyless-entity-types) que forman parte del modelo.

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL) de este artículo en GitHub.

## <a name="basic-raw-sql-queries"></a>Consultas SQL básicas sin formato

Puede usar el método de extensión `FromSqlRaw` para empezar una consulta LINQ basada en una consulta SQL sin formato. `FromSqlRaw` solo se puede usar en raíces de consulta, es decir, directamente en `DbSet<>`.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRaw)]

Las consultas SQL sin formato se pueden usar para ejecutar un procedimiento almacenado.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a>Pasar parámetros

> [!WARNING]
> **Use siempre la parametrización para las consultas SQL sin formato**
>
> Al indicar cualquier valor proporcionado por el usuario en una consulta SQL sin formato, debe tener cuidado para evitar ataques por inyección de código SQL. Además de validar que dichos valores no contienen caracteres no válidos, use siempre la parametrización que envía los valores separados del texto SQL.
>
> En concreto, no pase nunca a `FromSqlRaw` o `ExecuteSqlRaw` una cadena concatenada o interpolada (`$""`) con valores proporcionados por el usuario sin validar. Los métodos `FromSqlInterpolated` y `ExecuteSqlInterpolated` permiten usar la sintaxis de interpolación de cadenas de manera que se protege frente a los ataques por inyección de código SQL.

En el ejemplo siguiente se pasa un parámetro único a un procedimiento almacenado; para ello, se incluye un marcador de posición de parámetro en la cadena de consulta SQL y se proporciona un argumento adicional. Aunque esta sintaxis se pueda parecer a la de `String.Format`, el valor suministrado se encapsula en un elemento `DbParameter` y el nombre del parámetro generado se inserta donde se haya especificado el marcador de posición `{0}`.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureParameter)]

`FromSqlInterpolated` es similar a `FromSqlRaw`, pero permite usar la sintaxis de interpolación de cadenas. Al igual que `FromSqlRaw`, `FromSqlInterpolated` solo se puede usar en raíces de consulta. Como en el ejemplo anterior, el valor se convierte a `DbParameter` y no es vulnerable a la inyección de código SQL.

> [!NOTE]
> Antes de la versión 3.0, `FromSqlRaw` y `FromSqlInterpolated` eran dos sobrecargas denominadas `FromSql`. Para obtener más información, vea la [sección sobre versiones anteriores](#previous-versions).

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedStoredProcedureParameter)]

También puede construir un elemento DbParameter y suministrarlo como un valor de parámetro. Dado que se usa un marcador de posición de parámetro SQL normal, en lugar de un marcador de posición de cadena, `FromSqlRaw` se puede usar de forma segura:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureSqlParameter)]

`FromSqlRaw` permite usar parámetros con nombre en la cadena de consulta SQL, lo que resulta útil cuando un procedimiento almacenado tiene parámetros opcionales:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> **Orden de los parámetros** Entity Framework Core pasa parámetros basados en el orden de la matriz de `SqlParameter[]`. Al pasar varios `SqlParameter`, el orden de la cadena SQL debe coincidir con el orden de los parámetros en la definición del procedimiento almacenado. Si no lo hace, al ejecutar el procedimiento pueden producirse excepciones de conversión de tipos o un comportamiento inesperado.

## <a name="composing-with-linq"></a>Redacción con LINQ

Puede redactar sobre la consulta SQL sin formato inicial mediante operadores de LINQ. EF Core la tratará como una subconsulta y redactará sobre ella en la base de datos. En el ejemplo siguiente se usa una consulta SQL sin formato que realiza una selección en una función con valores de tabla (TVF). Y después se redacta sobre ella con LINQ para realizar el filtrado y la ordenación.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedComposed)]

La consulta anterior genera el código SQL siguiente:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a>Inclusión de datos relacionados

El método `Include` puede usarse para incluir datos relacionados, igual que cualquier otra consulta LINQ:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedInclude)]

La redacción con LINQ requiere que la consulta SQL sin procesar se pueda redactar, ya que EF Core tratará el código SQL proporcionado como una subconsulta. Las consultas SQL que se pueden redactar empiezan con la palabra clave `SELECT`. Es más, el código SQL que se pasa no debe contener ningún carácter ni opción que no sea válido en una subconsulta, como los siguientes:

- Un punto y coma final
- En SQL Server, una sugerencia en el nivel de consulta final (por ejemplo, `OPTION (HASH JOIN)`)
- En SQL Server, una cláusula `ORDER BY` que no se usa con `OFFSET 0` O BIEN `TOP 100 PERCENT` en la cláusula `SELECT`

SQL Server no permite la redacción sobre llamadas a procedimientos almacenados, por lo que cualquier intento de aplicar operadores de consulta adicionales a ese tipo de llamada producirá código SQL no válido. Use el método `AsEnumerable` o `AsAsyncEnumerable` justo después de los métodos `FromSqlRaw` o `FromSqlInterpolated` para asegurarse de que EF Core no intente redactar sobre un procedimiento almacenado.

## <a name="change-tracking"></a>Seguimiento de cambios

Las consultas que usan los métodos `FromSqlRaw` o `FromSqlInterpolated` siguen las mismas reglas de seguimiento de cambios que las demás consultas LINQ en EF Core. Por ejemplo, si la consulta proyecta tipos de entidad, se realizará un seguimiento de los resultados de forma predeterminada.

En el ejemplo siguiente se usa una consulta SQL sin formato que realiza una selección en una función con valores de tabla (TVF) y después deshabilita el seguimiento de cambios con la llamada a `AsNoTracking`:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a>Limitaciones

Existen algunas limitaciones que debe considerar al usar las consultas SQL sin formato:

- La consulta SQL debe devolver datos para todas las propiedades del tipo de entidad.
- Los nombres de las columnas del conjunto de resultados deben coincidir con los nombres de las columnas a los que se asignan las propiedades. Tenga en cuenta que este comportamiento es diferente al de EF6. En EF6 se omitía la asignación de propiedades y columnas para las consultas SQL sin formato, y los nombres de las columnas del conjunto de resultados tenían que coincidir con los nombres de las propiedades.
- La consulta SQL no puede contener datos relacionados. Sin embargo, en muchos casos puede redactar sobre la consulta si usa el operador `Include` para devolver datos relacionados (consulte [Inclusión de datos relacionados](#including-related-data)).

## <a name="previous-versions"></a>Versiones anteriores

EF Core 2.2 y las versiones anteriores tenían dos sobrecargas de método denominadas `FromSql`, que se comportaban de la misma manera que las sobrecargas `FromSqlRaw` y `FromSqlInterpolated` más recientes. Resultaba sencillo llamar de forma accidental al método de cadena sin formato cuando la intención era llamar al método de cadena interpolada y viceversa. La llamada accidental a la sobrecarga incorrecta podría generar como resultado consultas que no se parametrizaban cuando debían.
