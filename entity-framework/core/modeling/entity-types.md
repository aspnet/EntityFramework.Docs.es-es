---
title: 'Tipos de entidad: EF Core'
description: Cómo configurar y asignar tipos de entidad mediante Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: ca8cb8560afe374218e763bc0476839187a40ece
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635775"
---
# <a name="entity-types"></a>Tipos de entidad

La inclusión de un DbSet de un tipo en el contexto significa que se incluye en el modelo de EF Core. normalmente hacemos referencia a este tipo como una *entidad*. EF Core puede leer y escribir instancias de entidad desde y hacia la base de datos, y si está utilizando una base de datos relacional, EF Core puede crear tablas para las entidades a través de migraciones.

## <a name="including-types-in-the-model"></a>Incluir tipos en el modelo

Por Convención, los tipos que se exponen en las propiedades de DbSet en el contexto se incluyen en el modelo como entidades. También se incluyen los tipos de entidad que se especifican en el `OnModelCreating` método, al igual que los tipos que se encuentran al explorar de forma recursiva las propiedades de navegación de otros tipos de entidades detectadas.

En el ejemplo de código siguiente, se incluyen todos los tipos:

* `Blog` se incluye porque se expone en una propiedad DbSet en el contexto.
* `Post` se incluye porque se detecta a través de la `Blog.Posts` propiedad de navegación.
* `AuditEntry` porque se especifica en `OnModelCreating` .

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>Excluir tipos del modelo

Si no desea incluir un tipo en el modelo, puede excluirlo:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a>Exclusión de las migraciones

> [!NOTE]
> La capacidad de excluir las tablas de las migraciones se presentó en EF Core 5,0.

A veces resulta útil tener el mismo tipo de entidad asignado en varios `DbContext` tipos. Esto es especialmente cierto cuando se usan [contextos delimitados](https://www.martinfowler.com/bliki/BoundedContext.html), para los que es común tener un `DbContext` tipo diferente para cada contexto enlazado.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

Con esta configuración, las migraciones no crearán la `AspNetUsers` tabla, pero `IdentityUser` se incluirán en el modelo y se pueden usar con normalidad.

Si necesita empezar a administrar la tabla con las migraciones de nuevo, se debe crear una nueva migración en la que `AspNetUsers` no se excluya. La siguiente migración contendrá los cambios realizados en la tabla.

## <a name="table-name"></a>Nombre de la tabla

Por Convención, cada tipo de entidad se configurará para asignarse a una tabla de base de datos con el mismo nombre que la propiedad DbSet que expone la entidad. Si no existe ningún DbSet para la entidad especificada, se utiliza el nombre de clase.

Puede configurar manualmente el nombre de la tabla:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

**_

## <a name="table-schema"></a>Esquema de tabla

Al utilizar una base de datos relacional, las tablas se crean por Convención en el esquema predeterminado de la base de datos. Por ejemplo, Microsoft SQL Server usará el `dbo` esquema (SQLite no admite esquemas).

Puede configurar las tablas que se van a crear en un esquema específico de la siguiente manera:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

_*_

En lugar de especificar el esquema de cada tabla, también puede definir el esquema predeterminado en el nivel de modelo con la API fluida:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

Tenga en cuenta que al establecer el esquema predeterminado también se verán afectados otros objetos de base de datos, como las secuencias.

## <a name="view-mapping"></a>Vista de la asignación

Los tipos de entidad pueden asignarse a vistas de base de datos mediante la API fluida.

> [!Note]
> EF supondrá que la vista a la que se hace referencia ya existe en la base de datos, no la creará automáticamente en una migración.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 La asignación a una vista quitará la asignación de tabla predeterminada, pero a partir de EF 5,0 el tipo de entidad también se puede asignar explícitamente a una tabla. En este caso, la asignación de consultas se usará para las consultas y la asignación de tabla se usará para las actualizaciones.

> [!TIP]
> Para probar los tipos de entidad asignados a las vistas mediante el proveedor en memoria, asígnelo a una consulta a través de `ToInMemoryQuery` . Vea un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) mediante esta técnica para obtener más detalles.

## <a name="table-valued-function-mapping"></a>Asignación de funciones con valores de tabla

Es posible asignar un tipo de entidad a una función con valores de tabla (TVF) en lugar de a una tabla de la base de datos. Para ilustrar esto, vamos a definir otra entidad que represente el blog con varias publicaciones. En el ejemplo, la entidad es una [entrada sin llave](xref:core/modeling/keyless-entity-types), pero no es necesario que sea.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

A continuación, cree la siguiente función con valores de tabla en la base de datos, que solo devuelve blogs con varias publicaciones, así como el número de entradas asociadas a cada uno de estos blogs:

```sql
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)
```

Ahora, la entidad `BlogWithMultiplePost` se puede asignar a esta función de la siguiente manera:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> Para asignar una entidad a una función con valores de tabla, la función no debe tener parámetros.

Convencionalmente, las propiedades de la entidad se asignarán a las columnas coincidentes devueltas por la función TVF. Si las columnas devueltas por TVF tienen un nombre diferente que la propiedad de entidad, se puede configurar mediante el `HasColumnName` método, al igual que cuando se asigna a una tabla normal.

Cuando el tipo de entidad se asigna a una función con valores de tabla, la consulta:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

Produce el siguiente SQL:

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a>Comentarios de tabla

Puede establecer un Comentario de texto arbitrario que se establece en la tabla de base de datos, lo que le permite documentar el esquema en la base de datos:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

> [!NOTE]
> La configuración de comentarios a través de anotaciones de datos se presentó en EF Core 5,0.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

_**
