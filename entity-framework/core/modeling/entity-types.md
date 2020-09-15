---
title: 'Tipos de entidad: EF Core'
description: Cómo configurar y asignar tipos de entidad mediante Entity Framework Core
author: roji
ms.date: 12/03/2019
uid: core/modeling/entity-types
ms.openlocfilehash: fead7f9e37efb7f674f429acbfd16c2ca78480d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071516"
---
# <a name="entity-types"></a><span data-ttu-id="66cd8-103">Tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="66cd8-103">Entity Types</span></span>

<span data-ttu-id="66cd8-104">La inclusión de un DbSet de un tipo en el contexto significa que se incluye en el modelo de EF Core. normalmente hacemos referencia a este tipo como una *entidad*.</span><span class="sxs-lookup"><span data-stu-id="66cd8-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="66cd8-105">EF Core puede leer y escribir instancias de entidad desde y hacia la base de datos, y si está utilizando una base de datos relacional, EF Core puede crear tablas para las entidades a través de migraciones.</span><span class="sxs-lookup"><span data-stu-id="66cd8-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="66cd8-106">Incluir tipos en el modelo</span><span class="sxs-lookup"><span data-stu-id="66cd8-106">Including types in the model</span></span>

<span data-ttu-id="66cd8-107">Por Convención, los tipos que se exponen en las propiedades de DbSet en el contexto se incluyen en el modelo como entidades.</span><span class="sxs-lookup"><span data-stu-id="66cd8-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="66cd8-108">También se incluyen los tipos de entidad que se especifican en el `OnModelCreating` método, al igual que los tipos que se encuentran al explorar de forma recursiva las propiedades de navegación de otros tipos de entidades detectadas.</span><span class="sxs-lookup"><span data-stu-id="66cd8-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="66cd8-109">En el ejemplo de código siguiente, se incluyen todos los tipos:</span><span class="sxs-lookup"><span data-stu-id="66cd8-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="66cd8-110">`Blog` se incluye porque se expone en una propiedad DbSet en el contexto.</span><span class="sxs-lookup"><span data-stu-id="66cd8-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="66cd8-111">`Post` se incluye porque se detecta a través de la `Blog.Posts` propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="66cd8-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="66cd8-112">`AuditEntry` porque se especifica en `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="66cd8-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="66cd8-113">Excluir tipos del modelo</span><span class="sxs-lookup"><span data-stu-id="66cd8-113">Excluding types from the model</span></span>

<span data-ttu-id="66cd8-114">Si no desea incluir un tipo en el modelo, puede excluirlo:</span><span class="sxs-lookup"><span data-stu-id="66cd8-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="66cd8-115">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="66cd8-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="66cd8-116">API fluida</span><span class="sxs-lookup"><span data-stu-id="66cd8-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="66cd8-117">Nombre de la tabla</span><span class="sxs-lookup"><span data-stu-id="66cd8-117">Table name</span></span>

<span data-ttu-id="66cd8-118">Por Convención, cada tipo de entidad se configurará para asignarse a una tabla de base de datos con el mismo nombre que la propiedad DbSet que expone la entidad.</span><span class="sxs-lookup"><span data-stu-id="66cd8-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="66cd8-119">Si no existe ningún DbSet para la entidad especificada, se utiliza el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="66cd8-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="66cd8-120">Puede configurar manualmente el nombre de la tabla:</span><span class="sxs-lookup"><span data-stu-id="66cd8-120">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="66cd8-121">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="66cd8-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="66cd8-122">API fluida</span><span class="sxs-lookup"><span data-stu-id="66cd8-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="66cd8-123">Esquema de tabla</span><span class="sxs-lookup"><span data-stu-id="66cd8-123">Table schema</span></span>

<span data-ttu-id="66cd8-124">Al utilizar una base de datos relacional, las tablas se crean por Convención en el esquema predeterminado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="66cd8-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="66cd8-125">Por ejemplo, Microsoft SQL Server usará el `dbo` esquema (SQLite no admite esquemas).</span><span class="sxs-lookup"><span data-stu-id="66cd8-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="66cd8-126">Puede configurar las tablas que se van a crear en un esquema específico de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="66cd8-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="66cd8-127">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="66cd8-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="66cd8-128">API fluida</span><span class="sxs-lookup"><span data-stu-id="66cd8-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="66cd8-129">En lugar de especificar el esquema de cada tabla, también puede definir el esquema predeterminado en el nivel de modelo con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="66cd8-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="66cd8-130">Tenga en cuenta que al establecer el esquema predeterminado también se verán afectados otros objetos de base de datos, como las secuencias.</span><span class="sxs-lookup"><span data-stu-id="66cd8-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
