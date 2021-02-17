---
title: 'Datos espaciales: EF Core'
description: Usar datos espaciales en un modelo de Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 721aa2628d17b89b79160f8f658f8ef0dd78d6a6
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543294"
---
# <a name="spatial-data"></a><span data-ttu-id="7b2e1-103">Datos espaciales</span><span class="sxs-lookup"><span data-stu-id="7b2e1-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="7b2e1-104">Esta característica se presentó en EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-104">This feature was introduced in EF Core 2.2.</span></span>

<span data-ttu-id="7b2e1-105">Los datos espaciales representan la ubicación física y la forma de los objetos.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="7b2e1-106">Muchas bases de datos proporcionan compatibilidad con este tipo de datos, por lo que se puede indizar y consultar junto con otros datos.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="7b2e1-107">Entre los escenarios comunes se incluyen las consultas de objetos dentro de una distancia determinada desde una ubicación o la selección del objeto cuyo borde contiene una ubicación determinada.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="7b2e1-108">EF Core admite la asignación a tipos de datos espaciales mediante la biblioteca espacial NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-108">EF Core supports mapping to spatial data types using the NetTopologySuite spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="7b2e1-109">Instalando</span><span class="sxs-lookup"><span data-stu-id="7b2e1-109">Installing</span></span>

