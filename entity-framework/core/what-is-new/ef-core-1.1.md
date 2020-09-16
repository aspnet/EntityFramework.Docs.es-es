---
title: Novedades de EF Core 1.1 - EF Core
description: Cambios y mejoras en Entity Framework Core 1.1
author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 89dfe4b4772e9e6dc232860f38f07c94654af437
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618816"
---
# <a name="new-features-in-ef-core-11"></a><span data-ttu-id="0092a-103">Características nuevas en EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="0092a-103">New features in EF Core 1.1</span></span>

## <a name="modeling"></a><span data-ttu-id="0092a-104">Modelado</span><span class="sxs-lookup"><span data-stu-id="0092a-104">Modeling</span></span>

### <a name="field-mapping"></a><span data-ttu-id="0092a-105">Asignación de campos</span><span class="sxs-lookup"><span data-stu-id="0092a-105">Field mapping</span></span>

<span data-ttu-id="0092a-106">Permite configurar un campo de respaldo para una propiedad.</span><span class="sxs-lookup"><span data-stu-id="0092a-106">Allows you to configure a backing field for a property.</span></span> <span data-ttu-id="0092a-107">Puede resultar útil en las propiedades de solo lectura o en los datos que tienen métodos Get/Set en lugar de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="0092a-107">This can be useful for read-only properties, or data that has Get/Set methods rather than a property.</span></span>

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a><span data-ttu-id="0092a-108">Asignación a tablas optimizadas para memoria en SQL Server</span><span class="sxs-lookup"><span data-stu-id="0092a-108">Mapping to Memory-Optimized Tables in SQL Server</span></span>

<span data-ttu-id="0092a-109">Puede especificar que la tabla a la que está asignada una entidad está optimizada para memoria.</span><span class="sxs-lookup"><span data-stu-id="0092a-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="0092a-110">Cuando use EF Core para crear y mantener una base de datos basada en el modelo (ya sea con migraciones o `Database.EnsureCreated()`), se creará una tabla optimizada para memoria para estas entidades.</span><span class="sxs-lookup"><span data-stu-id="0092a-110">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="0092a-111">seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="0092a-111">Change tracking</span></span>

### <a name="additional-change-tracking-apis-from-ef6"></a><span data-ttu-id="0092a-112">API adicionales de seguimiento de cambios de EF6</span><span class="sxs-lookup"><span data-stu-id="0092a-112">Additional change tracking APIs from EF6</span></span>

<span data-ttu-id="0092a-113">Como `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span><span class="sxs-lookup"><span data-stu-id="0092a-113">Such as `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span></span>

## <a name="query"></a><span data-ttu-id="0092a-114">Consultar</span><span class="sxs-lookup"><span data-stu-id="0092a-114">Query</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="0092a-115">Carga explícita</span><span class="sxs-lookup"><span data-stu-id="0092a-115">Explicit Loading</span></span>

<span data-ttu-id="0092a-116">Permite desencadenar el rellenado de una propiedad de navegación o una entidad que se cargó anteriormente a partir de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0092a-116">Allows you to trigger population of a navigation property on an entity that was previously loaded from the database.</span></span>

### <a name="dbsetfind"></a><span data-ttu-id="0092a-117">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="0092a-117">DbSet.Find</span></span>

<span data-ttu-id="0092a-118">Proporciona una manera sencilla de capturar una entidad en función de su valor de clave principal.</span><span class="sxs-lookup"><span data-stu-id="0092a-118">Provides an easy way to fetch an entity based on its primary key value.</span></span>

## <a name="other"></a><span data-ttu-id="0092a-119">Otros</span><span class="sxs-lookup"><span data-stu-id="0092a-119">Other</span></span>

### <a name="connection-resiliency"></a><span data-ttu-id="0092a-120">Resistencia de la conexión</span><span class="sxs-lookup"><span data-stu-id="0092a-120">Connection resiliency</span></span>

<span data-ttu-id="0092a-121">Reintenta automáticamente los comandos de base de datos erróneos.</span><span class="sxs-lookup"><span data-stu-id="0092a-121">Automatically retries failed database commands.</span></span> <span data-ttu-id="0092a-122">Esto resulta especialmente útil cuando se realizan conexiones a SQL Azure, donde los errores transitorios son comunes.</span><span class="sxs-lookup"><span data-stu-id="0092a-122">This is especially useful when connection to SQL Azure, where transient failures are common.</span></span>

### <a name="simplified-service-replacement"></a><span data-ttu-id="0092a-123">Reemplazo de servicio simplificado</span><span class="sxs-lookup"><span data-stu-id="0092a-123">Simplified service replacement</span></span>

<span data-ttu-id="0092a-124">Facilita el reemplazo de servicios internos que EF usa.</span><span class="sxs-lookup"><span data-stu-id="0092a-124">Makes it easier to replace internal services that EF uses.</span></span>
