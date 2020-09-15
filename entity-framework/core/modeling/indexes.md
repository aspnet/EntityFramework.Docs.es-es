---
title: 'Índices: EF Core'
description: Configurar índices en un modelo de Entity Framework Core
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: 3a89f1ae9727dcd8f086e915e666721572636314
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071412"
---
# <a name="indexes"></a><span data-ttu-id="653a3-103">Índices</span><span class="sxs-lookup"><span data-stu-id="653a3-103">Indexes</span></span>

<span data-ttu-id="653a3-104">Los índices son un concepto común en muchos almacenes de datos.</span><span class="sxs-lookup"><span data-stu-id="653a3-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="653a3-105">Aunque su implementación en el almacén de datos puede variar, se usan para realizar búsquedas basadas en una columna (o conjunto de columnas) más eficaces.</span><span class="sxs-lookup"><span data-stu-id="653a3-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="653a3-106">Los índices no se pueden crear con anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="653a3-106">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="653a3-107">Puede usar la API fluida para especificar un índice en una sola columna de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="653a3-107">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="653a3-108">También puede especificar un índice en más de una columna:</span><span class="sxs-lookup"><span data-stu-id="653a3-108">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="653a3-109">Por Convención, se crea un índice en cada propiedad (o conjunto de propiedades) que se usa como clave externa.</span><span class="sxs-lookup"><span data-stu-id="653a3-109">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="653a3-110">EF Core solo admite un índice por conjunto de propiedades distinto.</span><span class="sxs-lookup"><span data-stu-id="653a3-110">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="653a3-111">Si usa la API fluida para configurar un índice en un conjunto de propiedades que ya tiene definido un índice, ya sea por Convención o por configuración anterior, cambiará la definición de ese índice.</span><span class="sxs-lookup"><span data-stu-id="653a3-111">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="653a3-112">Esto resulta útil si desea seguir configurando un índice creado por la Convención.</span><span class="sxs-lookup"><span data-stu-id="653a3-112">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="653a3-113">Unicidad del índice</span><span class="sxs-lookup"><span data-stu-id="653a3-113">Index uniqueness</span></span>

<span data-ttu-id="653a3-114">De forma predeterminada, los índices no son únicos: se permite que varias filas tengan los mismos valores para el conjunto de columnas del índice.</span><span class="sxs-lookup"><span data-stu-id="653a3-114">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="653a3-115">Puede crear un índice único como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="653a3-115">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="653a3-116">Si se intenta insertar más de una entidad con los mismos valores para el conjunto de columnas del índice, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="653a3-116">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="653a3-117">Nombre del índice</span><span class="sxs-lookup"><span data-stu-id="653a3-117">Index name</span></span>

<span data-ttu-id="653a3-118">Por Convención, los índices creados en una base de datos relacional se denominan `IX_<type name>_<property name>` .</span><span class="sxs-lookup"><span data-stu-id="653a3-118">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="653a3-119">En el caso de los índices compuestos, `<property name>` se convierte en una lista de nombres de propiedad separados por guiones bajos.</span><span class="sxs-lookup"><span data-stu-id="653a3-119">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="653a3-120">Puede usar la API fluida para establecer el nombre del índice creado en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="653a3-120">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="653a3-121">Filtro de índice</span><span class="sxs-lookup"><span data-stu-id="653a3-121">Index filter</span></span>

<span data-ttu-id="653a3-122">Algunas bases de datos relacionales permiten especificar un índice parcial o filtrado.</span><span class="sxs-lookup"><span data-stu-id="653a3-122">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="653a3-123">Esto permite indizar solo un subconjunto de los valores de una columna, reduciendo el tamaño del índice y mejorando el rendimiento y el uso del espacio en disco.</span><span class="sxs-lookup"><span data-stu-id="653a3-123">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="653a3-124">Para obtener más información sobre SQL Server los índices filtrados, [vea la documentación](/sql/relational-databases/indexes/create-filtered-indexes)de.</span><span class="sxs-lookup"><span data-stu-id="653a3-124">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="653a3-125">Puede usar la API fluida para especificar un filtro en un índice, proporcionado como una expresión SQL:</span><span class="sxs-lookup"><span data-stu-id="653a3-125">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="653a3-126">Al usar el SQL Server el proveedor EF agrega un `'IS NOT NULL'` filtro para todas las columnas que aceptan valores NULL que forman parte de un índice único.</span><span class="sxs-lookup"><span data-stu-id="653a3-126">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="653a3-127">Para invalidar esta Convención, puede proporcionar un `null` valor.</span><span class="sxs-lookup"><span data-stu-id="653a3-127">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="653a3-128">Columnas incluidas</span><span class="sxs-lookup"><span data-stu-id="653a3-128">Included columns</span></span>

<span data-ttu-id="653a3-129">Algunas bases de datos relacionales permiten configurar un conjunto de columnas que se incluyen en el índice, pero que no forman parte de su "clave".</span><span class="sxs-lookup"><span data-stu-id="653a3-129">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="653a3-130">Esto puede mejorar significativamente el rendimiento de las consultas cuando todas las columnas de la consulta se incluyen en el índice como columnas de clave o sin clave, ya que no es necesario tener acceso a la tabla en sí.</span><span class="sxs-lookup"><span data-stu-id="653a3-130">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="653a3-131">Para obtener más información sobre SQL Server columnas incluidas, [vea la documentación](/sql/relational-databases/indexes/create-indexes-with-included-columns)de.</span><span class="sxs-lookup"><span data-stu-id="653a3-131">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="653a3-132">En el ejemplo siguiente, la `Url` columna forma parte de la clave de índice, por lo que cualquier filtrado de consultas en esa columna puede utilizar el índice.</span><span class="sxs-lookup"><span data-stu-id="653a3-132">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="653a3-133">Pero además, las consultas que solo tienen acceso a las `Title` `PublishedOn` columnas y no tendrán que acceder a la tabla y se ejecutarán de forma más eficaz:</span><span class="sxs-lookup"><span data-stu-id="653a3-133">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
