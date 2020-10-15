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
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a>Datos espaciales en el proveedor de EF Core de SQLite

Esta página incluye información adicional sobre el uso de datos espaciales con el proveedor de base de datos de SQLite. Para obtener información general sobre el uso de datos espaciales en EF Core, consulte la documentación principal de los [datos espaciales](xref:core/modeling/spatial) .

## <a name="installing-spatialite"></a>Instalación de SpatiaLite

En Windows, la biblioteca de mod_spatialite nativa se distribuye como una dependencia del paquete NuGet. Otras plataformas deben instalarse por separado. Esto se suele hacer mediante un administrador de paquetes de software. Por ejemplo, puede usar APT en Debian y Ubuntu; y homebrew en MacOS.

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Desafortunadamente, las versiones más recientes de PROJ (una dependencia de SpatiaLite) son incompatibles con el [paquete SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predeterminado de EF. Puede solucionar este fin mediante el uso de la biblioteca de SQLite del sistema en su lugar.

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

En **MacOS**, también necesitará establecer una variable de entorno antes de ejecutar la aplicación para que use la versión de homebrew de SQLite. En Visual Studio para Mac, puede establecer esta opción en **project > Project options > Run >** configurations > default

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a>Configuración de SRID

En SpatiaLite, las columnas deben especificar un SRID por columna. El valor predeterminado de SRID es `0` . Especifique otro SRID con el método ForSqliteHasSrid.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> 4326 hace referencia a WGS 84, un estándar que se usa en GPS y en otros sistemas geográficos.

## <a name="dimension"></a>Dimensión

La dimensión predeterminada (o ordenadas) de una columna es X e y. Para habilitar otras coordenadas como Z o M, configure el tipo de columna.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a>Asignaciones de funciones espaciales

En esta tabla se muestran los miembros de [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) que se traducen en las funciones de SQL.

.NET                                        | SQL
------------------------------------------- | ---
Geometry. Áreas                               | Área ( @geometry )
Geometry. AsBinary ()                         | AsBinary ( @geometry )
Geometry. Astext ()                           | Astext ( @geometry )
Geometry. Exceso                           | Límite ( @geometry )
Geometry. Búfer (distancia)                   | Búfer ( @geometry , @distance )
Geometry. Búfer (distancia, quadrantSegments) | Búfer ( @geometry , @distance , @quadrantSegments )
Geometry. Centroide                           | Centroide ( @geometry )
Geometry. Contains (g)                        | Contains ( @geometry , @g )
Geometry. ConvexHull()                       | ConvexHull ( @geometry )
Geometry. CoveredBy (g)                       | CoveredBy ( @geometry , @g )
Geometry. Portadas (g)                          | Portadas ( @geometry , @g )
Geometry. Cruza (g)                         | Cruza ( @geometry , @g )
Geometry. Diferencia (otros)                  | Diferencia ( @geometry , @other )
Geometry. Galería                          | Dimension ( @geometry )
Geometry. Disunion (g)                        | Disjuntos ( @geometry , @g )
Geometry. Distancia (g)                        | Distancia ( @geometry , @g )
Geometry. Tales                           | Sobre ( @geometry )
Geometry. EqualsTopologically (g)             | Es igual a ( @geometry , @g )
Geometry. GeometryType                       | GeometryType ( @geometry )
Geometry. GetGeometryN (n)                    | Geometryn ( @geometry , @n + 1)
Geometry. InteriorPoint                      | PointOnSurface ( @geometry )
Geometry. Intersección (otro)                | Intersección ( @geometry , @other )
Geometry. Interseccións (g)                      | Interseccións ( @geometry , @g )
Geometry. Vacío                            | IsEmpty ( @geometry )
Geometry. IsSimple                           | IsSimple ( @geometry )
Geometry. IsValid                            | IsValid ( @geometry )
Geometry. IsWithinDistance (Geom, Distance)   | Distance ( @geometry , @geom ) <= @distance
Geometry. Longitud                             | GLength ( @geometry )
Geometry. NumGeometries                      | NumGeometries ( @geometry )
Geometry. NumPoints                          | NumPoints ( @geometry )
Geometry. OgcGeometryType                    | CASE GeometryType ( @geometry ) cuando ' Point ' then 1... EXTREMO
Geometry. Superposiciones (g)                        | Solapamientos ( @geometry , @g )
Geometry. PointOnSurface                     | PointOnSurface ( @geometry )
Geometry. Relacionar (g, intersectionPattern)     | Relacionar ( @geometry , @g , @intersectionPattern )
Geometry. REVERSE ()                          | ST_Reverse ( @geometry )
Geometry. SRID                               | SRID ( @geometry )
Geometry. SymmetricDifference (otro)         | SymDifference ( @geometry , @other )
Geometry. ToBinary()                         | AsBinary ( @geometry )
Geometry. ToText()                           | Astext ( @geometry )
Geometry. Toques (g)                         | Toques ( @geometry , @g )
Geometry. Unión ()                            | UnaryUnion ( @geometry )
Geometry. Unión (otro)                       | GUnion ( @geometry , @other )
Geometry. Dentro de (g)                          | Dentro de ( @geometry , @g )
geometryCollection [i]                       | Geometryn ( @geometryCollection , @i + 1)
geometryCollection. Count                    | NumGeometries ( @geometryCollection )
lineString. Count                            | NumPoints ( @lineString )
lineString. EndPoint                         | Punto de conexión ( @lineString )
lineString. GetPointN (n)                     | PointN ( @lineString , @n + 1)
lineString. IsClosed                         | IsClosed ( @lineString )
lineString. IsRing                           | IsRing ( @lineString )
lineString. StartPoint                       | StartPoint ( @lineString )
multiLineString. IsClosed                    | IsClosed ( @multiLineString )
Elija. F                                     | M ( @point )
Elija. X1                                     | X ( @point )
Elija. Sí                                     | Y ( @point )
Elija. Z                                     | Z ( @point )
Polígono. ExteriorRing                        | ExteriorRing ( @polygon )
Polígono. GetInteriorRingN (n)                 | InteriorRingN ( @polygon , @n + 1)
Polígono. NumInteriorRings                    | NumInteriorRing ( @polygon )

## <a name="additional-resources"></a>Recursos adicionales

* [Página principal de SpatiaLite](https://www.gaia-gis.it/fossil/libspatialite)
* [Documentos de NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
