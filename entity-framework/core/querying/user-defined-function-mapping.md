---
title: 'Asignación de funciones definidas por el usuario: EF Core'
description: Asignación de funciones definidas por el usuario a funciones de base de datos.
author: maumar
ms.date: 11/23/2020
uid: core/querying/user-defined-function-mapping
ms.openlocfilehash: 3e49ed9c49b38b98430128ffdc7ceef0b844b9df
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129127"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="6ca5f-103">Asignación de funciones definidas por el usuario</span><span class="sxs-lookup"><span data-stu-id="6ca5f-103">User-defined function mapping</span></span>

<span data-ttu-id="6ca5f-104">EF Core permite usar funciones SQL definidas por el usuario en las consultas.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="6ca5f-105">Para ello, las funciones deben asignarse a un método CLR durante la configuración del modelo.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="6ca5f-106">Al traducir la consulta LINQ a SQL, se llama a la función definida por el usuario en lugar de a la función CLR a la que se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="6ca5f-107">Asignación de un método a una función SQL</span><span class="sxs-lookup"><span data-stu-id="6ca5f-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="6ca5f-108">Para mostrar cómo funciona la asignación de funciones definidas por el usuario, vamos a definir las siguientes entidades:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-108">To illustrate how user-defined function mapping work, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="6ca5f-109">Y la siguiente configuración del modelo:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="6ca5f-110">El blog puede tener muchas publicaciones, y cada publicación puede tener muchos comentarios.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="6ca5f-111">Después, creamos la función definida por el usuario `CommentedPostCountForBlog`, que devuelve el recuento de publicaciones con al menos un comentario para un blog determinado, en función del `Id` del blog:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

```sql
CREATE FUNCTION dbo.CommentedPostCountForBlog(@id int)
RETURNS int
AS
BEGIN
    RETURN (SELECT COUNT(*)
        FROM [Posts] AS [p]
        WHERE ([p].[BlogId] = @id) AND ((
            SELECT COUNT(*)
            FROM [Comments] AS [c]
            WHERE [p].[PostId] = [c].[PostId]) > 0));
END
```

<span data-ttu-id="6ca5f-112">Para usar esta función en EF Core, definimos el siguiente método CLR y lo asignamos a la función definida por el usuario:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="6ca5f-113">El cuerpo del método CLR no es importante.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="6ca5f-114">No se invocará el método del lado cliente a menos que EF Core no pueda traducir sus argumentos.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="6ca5f-115">Si los argumentos se pueden traducir, EF Core solo tiene en cuenta la firma del método.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="6ca5f-116">En este ejemplo, el método se define en `DbContext`, pero también se puede definir como un método estático dentro de otras clases.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="6ca5f-117">Esta definición de función ahora se puede asociar a una función definida por el usuario en la configuración del modelo:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="6ca5f-118">De forma predeterminada, EF Core intenta asignar la función CLR a una función con el mismo nombre definida por el usuario.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="6ca5f-119">Si los nombres difieren, se puede usar `HasName` para proporcionar el nombre correcto para la función definida por el usuario a la que queremos asignar el método.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="6ca5f-120">Después, ejecutamos la siguiente consulta:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="6ca5f-121">Se generará esta función SQL:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="6ca5f-122">Asignación de un método a una función SQL personalizada</span><span class="sxs-lookup"><span data-stu-id="6ca5f-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="6ca5f-123">EF Core también permite funciones definidas por el usuario que se convierten en una función SQL específica.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="6ca5f-124">La expresión SQL se proporciona mediante el método `HasTranslation` durante la configuración de la función definida por el usuario.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="6ca5f-125">En el ejemplo siguiente, crearemos una función que calcula la diferencia de porcentaje entre dos enteros.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="6ca5f-126">El método CLR es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="6ca5f-127">La definición de la función es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="6ca5f-128">Una vez definida la función, se puede usar en la consulta.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="6ca5f-129">En lugar de llamar a la función de base de datos, EF Core traducirá el cuerpo del método directamente a SQL basándose en el árbol de expresión SQL construido a partir del método HasTranslation.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="6ca5f-130">La siguiente consulta LINQ:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="6ca5f-131">Produce el siguiente SQL:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a><span data-ttu-id="6ca5f-132">Configuración de la nulabilidad de funciones definidas por el usuario en función de sus argumentos</span><span class="sxs-lookup"><span data-stu-id="6ca5f-132">Configuring nullability of user-defined function based on its arguments</span></span>

