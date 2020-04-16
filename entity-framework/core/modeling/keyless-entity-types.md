---
title: Tipos de entidad sin clave - EF Core
description: Cómo configurar tipos de entidad sin clave mediante Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434219"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="e497e-103">Tipos de entidad sin llave</span><span class="sxs-lookup"><span data-stu-id="e497e-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="e497e-104">Esta característica se agregó en EF Core 2.1 bajo el nombre de tipos de consulta.</span><span class="sxs-lookup"><span data-stu-id="e497e-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="e497e-105">En EF Core 3.0, el concepto se renombró a tipos de entidad sin clave.</span><span class="sxs-lookup"><span data-stu-id="e497e-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="e497e-106">Además de los tipos de entidad normales, un modelo de EF Core puede contener tipos de _entidad sin clave,_ que se pueden usar para realizar consultas de base de datos con datos que no contienen valores de clave.</span><span class="sxs-lookup"><span data-stu-id="e497e-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="e497e-107">Definición de tipos de entidad sin clave</span><span class="sxs-lookup"><span data-stu-id="e497e-107">Defining Keyless entity types</span></span>

<span data-ttu-id="e497e-108">Los tipos de entidad sin clave se pueden definir mediante la anotación de datos o la API fluida:</span><span class="sxs-lookup"><span data-stu-id="e497e-108">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="e497e-109">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="e497e-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="e497e-110">API fluida</span><span class="sxs-lookup"><span data-stu-id="e497e-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="e497e-111">Características de los tipos de entidad sin clave</span><span class="sxs-lookup"><span data-stu-id="e497e-111">Keyless entity types characteristics</span></span>

<span data-ttu-id="e497e-112">Los tipos de entidad sin clave admiten muchas de las mismas capacidades de asignación que los tipos de entidad normales, como las propiedades de navegación y asignación de herencia.</span><span class="sxs-lookup"><span data-stu-id="e497e-112">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="e497e-113">En los almacenes relacionales, pueden configurar los objetos y columnas de la base de datos de destino a través de métodos de API fluidos o anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="e497e-113">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="e497e-114">Sin embargo, son diferentes de los tipos de entidad normales en que:</span><span class="sxs-lookup"><span data-stu-id="e497e-114">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="e497e-115">No se puede tener una clave definida.</span><span class="sxs-lookup"><span data-stu-id="e497e-115">Cannot have a key defined.</span></span>
- <span data-ttu-id="e497e-116">Nunca se realiza un seguimiento de los cambios en _DbContext_ y, por lo tanto, nunca se insertan, actualizan o eliminan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e497e-116">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="e497e-117">Nunca son descubiertos por convención.</span><span class="sxs-lookup"><span data-stu-id="e497e-117">Are never discovered by convention.</span></span>
- <span data-ttu-id="e497e-118">Solo admite un subconjunto de capacidades de asignación de navegación, específicamente:</span><span class="sxs-lookup"><span data-stu-id="e497e-118">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="e497e-119">Puede que nunca actúen como el final principal de una relación.</span><span class="sxs-lookup"><span data-stu-id="e497e-119">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="e497e-120">Es posible que no tengan navegaciones a entidades de propiedad</span><span class="sxs-lookup"><span data-stu-id="e497e-120">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="e497e-121">Solo pueden contener propiedades de navegación de referencia que apunten a entidades regulares.</span><span class="sxs-lookup"><span data-stu-id="e497e-121">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="e497e-122">Las entidades no pueden contener propiedades de navegación a tipos de entidad sin clave.</span><span class="sxs-lookup"><span data-stu-id="e497e-122">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="e497e-123">Es necesario configurarlo `[Keyless]` con una `.HasNoKey()` anotación de datos o una llamada de método.</span><span class="sxs-lookup"><span data-stu-id="e497e-123">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="e497e-124">Se puede asignar a una _consulta de definición._</span><span class="sxs-lookup"><span data-stu-id="e497e-124">May be mapped to a _defining query_.</span></span> <span data-ttu-id="e497e-125">Una consulta de definición es una consulta declarada en el modelo que actúa como origen de datos para un tipo de entidad sin clave.</span><span class="sxs-lookup"><span data-stu-id="e497e-125">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="e497e-126">Escenarios de uso</span><span class="sxs-lookup"><span data-stu-id="e497e-126">Usage scenarios</span></span>

