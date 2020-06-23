---
title: 'Cambios importantes en EF Core 5.0: EF Core'
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666172"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="d9527-102">Cambios importantes en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d9527-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="d9527-103">Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="d9527-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="d9527-104">Resumen</span><span class="sxs-lookup"><span data-stu-id="d9527-104">Summary</span></span>

| <span data-ttu-id="d9527-105">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="d9527-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="d9527-106">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="d9527-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="d9527-107">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="d9527-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="d9527-108">Bajo</span><span class="sxs-lookup"><span data-stu-id="d9527-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="d9527-109">Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="d9527-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="d9527-110">Problema de seguimiento n.º 14257</span><span class="sxs-lookup"><span data-stu-id="d9527-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="d9527-111">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="d9527-111">**Old behavior**</span></span>

<span data-ttu-id="d9527-112">Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría.</span><span class="sxs-lookup"><span data-stu-id="d9527-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="d9527-113">Sin embargo, solo ha afectado a la creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d9527-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="d9527-114">No era necesario especificarlo para consultar los valores con dimensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="d9527-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="d9527-115">Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="d9527-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="d9527-116">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="d9527-116">**New behavior**</span></span>

<span data-ttu-id="d9527-117">Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="d9527-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="d9527-118">Esto coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="d9527-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="d9527-119">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="d9527-119">**Why**</span></span>

<span data-ttu-id="d9527-120">El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.</span><span class="sxs-lookup"><span data-stu-id="d9527-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="d9527-121">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="d9527-121">**Mitigations**</span></span>

<span data-ttu-id="d9527-122">Use `HasColumnType` para especificar la dimensión:</span><span class="sxs-lookup"><span data-stu-id="d9527-122">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