<span data-ttu-id="6ca5f-133">Si la función definida por el usuario solo puede devolver `null` cuando uno o varios de sus argumentos son `null`, EF Core proporciona una manera de especificarlo, lo que da lugar a código SQL de mayor rendimiento.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-133">If the user-defined function can only return `null` when one or more of its arguments are `null`, EFCore provides way to specify that, resulting in more performant SQL.</span></span> <span data-ttu-id="6ca5f-134">Para ello, se puede agregar una llamada a `PropagatesNullability()` a la configuración del modelo de parámetros de función pertinente.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-134">It can be done by adding a `PropagatesNullability()` call to the relevant function parameters model configuration.</span></span>

<span data-ttu-id="6ca5f-135">Para ilustrarlo, defina la función de usuario `ConcatStrings`:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-135">To illustrate this, define user function `ConcatStrings`:</span></span>

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

<span data-ttu-id="6ca5f-136">Y dos métodos CLR que se asignan a la función:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-136">and two CLR methods that map to it:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

<span data-ttu-id="6ca5f-137">La configuración del modelo (dentro del método `OnModelCreating`) es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-137">The model configuration (inside `OnModelCreating` method) is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

<span data-ttu-id="6ca5f-138">La primera función se configura de la forma habitual.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-138">The first function is configured in the standard way.</span></span> <span data-ttu-id="6ca5f-139">La segunda función se configura para aprovechar las ventajas de la optimización de la propagación de nulabilidad, lo que proporciona más información sobre cómo se comporta la función en relación a los parámetros NULL.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-139">The second function is configured to take advantage of the nullability propagation optimization, providing more information on how the function behaves around null parameters.</span></span>

<span data-ttu-id="6ca5f-140">Al emitir las consultas siguientes:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-140">When issuing the following queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

<span data-ttu-id="6ca5f-141">Se obtiene este código SQL:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-141">We get this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

<span data-ttu-id="6ca5f-142">La segunda consulta no tiene que volver a evaluar la función para probar su nulabilidad.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-142">The second query doesn't need to re-evaluate the function itself to test its nullability.</span></span>

> [!NOTE]
> <span data-ttu-id="6ca5f-143">Esta optimización únicamente se debe usar si la función solo puede devolver `null` cuando sus parámetros son `null`.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-143">This optimization should only be used if the function can only return `null` when it's parameters are `null`.</span></span>

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="6ca5f-144">Asignación de una función consultable a una función con valores de tabla</span><span class="sxs-lookup"><span data-stu-id="6ca5f-144">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="6ca5f-145">EF Core también admite la asignación a una función con valores de tabla mediante un método CLR definido por el usuario que devuelve un objeto `IQueryable` de tipos de entidad, lo que permite a EF Core asignar funciones con valores de tabla (TVF) con parámetros.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-145">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="6ca5f-146">El proceso es similar a la asignación de una función escalar definida por el usuario a una función SQL: necesitamos una TVF en la base de datos, una función CLR que se usa en las consultas LINQ y una asignación entre las dos.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-146">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="6ca5f-147">Como ejemplo, usaremos una función con valores de tabla que devuelve todas las publicaciones que tienen al menos un comentario que cumple un umbral de "Me gusta" determinado:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-147">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

```sql
CREATE FUNCTION dbo.PostsWithPopularComments(@likeThreshold int)
RETURNS TABLE
AS
RETURN
(
    SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE (
        SELECT COUNT(*)
        FROM [Comments] AS [c]
        WHERE ([p].[PostId] = [c].[PostId]) AND ([c].[Likes] >= @likeThreshold)) > 0
)
```

<span data-ttu-id="6ca5f-148">La firma del método CLR es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-148">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="6ca5f-149">La llamada a `FromExpression` en el cuerpo de la función CLR permite usar la función en lugar de un objeto DbSet normal.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-149">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="6ca5f-150">A continuación se muestra la asignación:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-150">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="6ca5f-151">Hasta que se corrija la [incidencia 23408](https://github.com/dotnet/efcore/issues/23408), la asignación a un objeto `IQueryable` de tipos de entidad reemplaza la asignación predeterminada a una tabla para el objeto DbSet.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-151">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="6ca5f-152">Si es necesario, por ejemplo, cuando la entidad tiene clave, la asignación a la tabla se debe especificar explícitamente mediante el método `ToTable`.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-152">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="6ca5f-153">La función consultable debe estar asignada a una función con valores de tabla y no puede usar el método `HasTranslation`.</span><span class="sxs-lookup"><span data-stu-id="6ca5f-153">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="6ca5f-154">Cuando se asigna la función, se ejecuta la siguiente consulta:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-154">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="6ca5f-155">Esta consulta genera lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6ca5f-155">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
