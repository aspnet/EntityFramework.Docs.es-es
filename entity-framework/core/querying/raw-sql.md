---
title: 'Consultas SQL sin formato: EF Core'
description: Uso de SQL sin formato para consultas en Entity Framework Core
author: smitpatel
ms.date: 10/08/2019
uid: core/querying/raw-sql
ms.openlocfilehash: 13f5cbfbd7a110394402bff74d51b5fcda04c642
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071139"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="4083f-103">Consultas SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="4083f-103">Raw SQL Queries</span></span>

<span data-ttu-id="4083f-104">Entity Framework Core le permite descender hasta las consultas SQL sin formato cuando trabaja con una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="4083f-104">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="4083f-105">Las consultas SQL sin formato son útiles si la consulta que quiere no se puede expresar mediante LINQ.</span><span class="sxs-lookup"><span data-stu-id="4083f-105">Raw SQL queries are useful if the query you want can't be expressed using LINQ.</span></span> <span data-ttu-id="4083f-106">Las consultas SQL sin formato también se utilizan si el uso de una consulta LINQ genera una consulta SQL ineficaz.</span><span class="sxs-lookup"><span data-stu-id="4083f-106">Raw SQL queries are also used if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="4083f-107">Las consultas SQL sin formato pueden devolver tipos de entidad normales o [tipos de entidad sin clave](xref:core/modeling/keyless-entity-types) que forman parte del modelo.</span><span class="sxs-lookup"><span data-stu-id="4083f-107">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="4083f-108">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="4083f-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="4083f-109">Consultas SQL básicas sin formato</span><span class="sxs-lookup"><span data-stu-id="4083f-109">Basic raw SQL queries</span></span>

