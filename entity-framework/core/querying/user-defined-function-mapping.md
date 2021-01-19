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
# <a name="user-defined-function-mapping"></a>Asignación de funciones definidas por el usuario

EF Core permite usar funciones SQL definidas por el usuario en las consultas. Para ello, las funciones deben asignarse a un método CLR durante la configuración del modelo. Al traducir la consulta LINQ a SQL, se llama a la función definida por el usuario en lugar de a la función CLR a la que se ha asignado.

## <a name="mapping-a-method-to-a-sql-function"></a>Asignación de un método a una función SQL

Para mostrar cómo funciona la asignación de funciones definidas por el usuario, vamos a definir las siguientes entidades:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

Y la siguiente configuración del modelo:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

El blog puede tener muchas publicaciones, y cada publicación puede tener muchos comentarios.

Después, creamos la función definida por el usuario `CommentedPostCountForBlog`, que devuelve el recuento de publicaciones con al menos un comentario para un blog determinado, en función del `Id` del blog:

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

Para usar esta función en EF Core, definimos el siguiente método CLR y lo asignamos a la función definida por el usuario:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

El cuerpo del método CLR no es importante. No se invocará el método del lado cliente a menos que EF Core no pueda traducir sus argumentos. Si los argumentos se pueden traducir, EF Core solo tiene en cuenta la firma del método.

> [!NOTE]
> En este ejemplo, el método se define en `DbContext`, pero también se puede definir como un método estático dentro de otras clases.

Esta definición de función ahora se puede asociar a una función definida por el usuario en la configuración del modelo:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

De forma predeterminada, EF Core intenta asignar la función CLR a una función con el mismo nombre definida por el usuario. Si los nombres difieren, se puede usar `HasName` para proporcionar el nombre correcto para la función definida por el usuario a la que queremos asignar el método.

Después, ejecutamos la siguiente consulta:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

Se generará esta función SQL:

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a>Asignación de un método a una función SQL personalizada

EF Core también permite funciones definidas por el usuario que se convierten en una función SQL específica. La expresión SQL se proporciona mediante el método `HasTranslation` durante la configuración de la función definida por el usuario.

En el ejemplo siguiente, crearemos una función que calcula la diferencia de porcentaje entre dos enteros.

El método CLR es el siguiente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

La definición de la función es la siguiente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

Una vez definida la función, se puede usar en la consulta. En lugar de llamar a la función de base de datos, EF Core traducirá el cuerpo del método directamente a SQL basándose en el árbol de expresión SQL construido a partir del método HasTranslation. La siguiente consulta LINQ:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

Produce el siguiente SQL:

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a>Configuración de la nulabilidad de funciones definidas por el usuario en función de sus argumentos

Si la función definida por el usuario solo puede devolver `null` cuando uno o varios de sus argumentos son `null`, EF Core proporciona una manera de especificarlo, lo que da lugar a código SQL de mayor rendimiento. Para ello, se puede agregar una llamada a `PropagatesNullability()` a la configuración del modelo de parámetros de función pertinente.

Para ilustrarlo, defina la función de usuario `ConcatStrings`:

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

Y dos métodos CLR que se asignan a la función:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

La configuración del modelo (dentro del método `OnModelCreating`) es la siguiente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

La primera función se configura de la forma habitual. La segunda función se configura para aprovechar las ventajas de la optimización de la propagación de nulabilidad, lo que proporciona más información sobre cómo se comporta la función en relación a los parámetros NULL.

Al emitir las consultas siguientes:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

Se obtiene este código SQL:

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

La segunda consulta no tiene que volver a evaluar la función para probar su nulabilidad.

> [!NOTE]
> Esta optimización únicamente se debe usar si la función solo puede devolver `null` cuando sus parámetros son `null`.

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a>Asignación de una función consultable a una función con valores de tabla

EF Core también admite la asignación a una función con valores de tabla mediante un método CLR definido por el usuario que devuelve un objeto `IQueryable` de tipos de entidad, lo que permite a EF Core asignar funciones con valores de tabla (TVF) con parámetros. El proceso es similar a la asignación de una función escalar definida por el usuario a una función SQL: necesitamos una TVF en la base de datos, una función CLR que se usa en las consultas LINQ y una asignación entre las dos.

Como ejemplo, usaremos una función con valores de tabla que devuelve todas las publicaciones que tienen al menos un comentario que cumple un umbral de "Me gusta" determinado:

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

La firma del método CLR es la siguiente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> La llamada a `FromExpression` en el cuerpo de la función CLR permite usar la función en lugar de un objeto DbSet normal.

A continuación se muestra la asignación:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> Hasta que se corrija la [incidencia 23408](https://github.com/dotnet/efcore/issues/23408), la asignación a un objeto `IQueryable` de tipos de entidad reemplaza la asignación predeterminada a una tabla para el objeto DbSet. Si es necesario, por ejemplo, cuando la entidad tiene clave, la asignación a la tabla se debe especificar explícitamente mediante el método `ToTable`.

> [!NOTE]
> La función consultable debe estar asignada a una función con valores de tabla y no puede usar el método `HasTranslation`.

Cuando se asigna la función, se ejecuta la siguiente consulta:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

Esta consulta genera lo siguiente:

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
