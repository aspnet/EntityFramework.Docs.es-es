---
title: 'Tipos de entidad sin llave: EF Core'
description: Configuración de tipos de entidad sin entrada mediante Entity Framework Core
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: d1a60e0504b22623b97c1a4963d2e3f70faa365c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429512"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="77d45-103">Tipos de entidad sin llave</span><span class="sxs-lookup"><span data-stu-id="77d45-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="77d45-104">Esta característica se ha agregado bajo el nombre de los tipos de consulta.</span><span class="sxs-lookup"><span data-stu-id="77d45-104">This feature was added under the name of query types.</span></span> <span data-ttu-id="77d45-105">En EF Core 3,0 se cambió el nombre del concepto a tipos de entidad sin entrada.</span><span class="sxs-lookup"><span data-stu-id="77d45-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span> <span data-ttu-id="77d45-106">La `[Keyless]` anotación de datos estuvo disponible en EFCore 5,0.</span><span class="sxs-lookup"><span data-stu-id="77d45-106">The `[Keyless]` Data Annotation became available in EFCore 5.0.</span></span>

<span data-ttu-id="77d45-107">Además de los tipos de entidad normales, un modelo de EF Core puede contener _tipos de entidad_ sin clave, que se pueden usar para realizar consultas de base de datos con datos que no contengan valores de clave.</span><span class="sxs-lookup"><span data-stu-id="77d45-107">In addition to regular entity types, an EF Core model can contain _keyless entity types_ , which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="77d45-108">Definición de tipos de entidad sin entrada</span><span class="sxs-lookup"><span data-stu-id="77d45-108">Defining Keyless entity types</span></span>

<span data-ttu-id="77d45-109">Los tipos de entidad sin llave pueden definirse mediante la anotación de datos o la API fluida:</span><span class="sxs-lookup"><span data-stu-id="77d45-109">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="77d45-110">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="77d45-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="77d45-111">API fluida</span><span class="sxs-lookup"><span data-stu-id="77d45-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="77d45-112">Características de tipos de entidad sin llave</span><span class="sxs-lookup"><span data-stu-id="77d45-112">Keyless entity types characteristics</span></span>

<span data-ttu-id="77d45-113">Los tipos de entidad sin llave admiten muchas de las mismas capacidades de asignación que los tipos de entidad normales, como las propiedades de navegación y asignación de herencia.</span><span class="sxs-lookup"><span data-stu-id="77d45-113">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="77d45-114">En almacenes relacionales, pueden configurar los objetos y las columnas de la base de datos de destino mediante métodos de API fluida o anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="77d45-114">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="77d45-115">Sin embargo, son diferentes de los tipos de entidad normales en que:</span><span class="sxs-lookup"><span data-stu-id="77d45-115">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="77d45-116">No se puede definir una clave.</span><span class="sxs-lookup"><span data-stu-id="77d45-116">Cannot have a key defined.</span></span>
- <span data-ttu-id="77d45-117">Nunca se realiza un seguimiento de los cambios en _DbContext_ y, por lo tanto, nunca se insertan, actualizan o eliminan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77d45-117">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="77d45-118">Nunca se detectan por Convención.</span><span class="sxs-lookup"><span data-stu-id="77d45-118">Are never discovered by convention.</span></span>
- <span data-ttu-id="77d45-119">Solo admite un subconjunto de capacidades de asignación de navegación, en concreto:</span><span class="sxs-lookup"><span data-stu-id="77d45-119">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="77d45-120">Nunca pueden actuar como el extremo principal de una relación.</span><span class="sxs-lookup"><span data-stu-id="77d45-120">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="77d45-121">Puede que no tengan navegaciones a entidades propiedad</span><span class="sxs-lookup"><span data-stu-id="77d45-121">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="77d45-122">Solo pueden contener propiedades de navegación de referencia que apunten a entidades normales.</span><span class="sxs-lookup"><span data-stu-id="77d45-122">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="77d45-123">Las entidades no pueden contener propiedades de navegación a tipos de entidad sin llave.</span><span class="sxs-lookup"><span data-stu-id="77d45-123">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="77d45-124">Debe configurarse con una `[Keyless]` anotación de datos o una `.HasNoKey()` llamada al método.</span><span class="sxs-lookup"><span data-stu-id="77d45-124">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="77d45-125">Se puede asignar a una _consulta de definición_.</span><span class="sxs-lookup"><span data-stu-id="77d45-125">May be mapped to a _defining query_.</span></span> <span data-ttu-id="77d45-126">Una consulta de definición es una consulta declarada en el modelo que actúa como origen de datos para un tipo de entidad sin entrada.</span><span class="sxs-lookup"><span data-stu-id="77d45-126">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="77d45-127">Escenarios de uso</span><span class="sxs-lookup"><span data-stu-id="77d45-127">Usage scenarios</span></span>

