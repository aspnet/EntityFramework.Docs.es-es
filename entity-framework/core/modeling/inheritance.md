---
title: 'Herencia: EF Core'
description: Cómo configurar la herencia de tipos de entidad mediante Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 507854e3acc0347adee612e516b3e2e0b10f55cf
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502178"
---
# <a name="inheritance"></a><span data-ttu-id="1873d-103">Herencia</span><span class="sxs-lookup"><span data-stu-id="1873d-103">Inheritance</span></span>

<span data-ttu-id="1873d-104">EF puede asignar una jerarquía de tipos .NET a una base de datos.</span><span class="sxs-lookup"><span data-stu-id="1873d-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="1873d-105">Esto le permite escribir las entidades .NET en el código como de costumbre, con los tipos base y derivados, y hacer que EF cree sin problemas el esquema de base de datos adecuado, las consultas de problemas, etc. Los detalles reales de cómo se asigna una jerarquía de tipos dependen del proveedor; en esta página se describe la compatibilidad de herencia en el contexto de una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="1873d-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="1873d-106">En este momento, EF Core solo admite el patrón de tabla por jerarquía (TPH).</span><span class="sxs-lookup"><span data-stu-id="1873d-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="1873d-107">TPH usa una sola tabla para almacenar los datos de todos los tipos de la jerarquía y se usa una columna de discriminador para identificar qué tipo representa cada fila.</span><span class="sxs-lookup"><span data-stu-id="1873d-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="1873d-108">Los EF Core no admiten la tabla por tipo (TPT) y la tabla por tipo específico (TPC), que son compatibles con EF6.</span><span class="sxs-lookup"><span data-stu-id="1873d-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="1873d-109">TPT es una característica importante planeada para EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="1873d-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="1873d-110">Asignación de jerarquía de tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="1873d-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="1873d-111">Por Convención, EF solo configurará la herencia si dos o más tipos heredados se incluyen explícitamente en el modelo.</span><span class="sxs-lookup"><span data-stu-id="1873d-111">By convention, EF will only set up inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="1873d-112">EF no buscará automáticamente tipos base o derivados que no se incluyan en el modelo de otra forma.</span><span class="sxs-lookup"><span data-stu-id="1873d-112">EF will not automatically scan for base or derived types that are not otherwise included in the model.</span></span>

<span data-ttu-id="1873d-113">Puede incluir tipos en el modelo exponiendo un DbSet para cada tipo en la jerarquía de herencia:</span><span class="sxs-lookup"><span data-stu-id="1873d-113">You can include types in the model by exposing a DbSet for each type in the inheritance hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="1873d-114">Este modelo se asignará al siguiente esquema de la base de datos (tenga en cuenta la columna *discriminada* creada implícitamente, que identifica qué tipo de *blog* se almacena en cada fila):</span><span class="sxs-lookup"><span data-stu-id="1873d-114">This model be mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="1873d-116">Las columnas de la base de datos se convierten en NULL automáticamente según sea necesario al usar la asignación TPH.</span><span class="sxs-lookup"><span data-stu-id="1873d-116">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="1873d-117">Por ejemplo, la columna *RssUrl* admite valores NULL porque las instancias de *blog* normales no tienen esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="1873d-117">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="1873d-118">Si no desea exponer un DbSet para una o varias entidades de la jerarquía, también puede usar la API fluida para asegurarse de que se incluyen en el modelo.</span><span class="sxs-lookup"><span data-stu-id="1873d-118">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="1873d-119">Si no se basa en las convenciones, puede especificar explícitamente el tipo base mediante `HasBaseType`.</span><span class="sxs-lookup"><span data-stu-id="1873d-119">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="1873d-120">También puede usar `.HasBaseType((Type)null)` para quitar un tipo de entidad de la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="1873d-120">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="1873d-121">Configuración de discriminador</span><span class="sxs-lookup"><span data-stu-id="1873d-121">Discriminator configuration</span></span>

<span data-ttu-id="1873d-122">Puede configurar el nombre y el tipo de la columna discriminadora y los valores que se usan para identificar cada tipo en la jerarquía:</span><span class="sxs-lookup"><span data-stu-id="1873d-122">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="1873d-123">En los ejemplos anteriores, EF agregó el discriminador implícitamente como una [propiedad Shadow](xref:core/modeling/shadow-properties) en la entidad base de la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="1873d-123">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="1873d-124">Esta propiedad se puede configurar como cualquier otra:</span><span class="sxs-lookup"><span data-stu-id="1873d-124">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="1873d-125">Por último, el discriminador también se puede asignar a una propiedad .NET normal en la entidad:</span><span class="sxs-lookup"><span data-stu-id="1873d-125">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="1873d-126">Columnas compartidas</span><span class="sxs-lookup"><span data-stu-id="1873d-126">Shared columns</span></span>

<span data-ttu-id="1873d-127">De forma predeterminada, cuando dos tipos de entidad del mismo nivel en la jerarquía tienen una propiedad con el mismo nombre, se asignarán a dos columnas independientes.</span><span class="sxs-lookup"><span data-stu-id="1873d-127">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="1873d-128">Sin embargo, si su tipo es idéntico, se pueden asignar a la misma columna de base de datos:</span><span class="sxs-lookup"><span data-stu-id="1873d-128">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
