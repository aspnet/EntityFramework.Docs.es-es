---
title: 'Tipos de entidad: EF Core'
description: Cómo configurar y asignar tipos de entidad mediante Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 6c8029b64210dd9bc69fd53d9a3b4aa06bf519e0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023996"
---
# <a name="entity-types"></a><span data-ttu-id="52b43-103">Tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="52b43-103">Entity Types</span></span>

<span data-ttu-id="52b43-104">La inclusión de un DbSet de un tipo en el contexto significa que se incluye en el modelo de EF Core. normalmente hacemos referencia a este tipo como una *entidad*.</span><span class="sxs-lookup"><span data-stu-id="52b43-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="52b43-105">EF Core puede leer y escribir instancias de entidad desde y hacia la base de datos, y si está utilizando una base de datos relacional, EF Core puede crear tablas para las entidades a través de migraciones.</span><span class="sxs-lookup"><span data-stu-id="52b43-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="52b43-106">Incluir tipos en el modelo</span><span class="sxs-lookup"><span data-stu-id="52b43-106">Including types in the model</span></span>

<span data-ttu-id="52b43-107">Por Convención, los tipos que se exponen en las propiedades de DbSet en el contexto se incluyen en el modelo como entidades.</span><span class="sxs-lookup"><span data-stu-id="52b43-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="52b43-108">También se incluyen los tipos de entidad que se especifican en el `OnModelCreating` método, al igual que los tipos que se encuentran al explorar de forma recursiva las propiedades de navegación de otros tipos de entidades detectadas.</span><span class="sxs-lookup"><span data-stu-id="52b43-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="52b43-109">En el ejemplo de código siguiente, se incluyen todos los tipos:</span><span class="sxs-lookup"><span data-stu-id="52b43-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="52b43-110">`Blog` se incluye porque se expone en una propiedad DbSet en el contexto.</span><span class="sxs-lookup"><span data-stu-id="52b43-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="52b43-111">`Post` se incluye porque se detecta a través de la `Blog.Posts` propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="52b43-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="52b43-112">`AuditEntry` porque se especifica en `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="52b43-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="52b43-113">Excluir tipos del modelo</span><span class="sxs-lookup"><span data-stu-id="52b43-113">Excluding types from the model</span></span>

<span data-ttu-id="52b43-114">Si no desea incluir un tipo en el modelo, puede excluirlo:</span><span class="sxs-lookup"><span data-stu-id="52b43-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="52b43-115">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="52b43-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="52b43-116">API fluida</span><span class="sxs-lookup"><span data-stu-id="52b43-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="52b43-117">Exclusión de las migraciones</span><span class="sxs-lookup"><span data-stu-id="52b43-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="52b43-118">La capacidad de excluir las tablas de las migraciones se presentó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="52b43-118">The ability to exclude tables from migrations was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="52b43-119">A veces resulta útil tener el mismo tipo de entidad asignado en varios `DbContext` tipos.</span><span class="sxs-lookup"><span data-stu-id="52b43-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="52b43-120">Esto es especialmente cierto cuando se usan [contextos delimitados](https://www.martinfowler.com/bliki/BoundedContext.html), para los que es común tener un `DbContext` tipo diferente para cada contexto enlazado.</span><span class="sxs-lookup"><span data-stu-id="52b43-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="52b43-121">Con esta configuración, las migraciones no crearán la `AspNetUsers` tabla, pero `IdentityUser` se incluirán en el modelo y se pueden usar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="52b43-121">With this configuration migrations will not create the `AspNetUsers` table, but `IdentityUser` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="52b43-122">Si necesita empezar a administrar la tabla con las migraciones de nuevo, se debe crear una nueva migración en la que `AspNetUsers` no se excluya.</span><span class="sxs-lookup"><span data-stu-id="52b43-122">If you need to start managing the table using migrations again then a new migration should be created where `AspNetUsers` is not excluded.</span></span> <span data-ttu-id="52b43-123">La siguiente migración contendrá los cambios realizados en la tabla.</span><span class="sxs-lookup"><span data-stu-id="52b43-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="52b43-124">Nombre de la tabla</span><span class="sxs-lookup"><span data-stu-id="52b43-124">Table name</span></span>

<span data-ttu-id="52b43-125">Por Convención, cada tipo de entidad se configurará para asignarse a una tabla de base de datos con el mismo nombre que la propiedad DbSet que expone la entidad.</span><span class="sxs-lookup"><span data-stu-id="52b43-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="52b43-126">Si no existe ningún DbSet para la entidad especificada, se utiliza el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="52b43-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="52b43-127">Puede configurar manualmente el nombre de la tabla:</span><span class="sxs-lookup"><span data-stu-id="52b43-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="52b43-128">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="52b43-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="52b43-129">API fluida</span><span class="sxs-lookup"><span data-stu-id="52b43-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="52b43-130">Esquema de tabla</span><span class="sxs-lookup"><span data-stu-id="52b43-130">Table schema</span></span>

<span data-ttu-id="52b43-131">Al utilizar una base de datos relacional, las tablas se crean por Convención en el esquema predeterminado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="52b43-131">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="52b43-132">Por ejemplo, Microsoft SQL Server usará el `dbo` esquema (SQLite no admite esquemas).</span><span class="sxs-lookup"><span data-stu-id="52b43-132">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="52b43-133">Puede configurar las tablas que se van a crear en un esquema específico de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="52b43-133">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="52b43-134">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="52b43-134">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="52b43-135">API fluida</span><span class="sxs-lookup"><span data-stu-id="52b43-135">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="52b43-136">En lugar de especificar el esquema de cada tabla, también puede definir el esquema predeterminado en el nivel de modelo con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="52b43-136">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="52b43-137">Tenga en cuenta que al establecer el esquema predeterminado también se verán afectados otros objetos de base de datos, como las secuencias.</span><span class="sxs-lookup"><span data-stu-id="52b43-137">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="52b43-138">Vista de la asignación</span><span class="sxs-lookup"><span data-stu-id="52b43-138">View mapping</span></span>