<span data-ttu-id="7b2e1-110">Para usar los datos espaciales con EF Core, debe instalar el paquete NuGet de soporte adecuado.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="7b2e1-111">El paquete que necesita instalar depende del proveedor que esté usando.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="7b2e1-112">Proveedor de EF Core</span><span class="sxs-lookup"><span data-stu-id="7b2e1-112">EF Core Provider</span></span>                        | <span data-ttu-id="7b2e1-113">Paquete de NuGet espacial</span><span class="sxs-lookup"><span data-stu-id="7b2e1-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="7b2e1-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7b2e1-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="7b2e1-115">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="7b2e1-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="7b2e1-117">Microsoft. EntityFrameworkCore. SQLite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="7b2e1-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="7b2e1-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="7b2e1-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="7b2e1-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="7b2e1-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="7b2e1-121">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
<span data-ttu-id="7b2e1-122">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="7b2e1-122">Pomelo.EntityFrameworkCore.MySql</span></span>        | [<span data-ttu-id="7b2e1-123">Pomelo. EntityFrameworkCore. MySql. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-123">Pomelo.EntityFrameworkCore.MySql.NetTopologySuite</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
<span data-ttu-id="7b2e1-124">Devart.Data.MySql.EFCore</span><span class="sxs-lookup"><span data-stu-id="7b2e1-124">Devart.Data.MySql.EFCore</span></span>                | [<span data-ttu-id="7b2e1-125">Devart. Data. MySql. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-125">Devart.Data.MySql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
<span data-ttu-id="7b2e1-126">Devart.Data.PostgreSql.EFCore</span><span class="sxs-lookup"><span data-stu-id="7b2e1-126">Devart.Data.PostgreSql.EFCore</span></span>           | [<span data-ttu-id="7b2e1-127">Devart. Data. PostgreSql. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-127">Devart.Data.PostgreSql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
<span data-ttu-id="7b2e1-128">Devart.Data.SQLite.EFCore</span><span class="sxs-lookup"><span data-stu-id="7b2e1-128">Devart.Data.SQLite.EFCore</span></span>               | [<span data-ttu-id="7b2e1-129">Devart. Data. SQLite. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-129">Devart.Data.SQLite.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
<span data-ttu-id="7b2e1-130">Teradata.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7b2e1-130">Teradata.EntityFrameworkCore</span></span>            | [<span data-ttu-id="7b2e1-131">Teradata. EntityFrameworkCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-131">Teradata.EntityFrameworkCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a><span data-ttu-id="7b2e1-132">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-132">NetTopologySuite</span></span>

<span data-ttu-id="7b2e1-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) es una biblioteca espacial para .net.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) is a spatial library for .NET.</span></span> <span data-ttu-id="7b2e1-134">EF Core permite la asignación a tipos de datos espaciales en la base de datos mediante el uso de tipos NTS en el modelo.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-134">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="7b2e1-135">Para habilitar la asignación a tipos espaciales a través de NTS, llame al método UseNetTopologySuite en el generador de opciones DbContext del proveedor.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-135">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="7b2e1-136">Por ejemplo, con SQL Server le llamaría como esto.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-136">For example, with SQL Server you'd call it like this.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

<span data-ttu-id="7b2e1-137">Hay varios tipos de datos espaciales.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-137">There are several spatial data types.</span></span> <span data-ttu-id="7b2e1-138">El tipo que use dependerá de los tipos de formas que desee permitir.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-138">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="7b2e1-139">Esta es la jerarquía de tipos NTS que puede usar para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-139">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="7b2e1-140">Están ubicados en el `NetTopologySuite.Geometries` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-140">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="7b2e1-141">Geometría</span><span class="sxs-lookup"><span data-stu-id="7b2e1-141">Geometry</span></span>
  * <span data-ttu-id="7b2e1-142">Punto</span><span class="sxs-lookup"><span data-stu-id="7b2e1-142">Point</span></span>
  * <span data-ttu-id="7b2e1-143">LineString</span><span class="sxs-lookup"><span data-stu-id="7b2e1-143">LineString</span></span>
  * <span data-ttu-id="7b2e1-144">Polygon</span><span class="sxs-lookup"><span data-stu-id="7b2e1-144">Polygon</span></span>
  * <span data-ttu-id="7b2e1-145">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="7b2e1-145">GeometryCollection</span></span>
    * <span data-ttu-id="7b2e1-146">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="7b2e1-146">MultiPoint</span></span>
    * <span data-ttu-id="7b2e1-147">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="7b2e1-147">MultiLineString</span></span>
    * <span data-ttu-id="7b2e1-148">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="7b2e1-148">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="7b2e1-149">La CircularString, CompoundCurve y CurePolygon no son compatibles con NTS.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-149">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="7b2e1-150">El uso del tipo de geometría base permite que la propiedad especifique cualquier tipo de forma.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-150">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

## <a name="longitude-and-latitude"></a><span data-ttu-id="7b2e1-151">Longitud y latitud</span><span class="sxs-lookup"><span data-stu-id="7b2e1-151">Longitude and Latitude</span></span>

<span data-ttu-id="7b2e1-152">Las coordenadas en NTS están en términos de valores X e y.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-152">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="7b2e1-153">Para representar la longitud y la latitud, use X para longitud e y para latitud.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-153">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="7b2e1-154">Tenga en cuenta que esto es **hacia atrás** desde el `latitude, longitude` formato en el que normalmente se ven estos valores.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-154">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

## <a name="querying-data"></a><span data-ttu-id="7b2e1-155">Consulta de datos</span><span class="sxs-lookup"><span data-stu-id="7b2e1-155">Querying Data</span></span>

<span data-ttu-id="7b2e1-156">Las siguientes clases de entidad se pueden usar para asignar tablas en la [base de datos de ejemplo Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630).</span><span class="sxs-lookup"><span data-stu-id="7b2e1-156">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

<span data-ttu-id="7b2e1-157">En LINQ, los métodos y las propiedades NTS disponibles como funciones de base de datos se traducirán a SQL.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-157">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="7b2e1-158">Por ejemplo, los métodos Distance y Contains se traducen en las siguientes consultas.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-158">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="7b2e1-159">Consulte la documentación del proveedor para saber qué métodos se admiten.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-159">See your provider's documentation for which methods are supported.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a><span data-ttu-id="7b2e1-160">Ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="7b2e1-160">Reverse engineering</span></span>

<span data-ttu-id="7b2e1-161">Los paquetes de NuGet espaciales también habilitan los modelos de [ingeniería inversa](xref:core/managing-schemas/scaffolding) con propiedades espaciales, pero debe instalar el paquete ***antes*** de ejecutar `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="7b2e1-161">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="7b2e1-162">Si no lo hace, recibirá advertencias sobre cómo no encontrar las asignaciones de tipos para las columnas y se omitirán las columnas.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-162">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="7b2e1-163">SRID omitido durante las operaciones de cliente</span><span class="sxs-lookup"><span data-stu-id="7b2e1-163">SRID Ignored during client operations</span></span>

<span data-ttu-id="7b2e1-164">NTS omite los valores de SRID durante las operaciones.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-164">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="7b2e1-165">Supone un sistema de coordenadas plano.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-165">It assumes a planar coordinate system.</span></span> <span data-ttu-id="7b2e1-166">Esto significa que si especifica coordenadas en términos de longitud y latitud, algunos valores evaluados por el cliente como la distancia, la longitud y el área estarán en grados, no en metros.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-166">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="7b2e1-167">Para valores más significativos, primero debe proyectar las coordenadas en otro sistema de coordenadas mediante una biblioteca como [ProjNet (para GeoAPI)](https://github.com/NetTopologySuite/ProjNet4GeoAPI).</span><span class="sxs-lookup"><span data-stu-id="7b2e1-167">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet (for GeoAPI)](https://github.com/NetTopologySuite/ProjNet4GeoAPI).</span></span>

> [!NOTE]
> <span data-ttu-id="7b2e1-168">Use el [paquete NuGet de ProjNet](https://www.nuget.org/packages/ProjNet/)más reciente, **no** el paquete anterior denominado ProjNet4GeoAPI.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-168">Use the newer [ProjNet NuGet package](https://www.nuget.org/packages/ProjNet/), **not** the older package called ProjNet4GeoAPI.</span></span>

<span data-ttu-id="7b2e1-169">Si una operación es evaluada por el servidor mediante EF Core a través de SQL, la unidad del resultado se determinará por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-169">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="7b2e1-170">Este es un ejemplo del uso de ProjNet para calcular la distancia entre dos ciudades.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-170">Here is an example of using ProjNet to calculate the distance between two cities.</span></span>

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> <span data-ttu-id="7b2e1-171">4326 hace referencia a WGS 84, un estándar que se usa en GPS y en otros sistemas geográficos.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-171">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b2e1-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7b2e1-172">Additional resources</span></span>

### <a name="database-specific-information"></a><span data-ttu-id="7b2e1-173">Información específica de la base de datos</span><span class="sxs-lookup"><span data-stu-id="7b2e1-173">Database-specific information</span></span>

<span data-ttu-id="7b2e1-174">Asegúrese de leer la documentación del proveedor para obtener información adicional sobre cómo trabajar con datos espaciales.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-174">Be sure to read your provider's documentation for additional information on working with spatial data.</span></span>

* [<span data-ttu-id="7b2e1-175">Datos espaciales en el proveedor de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7b2e1-175">Spatial Data in the SQL Server Provider</span></span>](xref:core/providers/sql-server/spatial)
* [<span data-ttu-id="7b2e1-176">Datos espaciales en el proveedor de SQLite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-176">Spatial Data in the SQLite Provider</span></span>](xref:core/providers/sqlite/spatial)
* [<span data-ttu-id="7b2e1-177">Datos espaciales en el proveedor Npgsql</span><span class="sxs-lookup"><span data-stu-id="7b2e1-177">Spatial Data in the Npgsql Provider</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)

### <a name="other-resources"></a><span data-ttu-id="7b2e1-178">Otros recursos</span><span class="sxs-lookup"><span data-stu-id="7b2e1-178">Other resources</span></span>

* [<span data-ttu-id="7b2e1-179">Documentos de NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7b2e1-179">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
* <span data-ttu-id="7b2e1-180">[EF Core sesión reunión](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15)de la comunidad, centrándose en los datos espaciales y NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="7b2e1-180">[EF Core Community Standup session](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15), focusing on spatial data and NetTopologySuite.</span></span>
