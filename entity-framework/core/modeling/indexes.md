---
title: 'Índices: EF Core'
description: Configurar índices en un modelo de Entity Framework Core
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128542"
---
# <a name="indexes"></a><span data-ttu-id="4064a-103">Índices</span><span class="sxs-lookup"><span data-stu-id="4064a-103">Indexes</span></span>

<span data-ttu-id="4064a-104">Los índices son un concepto común en muchos almacenes de datos.</span><span class="sxs-lookup"><span data-stu-id="4064a-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="4064a-105">Aunque su implementación en el almacén de datos puede variar, se usan para realizar búsquedas basadas en una columna (o conjunto de columnas) más eficaces.</span><span class="sxs-lookup"><span data-stu-id="4064a-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span> <span data-ttu-id="4064a-106">Vea la [sección índices](xref:core/performance/efficient-querying#use-indexes-properly) de la documentación de rendimiento para obtener más información sobre el uso de índices correctos.</span><span class="sxs-lookup"><span data-stu-id="4064a-106">See the [indexes section](xref:core/performance/efficient-querying#use-indexes-properly) in the performance documentation for more information on good index usage.</span></span>

<span data-ttu-id="4064a-107">Puede especificar un índice en una columna de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="4064a-107">You can specify an index over a column as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="4064a-108">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="4064a-108">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> <span data-ttu-id="4064a-109">La configuración de índices a través de anotaciones de datos se ha introducido en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4064a-109">Configuring indexes via Data Annotations has been introduced in EF Core 5.0.</span></span>

## <a name="fluent-api"></a>[<span data-ttu-id="4064a-110">API fluida</span><span class="sxs-lookup"><span data-stu-id="4064a-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> <span data-ttu-id="4064a-111">Por Convención, se crea un índice en cada propiedad (o conjunto de propiedades) que se usa como clave externa.</span><span class="sxs-lookup"><span data-stu-id="4064a-111">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="4064a-112">EF Core solo admite un índice por conjunto de propiedades distinto.</span><span class="sxs-lookup"><span data-stu-id="4064a-112">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="4064a-113">Si configura un índice en un conjunto de propiedades que ya tiene definido un índice, ya sea por Convención o por configuración anterior, cambiará la definición de ese índice.</span><span class="sxs-lookup"><span data-stu-id="4064a-113">If you configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="4064a-114">Esto resulta útil si desea seguir configurando un índice creado por la Convención.</span><span class="sxs-lookup"><span data-stu-id="4064a-114">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="composite-index"></a><span data-ttu-id="4064a-115">Índice compuesto</span><span class="sxs-lookup"><span data-stu-id="4064a-115">Composite index</span></span>

<span data-ttu-id="4064a-116">Un índice también puede abarcar más de una columna:</span><span class="sxs-lookup"><span data-stu-id="4064a-116">An index can also span more than one column:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4064a-117">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="4064a-117">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4064a-118">API fluida</span><span class="sxs-lookup"><span data-stu-id="4064a-118">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

<span data-ttu-id="4064a-119">\*\*_</span><span class="sxs-lookup"><span data-stu-id="4064a-119">\*\*_</span></span>

<span data-ttu-id="4064a-120">Los índices de varias columnas, también conocidos como índices de _composite \*, agilizan las consultas que filtran las columnas del índice, pero también las consultas que solo filtran las *primeras* columnas que se incluyen en el índice.</span><span class="sxs-lookup"><span data-stu-id="4064a-120">Indexes over multiple columns, also known as _composite indexes\*, speed up queries which filter on index's columns, but also queries which only filter on the *first* columns covered by the index.</span></span> <span data-ttu-id="4064a-121">Vea los [documentos de rendimiento](xref:core/performance/efficient-querying#use-indexes-properly) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="4064a-121">See the [performance docs](xref:core/performance/efficient-querying#use-indexes-properly) for more information.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="4064a-122">Unicidad del índice</span><span class="sxs-lookup"><span data-stu-id="4064a-122">Index uniqueness</span></span>

<span data-ttu-id="4064a-123">De forma predeterminada, los índices no son únicos: se permite que varias filas tengan los mismos valores para el conjunto de columnas del índice.</span><span class="sxs-lookup"><span data-stu-id="4064a-123">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="4064a-124">Puede crear un índice único como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="4064a-124">You can make an index unique as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4064a-125">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="4064a-125">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4064a-126">API fluida</span><span class="sxs-lookup"><span data-stu-id="4064a-126">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

<span data-ttu-id="4064a-127">Si se intenta insertar más de una entidad con los mismos valores para el conjunto de columnas del índice, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="4064a-127">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="4064a-128">Nombre del índice</span><span class="sxs-lookup"><span data-stu-id="4064a-128">Index name</span></span>

<span data-ttu-id="4064a-129">Por Convención, los índices creados en una base de datos relacional se denominan `IX_<type name>_<property name>` .</span><span class="sxs-lookup"><span data-stu-id="4064a-129">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="4064a-130">En el caso de los índices compuestos, `<property name>` se convierte en una lista de nombres de propiedad separados por guiones bajos.</span><span class="sxs-lookup"><span data-stu-id="4064a-130">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="4064a-131">Puede establecer el nombre del índice creado en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4064a-131">You can set the name of the index created in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4064a-132">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="4064a-132">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4064a-133">API fluida</span><span class="sxs-lookup"><span data-stu-id="4064a-133">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

## <a name="index-filter"></a><span data-ttu-id="4064a-134">Filtro de índice</span><span class="sxs-lookup"><span data-stu-id="4064a-134">Index filter</span></span>

<span data-ttu-id="4064a-135">Algunas bases de datos relacionales permiten especificar un índice parcial o filtrado.</span><span class="sxs-lookup"><span data-stu-id="4064a-135">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="4064a-136">Esto permite indizar solo un subconjunto de los valores de una columna, reduciendo el tamaño del índice y mejorando el rendimiento y el uso del espacio en disco.</span><span class="sxs-lookup"><span data-stu-id="4064a-136">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="4064a-137">Para obtener más información sobre SQL Server los índices filtrados, [vea la documentación](/sql/relational-databases/indexes/create-filtered-indexes)de.</span><span class="sxs-lookup"><span data-stu-id="4064a-137">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="4064a-138">Puede usar la API fluida para especificar un filtro en un índice, proporcionado como una expresión SQL:</span><span class="sxs-lookup"><span data-stu-id="4064a-138">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="4064a-139">Al usar el SQL Server el proveedor EF agrega un `'IS NOT NULL'` filtro para todas las columnas que aceptan valores NULL que forman parte de un índice único.</span><span class="sxs-lookup"><span data-stu-id="4064a-139">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="4064a-140">Para invalidar esta Convención, puede proporcionar un `null` valor.</span><span class="sxs-lookup"><span data-stu-id="4064a-140">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="4064a-141">Columnas incluidas</span><span class="sxs-lookup"><span data-stu-id="4064a-141">Included columns</span></span>

<span data-ttu-id="4064a-142">Algunas bases de datos relacionales permiten configurar un conjunto de columnas que se incluyen en el índice, pero que no forman parte de su "clave".</span><span class="sxs-lookup"><span data-stu-id="4064a-142">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="4064a-143">Esto puede mejorar significativamente el rendimiento de las consultas cuando todas las columnas de la consulta se incluyen en el índice como columnas de clave o sin clave, ya que no es necesario tener acceso a la tabla en sí.</span><span class="sxs-lookup"><span data-stu-id="4064a-143">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="4064a-144">Para obtener más información sobre SQL Server columnas incluidas, [vea la documentación](/sql/relational-databases/indexes/create-indexes-with-included-columns)de.</span><span class="sxs-lookup"><span data-stu-id="4064a-144">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="4064a-145">En el ejemplo siguiente, la `Url` columna forma parte de la clave de índice, por lo que cualquier filtrado de consultas en esa columna puede utilizar el índice.</span><span class="sxs-lookup"><span data-stu-id="4064a-145">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="4064a-146">Pero además, las consultas que solo tienen acceso a las `Title` `PublishedOn` columnas y no tendrán que acceder a la tabla y se ejecutarán de forma más eficaz:</span><span class="sxs-lookup"><span data-stu-id="4064a-146">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