<span data-ttu-id="4083f-110">Puede usar el método de extensión `FromSqlRaw` para empezar una consulta LINQ basada en una consulta SQL sin formato.</span><span class="sxs-lookup"><span data-stu-id="4083f-110">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span> <span data-ttu-id="4083f-111">`FromSqlRaw` solo se puede usar en raíces de consulta, es decir, directamente en `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="4083f-111">`FromSqlRaw` can only be used on query roots, that is directly on the `DbSet<>`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

<span data-ttu-id="4083f-112">Las consultas SQL sin formato se pueden usar para ejecutar un procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="4083f-112">Raw SQL queries can be used to execute a stored procedure.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a><span data-ttu-id="4083f-113">Pasar parámetros</span><span class="sxs-lookup"><span data-stu-id="4083f-113">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="4083f-114">**Use siempre la parametrización para las consultas SQL sin formato**</span><span class="sxs-lookup"><span data-stu-id="4083f-114">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="4083f-115">Al indicar cualquier valor proporcionado por el usuario en una consulta SQL sin formato, debe tener cuidado para evitar ataques por inyección de código SQL.</span><span class="sxs-lookup"><span data-stu-id="4083f-115">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="4083f-116">Además de validar que dichos valores no contienen caracteres no válidos, use siempre la parametrización que envía los valores separados del texto SQL.</span><span class="sxs-lookup"><span data-stu-id="4083f-116">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="4083f-117">En concreto, no pase nunca a `FromSqlRaw` o `ExecuteSqlRaw` una cadena concatenada o interpolada (`$""`) con valores proporcionados por el usuario sin validar.</span><span class="sxs-lookup"><span data-stu-id="4083f-117">In particular, never pass a concatenated or interpolated string (`$""`) with non-validated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="4083f-118">Los métodos `FromSqlInterpolated` y `ExecuteSqlInterpolated` permiten usar la sintaxis de interpolación de cadenas de manera que se protege frente a los ataques por inyección de código SQL.</span><span class="sxs-lookup"><span data-stu-id="4083f-118">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="4083f-119">En el ejemplo siguiente se pasa un parámetro único a un procedimiento almacenado; para ello, se incluye un marcador de posición de parámetro en la cadena de consulta SQL y se proporciona un argumento adicional.</span><span class="sxs-lookup"><span data-stu-id="4083f-119">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="4083f-120">Aunque esta sintaxis se pueda parecer a la de `String.Format`, el valor suministrado se encapsula en un elemento `DbParameter` y el nombre del parámetro generado se inserta donde se haya especificado el marcador de posición `{0}`.</span><span class="sxs-lookup"><span data-stu-id="4083f-120">While this syntax may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

<span data-ttu-id="4083f-121">`FromSqlInterpolated` es similar a `FromSqlRaw`, pero permite usar la sintaxis de interpolación de cadenas.</span><span class="sxs-lookup"><span data-stu-id="4083f-121">`FromSqlInterpolated` is similar to `FromSqlRaw` but allows you to use string interpolation syntax.</span></span> <span data-ttu-id="4083f-122">Al igual que `FromSqlRaw`, `FromSqlInterpolated` solo se puede usar en raíces de consulta.</span><span class="sxs-lookup"><span data-stu-id="4083f-122">Just like `FromSqlRaw`, `FromSqlInterpolated` can only be used on query roots.</span></span> <span data-ttu-id="4083f-123">Como en el ejemplo anterior, el valor se convierte a `DbParameter` y no es vulnerable a la inyección de código SQL.</span><span class="sxs-lookup"><span data-stu-id="4083f-123">As with the previous example, the value is converted to a `DbParameter` and isn't vulnerable to SQL injection.</span></span>

> [!NOTE]
> <span data-ttu-id="4083f-124">Antes de la versión 3.0, `FromSqlRaw` y `FromSqlInterpolated` eran dos sobrecargas denominadas `FromSql`.</span><span class="sxs-lookup"><span data-stu-id="4083f-124">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="4083f-125">Para obtener más información, vea la [sección sobre versiones anteriores](#previous-versions).</span><span class="sxs-lookup"><span data-stu-id="4083f-125">For more information, see the [previous versions section](#previous-versions).</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

<span data-ttu-id="4083f-126">También puede construir un elemento DbParameter y suministrarlo como un valor de parámetro.</span><span class="sxs-lookup"><span data-stu-id="4083f-126">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="4083f-127">Dado que se usa un marcador de posición de parámetro SQL normal, en lugar de un marcador de posición de cadena, `FromSqlRaw` se puede usar de forma segura:</span><span class="sxs-lookup"><span data-stu-id="4083f-127">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

<span data-ttu-id="4083f-128">`FromSqlRaw` permite usar parámetros con nombre en la cadena de consulta SQL, lo que resulta útil cuando un procedimiento almacenado tiene parámetros opcionales:</span><span class="sxs-lookup"><span data-stu-id="4083f-128">`FromSqlRaw` allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> <span data-ttu-id="4083f-129">**Orden de los parámetros** Entity Framework Core pasa parámetros basados en el orden de la matriz de `SqlParameter[]`.</span><span class="sxs-lookup"><span data-stu-id="4083f-129">**Parameter Ordering** Entity Framework Core passes parameters based on the order of the `SqlParameter[]` array.</span></span> <span data-ttu-id="4083f-130">Al pasar varios `SqlParameter`, el orden de la cadena SQL debe coincidir con el orden de los parámetros en la definición del procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="4083f-130">When passing multiple `SqlParameter`s, the ordering in the SQL string must match the order of the parameters in the stored procedure's definition.</span></span> <span data-ttu-id="4083f-131">Si no lo hace, al ejecutar el procedimiento pueden producirse excepciones de conversión de tipos o un comportamiento inesperado.</span><span class="sxs-lookup"><span data-stu-id="4083f-131">Failure to do this may result in type conversion exceptions and/or unexpected behavior when the procedure is executed.</span></span>

## <a name="composing-with-linq"></a><span data-ttu-id="4083f-132">Redacción con LINQ</span><span class="sxs-lookup"><span data-stu-id="4083f-132">Composing with LINQ</span></span>

<span data-ttu-id="4083f-133">Puede redactar sobre la consulta SQL sin formato inicial mediante operadores de LINQ.</span><span class="sxs-lookup"><span data-stu-id="4083f-133">You can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="4083f-134">EF Core la tratará como una subconsulta y redactará sobre ella en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4083f-134">EF Core will treat it as subquery and compose over it in the database.</span></span> <span data-ttu-id="4083f-135">En el ejemplo siguiente se usa una consulta SQL sin formato que realiza una selección en una función con valores de tabla (TVF).</span><span class="sxs-lookup"><span data-stu-id="4083f-135">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF).</span></span> <span data-ttu-id="4083f-136">Y después se redacta sobre ella con LINQ para realizar el filtrado y la ordenación.</span><span class="sxs-lookup"><span data-stu-id="4083f-136">And then composes on it using LINQ to do filtering and sorting.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

<span data-ttu-id="4083f-137">La consulta anterior genera el código SQL siguiente:</span><span class="sxs-lookup"><span data-stu-id="4083f-137">Above query generates following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a><span data-ttu-id="4083f-138">Inclusión de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="4083f-138">Including related data</span></span>

<span data-ttu-id="4083f-139">El método `Include` puede usarse para incluir datos relacionados, igual que cualquier otra consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="4083f-139">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

<span data-ttu-id="4083f-140">La redacción con LINQ requiere que la consulta SQL sin procesar se pueda redactar, ya que EF Core tratará el código SQL proporcionado como una subconsulta.</span><span class="sxs-lookup"><span data-stu-id="4083f-140">Composing with LINQ requires your raw SQL query to be composable since EF Core will treat the supplied SQL as a subquery.</span></span> <span data-ttu-id="4083f-141">Las consultas SQL que se pueden redactar empiezan con la palabra clave `SELECT`.</span><span class="sxs-lookup"><span data-stu-id="4083f-141">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span> <span data-ttu-id="4083f-142">Es más, el código SQL que se pasa no debe contener ningún carácter ni opción que no sea válido en una subconsulta, como los siguientes:</span><span class="sxs-lookup"><span data-stu-id="4083f-142">Further, SQL passed shouldn't contain any characters or options that aren't valid on a subquery, such as:</span></span>

- <span data-ttu-id="4083f-143">Un punto y coma final</span><span class="sxs-lookup"><span data-stu-id="4083f-143">A trailing semicolon</span></span>
- <span data-ttu-id="4083f-144">En SQL Server, una sugerencia en el nivel de consulta final (por ejemplo, `OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="4083f-144">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
- <span data-ttu-id="4083f-145">En SQL Server, una cláusula `ORDER BY` que no se usa con `OFFSET 0` O BIEN `TOP 100 PERCENT` en la cláusula `SELECT`</span><span class="sxs-lookup"><span data-stu-id="4083f-145">On SQL Server, an `ORDER BY` clause that isn't used with `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