<span data-ttu-id="77d45-128">Algunos de los escenarios de uso principales de los tipos de entidad sin llave son:</span><span class="sxs-lookup"><span data-stu-id="77d45-128">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="77d45-129">Actúa como el tipo de valor devuelto para [las consultas SQL sin procesar](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="77d45-129">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="77d45-130">Asignación a vistas de base de datos que no contienen una clave principal.</span><span class="sxs-lookup"><span data-stu-id="77d45-130">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="77d45-131">Asignación a tablas que no tienen una clave principal definida.</span><span class="sxs-lookup"><span data-stu-id="77d45-131">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="77d45-132">Asignación a las consultas definidas en el modelo.</span><span class="sxs-lookup"><span data-stu-id="77d45-132">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="77d45-133">Asignar a objetos de base de datos</span><span class="sxs-lookup"><span data-stu-id="77d45-133">Mapping to database objects</span></span>

<span data-ttu-id="77d45-134">La asignación de un tipo de entidad sin llave a un objeto de base de datos se consigue mediante el `ToTable` o la `ToView` API fluida.</span><span class="sxs-lookup"><span data-stu-id="77d45-134">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="77d45-135">Desde la perspectiva de EF Core, el objeto de base de datos especificado en este método es una _vista_ , lo que significa que se trata como un origen de consulta de solo lectura y no puede ser el destino de las operaciones de actualización, inserción o eliminación.</span><span class="sxs-lookup"><span data-stu-id="77d45-135">From the perspective of EF Core, the database object specified in this method is a _view_ , meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="77d45-136">Sin embargo, esto no significa que el objeto de base de datos sea realmente necesario para ser una vista de base de datos.</span><span class="sxs-lookup"><span data-stu-id="77d45-136">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="77d45-137">Como alternativa, puede tratarse de una tabla de base de datos que se tratará como de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="77d45-137">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="77d45-138">Por el contrario, en el caso de los tipos de entidad normales, EF Core supone que un objeto de base de datos especificado en el `ToTable` método se puede tratar como una _tabla_ , lo que significa que se puede usar como origen de la consulta, pero también como destino de las operaciones de actualización, eliminación e inserción.</span><span class="sxs-lookup"><span data-stu-id="77d45-138">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_ , meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="77d45-139">De hecho, puede especificar el nombre de una vista de base de datos en `ToTable` y todo debería funcionar bien siempre que la vista esté configurada para ser actualizable en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77d45-139">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="77d45-140">`ToView` supone que el objeto ya existe en la base de datos y que no lo crearán las migraciones.</span><span class="sxs-lookup"><span data-stu-id="77d45-140">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="77d45-141">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="77d45-141">Example</span></span>

<span data-ttu-id="77d45-142">En el ejemplo siguiente se muestra cómo utilizar los tipos de entidad sin entrada para consultar una vista de base de datos.</span><span class="sxs-lookup"><span data-stu-id="77d45-142">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="77d45-143">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="77d45-143">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="77d45-144">En primer lugar, definimos un blog y un modelo post sencillos:</span><span class="sxs-lookup"><span data-stu-id="77d45-144">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="77d45-145">A continuación, se define una vista de base de datos simple que nos permitirá consultar el número de publicaciones asociadas a cada blog:</span><span class="sxs-lookup"><span data-stu-id="77d45-145">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="77d45-146">A continuación, se define una clase para que contenga el resultado de la vista de base de datos:</span><span class="sxs-lookup"><span data-stu-id="77d45-146">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="77d45-147">A continuación, configuraremos el tipo de entidad sin llave en _OnModelCreating_ con la `HasNoKey` API.</span><span class="sxs-lookup"><span data-stu-id="77d45-147">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="77d45-148">Usamos la API de configuración fluida para configurar la asignación para el tipo de entidad sin llave:</span><span class="sxs-lookup"><span data-stu-id="77d45-148">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="77d45-149">A continuación, configuramos el `DbContext` para incluir `DbSet<T>` :</span><span class="sxs-lookup"><span data-stu-id="77d45-149">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="77d45-150">Por último, podemos consultar la vista de base de datos de la manera estándar:</span><span class="sxs-lookup"><span data-stu-id="77d45-150">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="77d45-151">Nota también hemos definido una propiedad de consulta de nivel de contexto (DbSet) para que actúe como raíz para las consultas en este tipo.</span><span class="sxs-lookup"><span data-stu-id="77d45-151">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>

> [!TIP]
> <span data-ttu-id="77d45-152">Para probar los tipos de entidad sin entrada asignados a las vistas mediante el proveedor en memoria, asígnelo a una consulta a través de `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="77d45-152">To test keyless entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="77d45-153">Vea un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) mediante esta técnica para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="77d45-153">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>
