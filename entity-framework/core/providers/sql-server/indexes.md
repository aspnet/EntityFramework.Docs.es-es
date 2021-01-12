---
title: 'Proveedor de base de datos de Microsoft SQL Server: índices EF Core'
description: Características de índice específicas del proveedor de SQL Server de Entity Framework Core
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129166"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="73d59-103">Características de índice específicas del proveedor de SQL Server de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="73d59-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="73d59-104">En esta página se detallan las opciones de configuración del índice que son específicas del proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="73d59-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="73d59-105">Agrupación en clústeres</span><span class="sxs-lookup"><span data-stu-id="73d59-105">Clustering</span></span>

<span data-ttu-id="73d59-106">Los índices clúster ordenan y almacenan las filas de los datos de la tabla o vista de acuerdo con los valores de la clave del índice.</span><span class="sxs-lookup"><span data-stu-id="73d59-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="73d59-107">La creación del índice clúster adecuado para la tabla puede mejorar significativamente la velocidad de las consultas, ya que los datos ya están dispuestos en el orden óptimo.</span><span class="sxs-lookup"><span data-stu-id="73d59-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="73d59-108">Solo puede haber un índice clúster por cada tabla, porque las filas de datos solo pueden estar almacenadas de una forma.</span><span class="sxs-lookup"><span data-stu-id="73d59-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="73d59-109">Para obtener más información, consulte [la documentación de SQL Server sobre los índices agrupados y no agrupados](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span><span class="sxs-lookup"><span data-stu-id="73d59-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="73d59-110">De forma predeterminada, la columna de clave principal de una tabla está respaldada implícitamente por un índice clúster y el resto de los índices no están agrupados.</span><span class="sxs-lookup"><span data-stu-id="73d59-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="73d59-111">Puede configurar un índice o una clave para agruparlos como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="73d59-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> <span data-ttu-id="73d59-112">SQL Server solo admite un índice clúster por tabla y la clave principal está agrupada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="73d59-112">SQL Server only supports one clustered index per table, and the primary key is by default clustered.</span></span> <span data-ttu-id="73d59-113">Si desea tener un índice clúster en una columna que no sea de clave, debe hacer explícitamente que la clave no esté en clúster.</span><span class="sxs-lookup"><span data-stu-id="73d59-113">If you'd like to have a clustered index on a non-key column, you must explicitly make your key non-clustered.</span></span>

## <a name="fill-factor"></a><span data-ttu-id="73d59-114">Factor de relleno</span><span class="sxs-lookup"><span data-stu-id="73d59-114">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="73d59-115">Esta característica se incluyó por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="73d59-115">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="73d59-116">La opción de factor de relleno de índice se proporciona para ajustar el rendimiento y el almacenamiento de datos de índice.</span><span class="sxs-lookup"><span data-stu-id="73d59-116">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="73d59-117">Para obtener más información, consulte [la documentación de SQL Server sobre el factor de relleno](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span><span class="sxs-lookup"><span data-stu-id="73d59-117">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="73d59-118">Puede configurar el factor de relleno de un índice como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="73d59-118">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="73d59-119">Creación en línea</span><span class="sxs-lookup"><span data-stu-id="73d59-119">Online creation</span></span>

<span data-ttu-id="73d59-120">La opción ONLINE permite el acceso simultáneo de los usuarios a los datos de la tabla subyacente o del índice clúster, así como a los índices no clúster asociados durante la creación del índice, de modo que los usuarios puedan seguir actualizando y consultando los datos subyacentes.</span><span class="sxs-lookup"><span data-stu-id="73d59-120">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="73d59-121">Al realizar operaciones DDL (lenguaje de definición de datos) sin conexión, como generar o volver a generar un índice clúster, estas operaciones mantienen bloqueos exclusivos de los datos subyacentes y los índices asociados.</span><span class="sxs-lookup"><span data-stu-id="73d59-121">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="73d59-122">Para obtener más información, consulte [la documentación de SQL Server en la opción de índice en línea](/sql/relational-databases/indexes/perform-index-operations-online).</span><span class="sxs-lookup"><span data-stu-id="73d59-122">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="73d59-123">Puede configurar un índice con la opción ONLINE de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="73d59-123">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