<span data-ttu-id="e497e-127">Algunos de los principales escenarios de uso para tipos de entidad sin clave son:</span><span class="sxs-lookup"><span data-stu-id="e497e-127">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="e497e-128">Servir como el tipo de valor devuelto para [consultas SQL sin procesar](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="e497e-128">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="e497e-129">Asignación a vistas de base de datos que no contienen una clave principal.</span><span class="sxs-lookup"><span data-stu-id="e497e-129">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="e497e-130">Asignación a tablas que no tienen definida una clave principal.</span><span class="sxs-lookup"><span data-stu-id="e497e-130">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="e497e-131">Asignación a consultas definidas en el modelo.</span><span class="sxs-lookup"><span data-stu-id="e497e-131">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="e497e-132">Asignación a objetos de base de datos</span><span class="sxs-lookup"><span data-stu-id="e497e-132">Mapping to database objects</span></span>

<span data-ttu-id="e497e-133">La asignación de un tipo de entidad `ToTable` sin clave a un objeto de base de datos se logra mediante la API o `ToView` fluida.</span><span class="sxs-lookup"><span data-stu-id="e497e-133">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="e497e-134">Desde la perspectiva de EF Core, el objeto de base de datos especificado en este método es una _vista,_ lo que significa que se trata como un origen de consulta de solo lectura y no puede ser el destino de las operaciones de actualización, inserción o eliminación.</span><span class="sxs-lookup"><span data-stu-id="e497e-134">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="e497e-135">Sin embargo, esto no significa que el objeto de base de datos realmente sea necesario para ser una vista de base de datos.</span><span class="sxs-lookup"><span data-stu-id="e497e-135">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="e497e-136">También puede ser una tabla de base de datos que se tratará como de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="e497e-136">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="e497e-137">Por el contrario, para los tipos de entidad normales, `ToTable` EF Core supone que un objeto de base de datos especificado en el método se puede tratar como una _tabla,_ lo que significa que se puede usar como origen de consulta, pero también se destina a las operaciones de actualización, eliminación e inserción.</span><span class="sxs-lookup"><span data-stu-id="e497e-137">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="e497e-138">De hecho, puede especificar el nombre `ToTable` de una vista de base de datos y todo debe funcionar bien siempre que la vista esté configurada para que se pueda actualizar en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e497e-138">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="e497e-139">`ToView`se supone que el objeto ya existe en la base de datos y no se creará mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="e497e-139">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="e497e-140">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e497e-140">Example</span></span>

<span data-ttu-id="e497e-141">En el ejemplo siguiente se muestra cómo utilizar tipos de entidad sin clave para consultar una vista de base de datos.</span><span class="sxs-lookup"><span data-stu-id="e497e-141">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="e497e-142">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="e497e-142">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="e497e-143">En primer lugar, definimos un modelo simple blog y publicación:</span><span class="sxs-lookup"><span data-stu-id="e497e-143">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="e497e-144">A continuación, definimos una vista de base de datos simple que nos permitirá consultar el número de publicaciones asociadas a cada blog:</span><span class="sxs-lookup"><span data-stu-id="e497e-144">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="e497e-145">A continuación, definimos una clase para contener el resultado de la vista de base de datos:</span><span class="sxs-lookup"><span data-stu-id="e497e-145">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="e497e-146">A continuación, configuramos el tipo de entidad `HasNoKey` sin clave en _OnModelCreating_ mediante la API.</span><span class="sxs-lookup"><span data-stu-id="e497e-146">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="e497e-147">Usamos una API de configuración fluida para configurar la asignación para el tipo de entidad sin clave:</span><span class="sxs-lookup"><span data-stu-id="e497e-147">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="e497e-148">A continuación, `DbContext` configuramos `DbSet<T>`el para incluir el :</span><span class="sxs-lookup"><span data-stu-id="e497e-148">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="e497e-149">Por último, podemos consultar la vista de base de datos de la manera estándar:</span><span class="sxs-lookup"><span data-stu-id="e497e-149">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="e497e-150">Tenga en cuenta que también hemos definido una propiedad de consulta de nivel de contexto (DbSet) para que actúe como raíz para las consultas en este tipo.</span><span class="sxs-lookup"><span data-stu-id="e497e-150">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
