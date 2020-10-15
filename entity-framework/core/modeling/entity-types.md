---
title: 'Tipos de entidad: EF Core'
description: Cómo configurar y asignar tipos de entidad mediante Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: bfefa29c08679a1524c00769b3495d75a301e2d3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062235"
---
# <a name="entity-types"></a><span data-ttu-id="a1edb-103">Tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="a1edb-103">Entity Types</span></span>

<span data-ttu-id="a1edb-104">La inclusión de un DbSet de un tipo en el contexto significa que se incluye en el modelo de EF Core. normalmente hacemos referencia a este tipo como una *entidad*.</span><span class="sxs-lookup"><span data-stu-id="a1edb-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="a1edb-105">EF Core puede leer y escribir instancias de entidad desde y hacia la base de datos, y si está utilizando una base de datos relacional, EF Core puede crear tablas para las entidades a través de migraciones.</span><span class="sxs-lookup"><span data-stu-id="a1edb-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="a1edb-106">Incluir tipos en el modelo</span><span class="sxs-lookup"><span data-stu-id="a1edb-106">Including types in the model</span></span>

<span data-ttu-id="a1edb-107">Por Convención, los tipos que se exponen en las propiedades de DbSet en el contexto se incluyen en el modelo como entidades.</span><span class="sxs-lookup"><span data-stu-id="a1edb-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="a1edb-108">También se incluyen los tipos de entidad que se especifican en el `OnModelCreating` método, al igual que los tipos que se encuentran al explorar de forma recursiva las propiedades de navegación de otros tipos de entidades detectadas.</span><span class="sxs-lookup"><span data-stu-id="a1edb-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="a1edb-109">En el ejemplo de código siguiente, se incluyen todos los tipos:</span><span class="sxs-lookup"><span data-stu-id="a1edb-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="a1edb-110">`Blog` se incluye porque se expone en una propiedad DbSet en el contexto.</span><span class="sxs-lookup"><span data-stu-id="a1edb-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="a1edb-111">`Post` se incluye porque se detecta a través de la `Blog.Posts` propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="a1edb-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="a1edb-112">`AuditEntry` porque se especifica en `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="a1edb-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="a1edb-113">Excluir tipos del modelo</span><span class="sxs-lookup"><span data-stu-id="a1edb-113">Excluding types from the model</span></span>

<span data-ttu-id="a1edb-114">Si no desea incluir un tipo en el modelo, puede excluirlo:</span><span class="sxs-lookup"><span data-stu-id="a1edb-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a1edb-115">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="a1edb-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="a1edb-116">API fluida</span><span class="sxs-lookup"><span data-stu-id="a1edb-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="a1edb-117">Exclusión de las migraciones</span><span class="sxs-lookup"><span data-stu-id="a1edb-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="a1edb-118">La capacidad de excluir las tablas de las migraciones se agregó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="a1edb-118">The ability to exclude tables from migrations was added in EF Core 5.0.</span></span>

<span data-ttu-id="a1edb-119">A veces resulta útil tener el mismo tipo de entidad asignado en varios `DbContext` tipos.</span><span class="sxs-lookup"><span data-stu-id="a1edb-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="a1edb-120">Esto es especialmente cierto cuando se usan [contextos delimitados](https://www.martinfowler.com/bliki/BoundedContext.html), para los que es común tener un `DbContext` tipo diferente para cada contexto enlazado.</span><span class="sxs-lookup"><span data-stu-id="a1edb-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="a1edb-121">Con esta configuración, las migraciones no crearán la `blogs` tabla, pero `Blog` se incluirán en el modelo y se pueden usar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="a1edb-121">With this configuration migrations will not create the `blogs` table, but `Blog` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="a1edb-122">Si necesita empezar a administrar la tabla con las migraciones de nuevo, se debe crear una nueva migración en la que `blogs` no se excluya.</span><span class="sxs-lookup"><span data-stu-id="a1edb-122">If you need to start managing the table using migrations again then a new migration should be created where `blogs` is not excluded.</span></span> <span data-ttu-id="a1edb-123">La siguiente migración contendrá los cambios realizados en la tabla.</span><span class="sxs-lookup"><span data-stu-id="a1edb-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="a1edb-124">Nombre de la tabla</span><span class="sxs-lookup"><span data-stu-id="a1edb-124">Table name</span></span>

<span data-ttu-id="a1edb-125">Por Convención, cada tipo de entidad se configurará para asignarse a una tabla de base de datos con el mismo nombre que la propiedad DbSet que expone la entidad.</span><span class="sxs-lookup"><span data-stu-id="a1edb-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="a1edb-126">Si no existe ningún DbSet para la entidad especificada, se utiliza el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="a1edb-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="a1edb-127">Puede configurar manualmente el nombre de la tabla:</span><span class="sxs-lookup"><span data-stu-id="a1edb-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a1edb-128">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="a1edb-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="a1edb-129">API fluida</span><span class="sxs-lookup"><span data-stu-id="a1edb-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="a1edb-130">Esquema de tabla</span><span class="sxs-lookup"><span data-stu-id="a1edb-130">Table schema</span></span>

<span data-ttu-id="a1edb-131">Al utilizar una base de datos relacional, las tablas se crean por Convención en el esquema predeterminado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a1edb-131">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="a1edb-132">Por ejemplo, Microsoft SQL Server usará el `dbo` esquema (SQLite no admite esquemas).</span><span class="sxs-lookup"><span data-stu-id="a1edb-132">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="a1edb-133">Puede configurar las tablas que se van a crear en un esquema específico de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="a1edb-133">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a1edb-134">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="a1edb-134">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="a1edb-135">API fluida</span><span class="sxs-lookup"><span data-stu-id="a1edb-135">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="a1edb-136">En lugar de especificar el esquema de cada tabla, también puede definir el esquema predeterminado en el nivel de modelo con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="a1edb-136">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="a1edb-137">Tenga en cuenta que al establecer el esquema predeterminado también se verán afectados otros objetos de base de datos, como las secuencias.</span><span class="sxs-lookup"><span data-stu-id="a1edb-137">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="a1edb-138">Vista de la asignación</span><span class="sxs-lookup"><span data-stu-id="a1edb-138">View mapping</span></span>

<span data-ttu-id="a1edb-139">Los tipos de entidad pueden asignarse a vistas de base de datos mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="a1edb-139">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="a1edb-140">EF supondrá que la vista a la que se hace referencia ya existe en la base de datos, no la creará automáticamente en una migración.</span><span class="sxs-lookup"><span data-stu-id="a1edb-140">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="a1edb-141">La asignación a una vista quitará la asignación de tabla predeterminada, pero el tipo de entidad también se puede asignar explícitamente a una tabla.</span><span class="sxs-lookup"><span data-stu-id="a1edb-141">Mapping to a view will remove the default table mapping, but the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="a1edb-142">En este caso, la asignación de consultas se usará para las consultas y la asignación de tabla se usará para las actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="a1edb-142">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>