<span data-ttu-id="52b43-139">Los tipos de entidad pueden asignarse a vistas de base de datos mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="52b43-139">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="52b43-140">EF supondrá que la vista a la que se hace referencia ya existe en la base de datos, no la creará automáticamente en una migración.</span><span class="sxs-lookup"><span data-stu-id="52b43-140">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="52b43-141">La asignación a una vista quitará la asignación de tabla predeterminada, pero a partir de EF 5,0 el tipo de entidad también se puede asignar explícitamente a una tabla.</span><span class="sxs-lookup"><span data-stu-id="52b43-141">Mapping to a view will remove the default table mapping, but starting with EF 5.0 the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="52b43-142">En este caso, la asignación de consultas se usará para las consultas y la asignación de tabla se usará para las actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="52b43-142">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>

> [!TIP]
> <span data-ttu-id="52b43-143">Para probar los tipos de entidad asignados a las vistas mediante el proveedor en memoria, asígnelo a una consulta a través de `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="52b43-143">To test entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="52b43-144">Vea un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) mediante esta técnica para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="52b43-144">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>

## <a name="table-valued-function-mapping"></a><span data-ttu-id="52b43-145">Asignación de funciones con valores de tabla</span><span class="sxs-lookup"><span data-stu-id="52b43-145">Table-valued function mapping</span></span>

<span data-ttu-id="52b43-146">Es posible asignar un tipo de entidad a una función con valores de tabla (TVF) en lugar de a una tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="52b43-146">It's possible to map an entity type to a table-valued function (TVF) instead of a table in the database.</span></span> <span data-ttu-id="52b43-147">Para ilustrar esto, vamos a definir otra entidad que represente el blog con varias publicaciones.</span><span class="sxs-lookup"><span data-stu-id="52b43-147">To illustrate this, let's define another entity that represents blog with multiple posts.</span></span> <span data-ttu-id="52b43-148">En el ejemplo, la entidad es una [entrada sin llave](xref:core/modeling/keyless-entity-types), pero no es necesario que sea.</span><span class="sxs-lookup"><span data-stu-id="52b43-148">In the example, the entity is [keyless](xref:core/modeling/keyless-entity-types), but it doesn't have to be.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

<span data-ttu-id="52b43-149">A continuación, cree la siguiente función con valores de tabla en la base de datos, que solo devuelve blogs con varias publicaciones, así como el número de entradas asociadas a cada uno de estos blogs:</span><span class="sxs-lookup"><span data-stu-id="52b43-149">Next, create the following table-valued function in the database, which returns only blogs with multiple posts as well as the number of posts associated with each of these blogs:</span></span>

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

<span data-ttu-id="52b43-150">Ahora, la entidad `BlogWithMultiplePost` se puede asignar a esta función de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="52b43-150">Now, the entity `BlogWithMultiplePost` can be mapped to this function in a following way:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> <span data-ttu-id="52b43-151">Para asignar una entidad a una función con valores de tabla, la función no debe tener parámetros.</span><span class="sxs-lookup"><span data-stu-id="52b43-151">In order to map an entity to a table-valued function the function must be parameterless.</span></span>

<span data-ttu-id="52b43-152">Convencionalmente, las propiedades de la entidad se asignarán a las columnas coincidentes devueltas por la función TVF.</span><span class="sxs-lookup"><span data-stu-id="52b43-152">Conventionally the entity properties will be mapped to matching columns returned by the TVF.</span></span> <span data-ttu-id="52b43-153">Si las columnas devueltas por TVF tienen un nombre diferente que la propiedad de entidad, se puede configurar mediante el `HasColumnName` método, al igual que cuando se asigna a una tabla normal.</span><span class="sxs-lookup"><span data-stu-id="52b43-153">If the columns returned by TVF has different name than entity property then it can be configured using `HasColumnName` method, just like when mapping to a regular table.</span></span>

<span data-ttu-id="52b43-154">Cuando el tipo de entidad se asigna a una función con valores de tabla, la consulta:</span><span class="sxs-lookup"><span data-stu-id="52b43-154">When the entity type is mapped to a table-valued function, the query:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

<span data-ttu-id="52b43-155">Produce el siguiente SQL:</span><span class="sxs-lookup"><span data-stu-id="52b43-155">Produces the following SQL:</span></span>

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a><span data-ttu-id="52b43-156">Comentarios de tabla</span><span class="sxs-lookup"><span data-stu-id="52b43-156">Table comments</span></span>

<span data-ttu-id="52b43-157">Puede establecer un Comentario de texto arbitrario que se establece en la tabla de base de datos, lo que le permite documentar el esquema en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="52b43-157">You can set an arbitrary text comment that gets set on the database table, allowing you to document your schema in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="52b43-158">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="52b43-158">Data Annotations</span></span>](#tab/data-annotations)

> [!NOTE]
> <span data-ttu-id="52b43-159">La configuración de comentarios a través de anotaciones de datos se presentó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="52b43-159">Setting comments via data annotations was introduced in EF Core 5.0.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="52b43-160">API fluida</span><span class="sxs-lookup"><span data-stu-id="52b43-160">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

***