<span data-ttu-id="4083f-146">SQL Server no permite la redacción sobre llamadas a procedimientos almacenados, por lo que cualquier intento de aplicar operadores de consulta adicionales a ese tipo de llamada producirá código SQL no válido.</span><span class="sxs-lookup"><span data-stu-id="4083f-146">SQL Server doesn't allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="4083f-147">Use el método `AsEnumerable` o `AsAsyncEnumerable` justo después de los métodos `FromSqlRaw` o `FromSqlInterpolated` para asegurarse de que EF Core no intente redactar sobre un procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="4083f-147">Use `AsEnumerable` or `AsAsyncEnumerable` method right after `FromSqlRaw` or `FromSqlInterpolated` methods to make sure that EF Core doesn't try to compose over a stored procedure.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="4083f-148">Seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="4083f-148">Change Tracking</span></span>

<span data-ttu-id="4083f-149">Las consultas que usan los métodos `FromSqlRaw` o `FromSqlInterpolated` siguen las mismas reglas de seguimiento de cambios que las demás consultas LINQ en EF Core.</span><span class="sxs-lookup"><span data-stu-id="4083f-149">Queries that use the `FromSqlRaw` or `FromSqlInterpolated` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="4083f-150">Por ejemplo, si la consulta proyecta tipos de entidad, se realizará un seguimiento de los resultados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4083f-150">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="4083f-151">En el ejemplo siguiente se usa una consulta SQL sin formato que realiza una selección en una función con valores de tabla (TVF) y después deshabilita el seguimiento de cambios con la llamada a `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="4083f-151">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a><span data-ttu-id="4083f-152">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="4083f-152">Limitations</span></span>

<span data-ttu-id="4083f-153">Existen algunas limitaciones que debe considerar al usar las consultas SQL sin formato:</span><span class="sxs-lookup"><span data-stu-id="4083f-153">There are a few limitations to be aware of when using raw SQL queries:</span></span>

- <span data-ttu-id="4083f-154">La consulta SQL debe devolver datos para todas las propiedades del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="4083f-154">The SQL query must return data for all properties of the entity type.</span></span>
- <span data-ttu-id="4083f-155">Los nombres de las columnas del conjunto de resultados deben coincidir con los nombres de las columnas a los que se asignan las propiedades.</span><span class="sxs-lookup"><span data-stu-id="4083f-155">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="4083f-156">Tenga en cuenta que este comportamiento es diferente al de EF6.</span><span class="sxs-lookup"><span data-stu-id="4083f-156">Note this behavior is different from EF6.</span></span> <span data-ttu-id="4083f-157">En EF6 se omitía la asignación de propiedades y columnas para las consultas SQL sin formato, y los nombres de las columnas del conjunto de resultados tenían que coincidir con los nombres de las propiedades.</span><span class="sxs-lookup"><span data-stu-id="4083f-157">EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.</span></span>
- <span data-ttu-id="4083f-158">La consulta SQL no puede contener datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="4083f-158">The SQL query can't contain related data.</span></span> <span data-ttu-id="4083f-159">Sin embargo, en muchos casos puede redactar sobre la consulta si usa el operador `Include` para devolver datos relacionados (consulte [Inclusión de datos relacionados](#including-related-data)).</span><span class="sxs-lookup"><span data-stu-id="4083f-159">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="4083f-160">Versiones anteriores</span><span class="sxs-lookup"><span data-stu-id="4083f-160">Previous versions</span></span>

<span data-ttu-id="4083f-161">EF Core 2.2 y las versiones anteriores tenían dos sobrecargas de método denominadas `FromSql`, que se comportaban de la misma manera que las sobrecargas `FromSqlRaw` y `FromSqlInterpolated` más recientes.</span><span class="sxs-lookup"><span data-stu-id="4083f-161">EF Core version 2.2 and earlier had two overloads of method named `FromSql`, which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="4083f-162">Resultaba sencillo llamar de forma accidental al método de cadena sin formato cuando la intención era llamar al método de cadena interpolada y viceversa.</span><span class="sxs-lookup"><span data-stu-id="4083f-162">It was easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="4083f-163">La llamada accidental a la sobrecarga incorrecta podría generar como resultado consultas que no se parametrizaban cuando debían.</span><span class="sxs-lookup"><span data-stu-id="4083f-163">Calling wrong overload accidentally could result in queries not being parameterized when they should have been.</span></span>
