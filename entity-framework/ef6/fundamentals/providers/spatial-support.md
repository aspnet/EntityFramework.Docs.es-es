---
title: 'Compatibilidad del proveedor con tipos espaciales: EF6'
description: Compatibilidad del proveedor con los tipos espaciales en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: 060d662aa8f03ea3510bd6b1fb7bdf904585efab
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89615793"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="767eb-103">Compatibilidad del proveedor con tipos espaciales</span><span class="sxs-lookup"><span data-stu-id="767eb-103">Provider Support for Spatial Types</span></span>
<span data-ttu-id="767eb-104">Entity Framework admite el trabajo con datos espaciales a través de las clases DbGeography o DbGeometry.</span><span class="sxs-lookup"><span data-stu-id="767eb-104">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="767eb-105">Estas clases se basan en la funcionalidad específica de la base de datos que ofrece el proveedor de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="767eb-105">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="767eb-106">No todos los proveedores admiten datos espaciales y los que pueden tener requisitos previos adicionales, como la instalación de ensamblados de tipo espacial.</span><span class="sxs-lookup"><span data-stu-id="767eb-106">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="767eb-107">A continuación se proporciona más información sobre la compatibilidad del proveedor con los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="767eb-107">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="767eb-108">Puede encontrar información adicional sobre cómo usar los tipos espaciales en una aplicación en dos tutoriales, uno para Code First, el otro para Database First o Model First:</span><span class="sxs-lookup"><span data-stu-id="767eb-108">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="767eb-109">Tipos de datos espaciales en Code First</span><span class="sxs-lookup"><span data-stu-id="767eb-109">Spatial Data Types in Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)  
- [<span data-ttu-id="767eb-110">Tipos de datos espaciales en EF Designer</span><span class="sxs-lookup"><span data-stu-id="767eb-110">Spatial Data Types in EF Designer</span></span>](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="767eb-111">Versiones de EF que admiten tipos espaciales</span><span class="sxs-lookup"><span data-stu-id="767eb-111">EF releases that support spatial types</span></span>  

<span data-ttu-id="767eb-112">La compatibilidad con los tipos espaciales se presentó en EF5.</span><span class="sxs-lookup"><span data-stu-id="767eb-112">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="767eb-113">Sin embargo, en los tipos espaciales de EF5 solo se admiten cuando la aplicación tiene como destino y se ejecuta en .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="767eb-113">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="767eb-114">A partir de EF6, se admiten los tipos espaciales para las aplicaciones que tienen como destino .NET 4 y .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="767eb-114">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="767eb-115">Proveedores de EF que admiten tipos espaciales</span><span class="sxs-lookup"><span data-stu-id="767eb-115">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="767eb-116">EF5</span><span class="sxs-lookup"><span data-stu-id="767eb-116">EF5</span></span>  

<span data-ttu-id="767eb-117">Los proveedores de Entity Framework para EF5 que somos conscientes de que admiten tipos espaciales son:</span><span class="sxs-lookup"><span data-stu-id="767eb-117">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="767eb-118">Proveedor de Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="767eb-118">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="767eb-119">Este proveedor se incluye como parte de EF5.</span><span class="sxs-lookup"><span data-stu-id="767eb-119">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="767eb-120">Este proveedor depende de algunas bibliotecas adicionales de bajo nivel que puedan ser necesarias para instalarse; consulte a continuación para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="767eb-120">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="767eb-121">Devart dotConnect para Oracle</span><span class="sxs-lookup"><span data-stu-id="767eb-121">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="767eb-122">Se trata de un proveedor de terceros de Devart.</span><span class="sxs-lookup"><span data-stu-id="767eb-122">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="767eb-123">Si conoce un proveedor de EF5 que admite tipos espaciales, póngase en contacto con y nos alegrará de agregarlo a esta lista.</span><span class="sxs-lookup"><span data-stu-id="767eb-123">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="767eb-124">EF6</span><span class="sxs-lookup"><span data-stu-id="767eb-124">EF6</span></span>  

<span data-ttu-id="767eb-125">Los proveedores de Entity Framework para EF6 que somos conscientes de que admiten tipos espaciales son:</span><span class="sxs-lookup"><span data-stu-id="767eb-125">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="767eb-126">Proveedor de Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="767eb-126">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="767eb-127">Este proveedor se incluye como parte de EF6.</span><span class="sxs-lookup"><span data-stu-id="767eb-127">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="767eb-128">Este proveedor depende de algunas bibliotecas adicionales de bajo nivel que puedan ser necesarias para instalarse; consulte a continuación para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="767eb-128">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="767eb-129">Devart dotConnect para Oracle</span><span class="sxs-lookup"><span data-stu-id="767eb-129">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="767eb-130">Se trata de un proveedor de terceros de Devart.</span><span class="sxs-lookup"><span data-stu-id="767eb-130">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="767eb-131">Si conoce un proveedor de EF6 que admite tipos espaciales, póngase en contacto con y nos alegrará de agregarlo a esta lista.</span><span class="sxs-lookup"><span data-stu-id="767eb-131">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="767eb-132">Requisitos previos para los tipos espaciales con Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="767eb-132">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="767eb-133">SQL Server compatibilidad espacial depende de los tipos de bajo nivel, específicos de SQL Server SqlGeography y SqlGeometry.</span><span class="sxs-lookup"><span data-stu-id="767eb-133">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="767eb-134">Estos tipos viven en Microsoft.SqlServer.Types.dll ensamblado, y este ensamblado no se envía como parte de EF o como parte de la .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="767eb-134">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="767eb-135">Cuando se instala Visual Studio, a menudo también se instala una versión de SQL Server, lo que incluye la instalación de la Microsoft.SqlServer.Types.dll.</span><span class="sxs-lookup"><span data-stu-id="767eb-135">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="767eb-136">Si SQL Server no está instalado en el equipo en el que desea usar tipos espaciales, o si los tipos espaciales se excluyen de la instalación de SQL Server, deberá instalarlos manualmente.</span><span class="sxs-lookup"><span data-stu-id="767eb-136">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="767eb-137">Los tipos se pueden instalar mediante `SQLSysClrTypes.msi` , que forma parte de Microsoft SQL Server Feature Pack.</span><span class="sxs-lookup"><span data-stu-id="767eb-137">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="767eb-138">Los tipos espaciales son SQL Server específicos de la versión, por lo que se recomienda [Buscar "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) en el centro de descarga de Microsoft y, a continuación, seleccionar y descargar la opción correspondiente a la versión de SQL Server que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="767eb-138">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>
