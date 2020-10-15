---
title: Proveedor de base de datos de SQLite-datos espaciales-EF Core
description: Uso de datos espaciales con el proveedor de base de datos de Entity Framework Core SQLite
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: 3296955dc046b91b53a1dcb09c51b340bc853b4a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066569"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a><span data-ttu-id="4a997-103">Datos espaciales en el proveedor de EF Core de SQLite</span><span class="sxs-lookup"><span data-stu-id="4a997-103">Spatial Data in the SQLite EF Core Provider</span></span>

<span data-ttu-id="4a997-104">Esta página incluye información adicional sobre el uso de datos espaciales con el proveedor de base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="4a997-104">This page includes additional information about using spatial data with the SQLite database provider.</span></span> <span data-ttu-id="4a997-105">Para obtener información general sobre el uso de datos espaciales en EF Core, consulte la documentación principal de los [datos espaciales](xref:core/modeling/spatial) .</span><span class="sxs-lookup"><span data-stu-id="4a997-105">For general information about using spatial data in EF Core, see the main [Spatial Data](xref:core/modeling/spatial) documentation.</span></span>

## <a name="installing-spatialite"></a><span data-ttu-id="4a997-106">Instalación de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="4a997-106">Installing SpatiaLite</span></span>

<span data-ttu-id="4a997-107">En Windows, la biblioteca de mod_spatialite nativa se distribuye como una dependencia del paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="4a997-107">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="4a997-108">Otras plataformas deben instalarse por separado.</span><span class="sxs-lookup"><span data-stu-id="4a997-108">Other platforms need to install it separately.</span></span> <span data-ttu-id="4a997-109">Esto se suele hacer mediante un administrador de paquetes de software.</span><span class="sxs-lookup"><span data-stu-id="4a997-109">This is typically done using a software package manager.</span></span> <span data-ttu-id="4a997-110">Por ejemplo, puede usar APT en Debian y Ubuntu; y homebrew en MacOS.</span><span class="sxs-lookup"><span data-stu-id="4a997-110">For example, you can use APT on Debian and Ubuntu; and Homebrew on MacOS.</span></span>

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="4a997-111">Desafortunadamente, las versiones más recientes de PROJ (una dependencia de SpatiaLite) son incompatibles con el [paquete SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predeterminado de EF.</span><span class="sxs-lookup"><span data-stu-id="4a997-111">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="4a997-112">Puede solucionar este fin mediante el uso de la biblioteca de SQLite del sistema en su lugar.</span><span class="sxs-lookup"><span data-stu-id="4a997-112">You can work around this by using the system SQLite library instead.</span></span>

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

<span data-ttu-id="4a997-113">En **MacOS**, también necesitará establecer una variable de entorno antes de ejecutar la aplicación para que use la versión de homebrew de SQLite.</span><span class="sxs-lookup"><span data-stu-id="4a997-113">On **macOS**, you'll also need set an environment variable before running your app so it uses Homebrew's version of SQLite.</span></span> <span data-ttu-id="4a997-114">En Visual Studio para Mac, puede establecer esta opción en **project > Project options > Run >** configurations > default</span><span class="sxs-lookup"><span data-stu-id="4a997-114">In Visual Studio for Mac, you can set this under **Project > Project Options > Run > Configurations > Default**</span></span>

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a><span data-ttu-id="4a997-115">Configuración de SRID</span><span class="sxs-lookup"><span data-stu-id="4a997-115">Configuring SRID</span></span>

<span data-ttu-id="4a997-116">En SpatiaLite, las columnas deben especificar un SRID por columna.</span><span class="sxs-lookup"><span data-stu-id="4a997-116">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="4a997-117">El valor predeterminado de SRID es `0` .</span><span class="sxs-lookup"><span data-stu-id="4a997-117">The default SRID is `0`.</span></span> <span data-ttu-id="4a997-118">Especifique otro SRID con el método ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="4a997-118">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> <span data-ttu-id="4a997-119">4326 hace referencia a WGS 84, un estándar que se usa en GPS y en otros sistemas geográficos.</span><span class="sxs-lookup"><span data-stu-id="4a997-119">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="dimension"></a><span data-ttu-id="4a997-120">Dimensión</span><span class="sxs-lookup"><span data-stu-id="4a997-120">Dimension</span></span>

<span data-ttu-id="4a997-121">La dimensión predeterminada (o ordenadas) de una columna es X e y. Para habilitar otras coordenadas como Z o M, configure el tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="4a997-121">The default dimension (or ordinates) of a column is X and Y. To enable additional ordinates like Z or M, configure the column type.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a><span data-ttu-id="4a997-122">Asignaciones de funciones espaciales</span><span class="sxs-lookup"><span data-stu-id="4a997-122">Spatial function mappings</span></span>

<span data-ttu-id="4a997-123">En esta tabla se muestran los miembros de [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) que se traducen en las funciones de SQL.</span><span class="sxs-lookup"><span data-stu-id="4a997-123">This table shows which [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) members are translated into which SQL functions.</span></span>

<span data-ttu-id="4a997-124">.NET</span><span class="sxs-lookup"><span data-stu-id="4a997-124">.NET</span></span>                                        | <span data-ttu-id="4a997-125">SQL</span><span class="sxs-lookup"><span data-stu-id="4a997-125">SQL</span></span>
------------------------------------------- | ---
<span data-ttu-id="4a997-126">Geometry. Áreas</span><span class="sxs-lookup"><span data-stu-id="4a997-126">geometry.Area</span></span>                               | <span data-ttu-id="4a997-127">Área ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-127">Area(@geometry)</span></span>
<span data-ttu-id="4a997-128">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="4a997-128">geometry.AsBinary()</span></span>                         | <span data-ttu-id="4a997-129">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-129">AsBinary(@geometry)</span></span>
<span data-ttu-id="4a997-130">Geometry. Astext ()</span><span class="sxs-lookup"><span data-stu-id="4a997-130">geometry.AsText()</span></span>                           | <span data-ttu-id="4a997-131">Astext ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-131">AsText(@geometry)</span></span>
<span data-ttu-id="4a997-132">Geometry. Exceso</span><span class="sxs-lookup"><span data-stu-id="4a997-132">geometry.Boundary</span></span>                           | <span data-ttu-id="4a997-133">Límite ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-133">Boundary(@geometry)</span></span>
<span data-ttu-id="4a997-134">Geometry. Búfer (distancia)</span><span class="sxs-lookup"><span data-stu-id="4a997-134">geometry.Buffer(distance)</span></span>                   | <span data-ttu-id="4a997-135">Búfer ( @geometry , @distance )</span><span class="sxs-lookup"><span data-stu-id="4a997-135">Buffer(@geometry, @distance)</span></span>
<span data-ttu-id="4a997-136">Geometry. Búfer (distancia, quadrantSegments)</span><span class="sxs-lookup"><span data-stu-id="4a997-136">geometry.Buffer(distance, quadrantSegments)</span></span> | <span data-ttu-id="4a997-137">Búfer ( @geometry , @distance , @quadrantSegments )</span><span class="sxs-lookup"><span data-stu-id="4a997-137">Buffer(@geometry, @distance, @quadrantSegments)</span></span>
<span data-ttu-id="4a997-138">Geometry. Centroide</span><span class="sxs-lookup"><span data-stu-id="4a997-138">geometry.Centroid</span></span>                           | <span data-ttu-id="4a997-139">Centroide ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-139">Centroid(@geometry)</span></span>
<span data-ttu-id="4a997-140">Geometry. Contains (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-140">geometry.Contains(g)</span></span>                        | <span data-ttu-id="4a997-141">Contains ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-141">Contains(@geometry, @g)</span></span>
<span data-ttu-id="4a997-142">Geometry. ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="4a997-142">geometry.ConvexHull()</span></span>                       | <span data-ttu-id="4a997-143">ConvexHull ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-143">ConvexHull(@geometry)</span></span>
<span data-ttu-id="4a997-144">Geometry. CoveredBy (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-144">geometry.CoveredBy(g)</span></span>                       | <span data-ttu-id="4a997-145">CoveredBy ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-145">CoveredBy(@geometry, @g)</span></span>
<span data-ttu-id="4a997-146">Geometry. Portadas (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-146">geometry.Covers(g)</span></span>                          | <span data-ttu-id="4a997-147">Portadas ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-147">Covers(@geometry, @g)</span></span>
<span data-ttu-id="4a997-148">Geometry. Cruza (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-148">geometry.Crosses(g)</span></span>                         | <span data-ttu-id="4a997-149">Cruza ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-149">Crosses(@geometry, @g)</span></span>
<span data-ttu-id="4a997-150">Geometry. Diferencia (otros)</span><span class="sxs-lookup"><span data-stu-id="4a997-150">geometry.Difference(other)</span></span>                  | <span data-ttu-id="4a997-151">Diferencia ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4a997-151">Difference(@geometry, @other)</span></span>
<span data-ttu-id="4a997-152">Geometry. Galería</span><span class="sxs-lookup"><span data-stu-id="4a997-152">geometry.Dimension</span></span>                          | <span data-ttu-id="4a997-153">Dimension ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-153">Dimension(@geometry)</span></span>
<span data-ttu-id="4a997-154">Geometry. Disunion (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-154">geometry.Disjoint(g)</span></span>                        | <span data-ttu-id="4a997-155">Disjuntos ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-155">Disjoint(@geometry, @g)</span></span>
<span data-ttu-id="4a997-156">Geometry. Distancia (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-156">geometry.Distance(g)</span></span>                        | <span data-ttu-id="4a997-157">Distancia ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-157">Distance(@geometry, @g)</span></span>
<span data-ttu-id="4a997-158">Geometry. Tales</span><span class="sxs-lookup"><span data-stu-id="4a997-158">geometry.Envelope</span></span>                           | <span data-ttu-id="4a997-159">Sobre ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-159">Envelope(@geometry)</span></span>
<span data-ttu-id="4a997-160">Geometry. EqualsTopologically (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-160">geometry.EqualsTopologically(g)</span></span>             | <span data-ttu-id="4a997-161">Es igual a ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-161">Equals(@geometry, @g)</span></span>
<span data-ttu-id="4a997-162">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="4a997-162">geometry.GeometryType</span></span>                       | <span data-ttu-id="4a997-163">GeometryType ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-163">GeometryType(@geometry)</span></span>
<span data-ttu-id="4a997-164">Geometry. GetGeometryN (n)</span><span class="sxs-lookup"><span data-stu-id="4a997-164">geometry.GetGeometryN(n)</span></span>                    | <span data-ttu-id="4a997-165">Geometryn ( @geometry , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="4a997-165">GeometryN(@geometry, @n + 1)</span></span>
<span data-ttu-id="4a997-166">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="4a997-166">geometry.InteriorPoint</span></span>                      | <span data-ttu-id="4a997-167">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-167">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="4a997-168">Geometry. Intersección (otro)</span><span class="sxs-lookup"><span data-stu-id="4a997-168">geometry.Intersection(other)</span></span>                | <span data-ttu-id="4a997-169">Intersección ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4a997-169">Intersection(@geometry, @other)</span></span>
<span data-ttu-id="4a997-170">Geometry. Interseccións (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-170">geometry.Intersects(g)</span></span>                      | <span data-ttu-id="4a997-171">Interseccións ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-171">Intersects(@geometry, @g)</span></span>
<span data-ttu-id="4a997-172">Geometry. Vacío</span><span class="sxs-lookup"><span data-stu-id="4a997-172">geometry.IsEmpty</span></span>                            | <span data-ttu-id="4a997-173">IsEmpty ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-173">IsEmpty(@geometry)</span></span>
<span data-ttu-id="4a997-174">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="4a997-174">geometry.IsSimple</span></span>                           | <span data-ttu-id="4a997-175">IsSimple ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-175">IsSimple(@geometry)</span></span>
<span data-ttu-id="4a997-176">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="4a997-176">geometry.IsValid</span></span>                            | <span data-ttu-id="4a997-177">IsValid ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-177">IsValid(@geometry)</span></span>
<span data-ttu-id="4a997-178">Geometry. IsWithinDistance (Geom, Distance)</span><span class="sxs-lookup"><span data-stu-id="4a997-178">geometry.IsWithinDistance(geom, distance)</span></span>   | <span data-ttu-id="4a997-179">Distance ( @geometry , @geom ) <= @distance</span><span class="sxs-lookup"><span data-stu-id="4a997-179">Distance(@geometry, @geom) <= @distance</span></span>
<span data-ttu-id="4a997-180">Geometry. Longitud</span><span class="sxs-lookup"><span data-stu-id="4a997-180">geometry.Length</span></span>                             | <span data-ttu-id="4a997-181">GLength ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-181">GLength(@geometry)</span></span>
<span data-ttu-id="4a997-182">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="4a997-182">geometry.NumGeometries</span></span>                      | <span data-ttu-id="4a997-183">NumGeometries ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-183">NumGeometries(@geometry)</span></span>
<span data-ttu-id="4a997-184">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="4a997-184">geometry.NumPoints</span></span>                          | <span data-ttu-id="4a997-185">NumPoints ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-185">NumPoints(@geometry)</span></span>
<span data-ttu-id="4a997-186">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="4a997-186">geometry.OgcGeometryType</span></span>                    | <span data-ttu-id="4a997-187">CASE GeometryType ( @geometry ) cuando ' Point ' then 1... EXTREMO</span><span class="sxs-lookup"><span data-stu-id="4a997-187">CASE GeometryType(@geometry) WHEN 'POINT' THEN 1 ... END</span></span>
<span data-ttu-id="4a997-188">Geometry. Superposiciones (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-188">geometry.Overlaps(g)</span></span>                        | <span data-ttu-id="4a997-189">Solapamientos ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-189">Overlaps(@geometry, @g)</span></span>
<span data-ttu-id="4a997-190">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="4a997-190">geometry.PointOnSurface</span></span>                     | <span data-ttu-id="4a997-191">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-191">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="4a997-192">Geometry. Relacionar (g, intersectionPattern)</span><span class="sxs-lookup"><span data-stu-id="4a997-192">geometry.Relate(g, intersectionPattern)</span></span>     | <span data-ttu-id="4a997-193">Relacionar ( @geometry , @g , @intersectionPattern )</span><span class="sxs-lookup"><span data-stu-id="4a997-193">Relate(@geometry, @g, @intersectionPattern)</span></span>
<span data-ttu-id="4a997-194">Geometry. REVERSE ()</span><span class="sxs-lookup"><span data-stu-id="4a997-194">geometry.Reverse()</span></span>                          | <span data-ttu-id="4a997-195">ST_Reverse ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-195">ST_Reverse(@geometry)</span></span>
<span data-ttu-id="4a997-196">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="4a997-196">geometry.SRID</span></span>                               | <span data-ttu-id="4a997-197">SRID ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-197">SRID(@geometry)</span></span>
<span data-ttu-id="4a997-198">Geometry. SymmetricDifference (otro)</span><span class="sxs-lookup"><span data-stu-id="4a997-198">geometry.SymmetricDifference(other)</span></span>         | <span data-ttu-id="4a997-199">SymDifference ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4a997-199">SymDifference(@geometry, @other)</span></span>
<span data-ttu-id="4a997-200">Geometry. ToBinary()</span><span class="sxs-lookup"><span data-stu-id="4a997-200">geometry.ToBinary()</span></span>                         | <span data-ttu-id="4a997-201">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-201">AsBinary(@geometry)</span></span>
<span data-ttu-id="4a997-202">Geometry. ToText()</span><span class="sxs-lookup"><span data-stu-id="4a997-202">geometry.ToText()</span></span>                           | <span data-ttu-id="4a997-203">Astext ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-203">AsText(@geometry)</span></span>
<span data-ttu-id="4a997-204">Geometry. Toques (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-204">geometry.Touches(g)</span></span>                         | <span data-ttu-id="4a997-205">Toques ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-205">Touches(@geometry, @g)</span></span>
<span data-ttu-id="4a997-206">Geometry. Unión ()</span><span class="sxs-lookup"><span data-stu-id="4a997-206">geometry.Union()</span></span>                            | <span data-ttu-id="4a997-207">UnaryUnion ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4a997-207">UnaryUnion(@geometry)</span></span>
<span data-ttu-id="4a997-208">Geometry. Unión (otro)</span><span class="sxs-lookup"><span data-stu-id="4a997-208">geometry.Union(other)</span></span>                       | <span data-ttu-id="4a997-209">GUnion ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4a997-209">GUnion(@geometry, @other)</span></span>
<span data-ttu-id="4a997-210">Geometry. Dentro de (g)</span><span class="sxs-lookup"><span data-stu-id="4a997-210">geometry.Within(g)</span></span>                          | <span data-ttu-id="4a997-211">Dentro de ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4a997-211">Within(@geometry, @g)</span></span>
<span data-ttu-id="4a997-212">geometryCollection [i]</span><span class="sxs-lookup"><span data-stu-id="4a997-212">geometryCollection[i]</span></span>                       | <span data-ttu-id="4a997-213">Geometryn ( @geometryCollection , @i + 1)</span><span class="sxs-lookup"><span data-stu-id="4a997-213">GeometryN(@geometryCollection, @i + 1)</span></span>
<span data-ttu-id="4a997-214">geometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="4a997-214">geometryCollection.Count</span></span>                    | <span data-ttu-id="4a997-215">NumGeometries ( @geometryCollection )</span><span class="sxs-lookup"><span data-stu-id="4a997-215">NumGeometries(@geometryCollection)</span></span>
<span data-ttu-id="4a997-216">lineString. Count</span><span class="sxs-lookup"><span data-stu-id="4a997-216">lineString.Count</span></span>                            | <span data-ttu-id="4a997-217">NumPoints ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4a997-217">NumPoints(@lineString)</span></span>
<span data-ttu-id="4a997-218">lineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="4a997-218">lineString.EndPoint</span></span>                         | <span data-ttu-id="4a997-219">Punto de conexión ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4a997-219">EndPoint(@lineString)</span></span>
<span data-ttu-id="4a997-220">lineString. GetPointN (n)</span><span class="sxs-lookup"><span data-stu-id="4a997-220">lineString.GetPointN(n)</span></span>                     | <span data-ttu-id="4a997-221">PointN ( @lineString , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="4a997-221">PointN(@lineString, @n + 1)</span></span>
<span data-ttu-id="4a997-222">lineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="4a997-222">lineString.IsClosed</span></span>                         | <span data-ttu-id="4a997-223">IsClosed ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4a997-223">IsClosed(@lineString)</span></span>
<span data-ttu-id="4a997-224">lineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="4a997-224">lineString.IsRing</span></span>                           | <span data-ttu-id="4a997-225">IsRing ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4a997-225">IsRing(@lineString)</span></span>
<span data-ttu-id="4a997-226">lineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="4a997-226">lineString.StartPoint</span></span>                       | <span data-ttu-id="4a997-227">StartPoint ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4a997-227">StartPoint(@lineString)</span></span>
<span data-ttu-id="4a997-228">multiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="4a997-228">multiLineString.IsClosed</span></span>                    | <span data-ttu-id="4a997-229">IsClosed ( @multiLineString )</span><span class="sxs-lookup"><span data-stu-id="4a997-229">IsClosed(@multiLineString)</span></span>
<span data-ttu-id="4a997-230">Elija. F</span><span class="sxs-lookup"><span data-stu-id="4a997-230">point.M</span></span>                                     | <span data-ttu-id="4a997-231">M ( @point )</span><span class="sxs-lookup"><span data-stu-id="4a997-231">M(@point)</span></span>
<span data-ttu-id="4a997-232">Elija. X1</span><span class="sxs-lookup"><span data-stu-id="4a997-232">point.X</span></span>                                     | <span data-ttu-id="4a997-233">X ( @point )</span><span class="sxs-lookup"><span data-stu-id="4a997-233">X(@point)</span></span>
<span data-ttu-id="4a997-234">Elija. Sí</span><span class="sxs-lookup"><span data-stu-id="4a997-234">point.Y</span></span>                                     | <span data-ttu-id="4a997-235">Y ( @point )</span><span class="sxs-lookup"><span data-stu-id="4a997-235">Y(@point)</span></span>
<span data-ttu-id="4a997-236">Elija. Z</span><span class="sxs-lookup"><span data-stu-id="4a997-236">point.Z</span></span>                                     | <span data-ttu-id="4a997-237">Z ( @point )</span><span class="sxs-lookup"><span data-stu-id="4a997-237">Z(@point)</span></span>
<span data-ttu-id="4a997-238">Polígono. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="4a997-238">polygon.ExteriorRing</span></span>                        | <span data-ttu-id="4a997-239">ExteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="4a997-239">ExteriorRing(@polygon)</span></span>
<span data-ttu-id="4a997-240">Polígono. GetInteriorRingN (n)</span><span class="sxs-lookup"><span data-stu-id="4a997-240">polygon.GetInteriorRingN(n)</span></span>                 | <span data-ttu-id="4a997-241">InteriorRingN ( @polygon , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="4a997-241">InteriorRingN(@polygon, @n + 1)</span></span>
<span data-ttu-id="4a997-242">Polígono. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="4a997-242">polygon.NumInteriorRings</span></span>                    | <span data-ttu-id="4a997-243">NumInteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="4a997-243">NumInteriorRing(@polygon)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a997-244">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4a997-244">Additional resources</span></span>

* [<span data-ttu-id="4a997-245">Página principal de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="4a997-245">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="4a997-246">Documentos de NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="4a997-246">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
