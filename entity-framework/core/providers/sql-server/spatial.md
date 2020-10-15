---
title: 'Proveedor de base de datos Microsoft SQL Server: datos espaciales-EF Core'
description: Usar datos espaciales con el proveedor de bases de datos de Microsoft SQL Server de Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sql-server/spatial
ms.openlocfilehash: 1356d2d86a497f01c4eacca777a8a260f33c0e9b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066572"
---
# <a name="spatial-data-in-the-sql-server-ef-core-provider"></a>Datos espaciales en el proveedor de EF Core de SQL Server

Esta página incluye información adicional sobre el uso de datos espaciales con el proveedor de base de datos de Microsoft SQL Server. Para obtener información general sobre el uso de datos espaciales en EF Core, consulte la documentación principal de los [datos espaciales](xref:core/modeling/spatial) .

## <a name="geography-or-geometry"></a>Geografía o geometría

De forma predeterminada, las propiedades espaciales se asignan a `geography` las columnas de SQL Server. Para usar `geometry` , [Configure el tipo de columna](xref:core/modeling/entity-properties#column-data-types) en el modelo.

## <a name="geography-polygon-rings"></a>Anillos de polígono de geografía

Al usar el `geography` tipo de columna, SQL Server impone requisitos adicionales en el anillo exterior (o shell) y los anillos interiores (o agujeros). El anillo exterior debe estar orientado en sentido contrario a las agujas del reloj y los anillos interiores hacia la derecha. [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) valida esto antes de enviar los valores a la base de datos.

## <a name="fullglobe"></a>FullGlobe

SQL Server tiene un tipo de geometría no estándar para representar todo el globo terráqueo cuando se usa el `geography` tipo de columna. También tiene una forma de representar polígonos basados en el globo completo (sin un anillo exterior). Ninguno de ellos es compatible con NTS.

> [!WARNING]
> Los FullGlobe y los polígonos basados en ellos no son compatibles con NTS.

## <a name="curves"></a>Curvas

Como se mencionó en la documentación principal de los [datos espaciales](xref:core/modeling/spatial) , NTS actualmente no puede representar curvas. Esto significa que deberá transformar los valores CircularString, CompoundCurve y CurePolygon con el método [STCurveToLine](/sql/t-sql/spatial-geography/stcurvetoline-geography-data-type) antes de usarlos en EF Core.

> [!WARNING]
> La CircularString, CompoundCurve y CurePolygon no son compatibles con NTS.

## <a name="spatial-function-mappings"></a>Asignaciones de funciones espaciales

En esta tabla se muestran los miembros de NTS que se traducen en las funciones de SQL. Tenga en cuenta que las traducciones varían en función de si la columna es de tipo Geography o Geometry.

.NET                                      | SQL (Geografía)                                              | SQL (geometría)
----------------------------------------- | ------------------------------------------------------------ | --------------
Geometry. Áreas                             | @geometry.STArea()                                           | @geometry.STArea()
Geometry. AsBinary ()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
Geometry. Astext ()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
Geometry. Exceso                         |                                                              | @geometry.STBoundary()
Geometry. Búfer (distancia)                 | @geometry.STBuffer(@distance)                                | @geometry.STBuffer(@distance)
Geometry. Centroide                         |                                                              | @geometry.STCentroid()
Geometry. Contains (g)                      | @geometry.STContains(@g)                                     | @geometry.STContains(@g)
Geometry. ConvexHull()                     | @geometry.STConvexHull()                                     | @geometry.STConvexHull()
Geometry. Cruza (g)                       |                                                              | @geometry.STCrosses(@g)
Geometry. Diferencia (otros)                | @geometry.STDifference(@other)                               | @geometry.STDifference(@other)
Geometry. Galería                        | @geometry.STDimension()                                      | @geometry.STDimension()
Geometry. Disunion (g)                      | @geometry.STDisjoint(@g)                                     | @geometry.STDisjoint(@g)
Geometry. Distancia (g)                      | @geometry.STDistance(@g)                                     | @geometry.STDistance(@g)
Geometry. Tales                         |                                                              | @geometry.STEnvelope()
Geometry. EqualsTopologically (g)           | @geometry.STEquals(@g)                                       | @geometry.STEquals(@g)
Geometry. GeometryType                     | @geometry.STGeometryType()                                   | @geometry.STGeometryType()
Geometry. GetGeometryN (n)                  | @geometry.STGeometryN( @n + 1)                                | @geometry.STGeometryN( @n + 1)
Geometry. InteriorPoint                    |                                                              | @geometry.STPointOnSurface()
Geometry. Intersección (otro)              | @geometry.STIntersection(@other)                             | @geometry.STIntersection(@other)
Geometry. Interseccións (g)                    | @geometry.STIntersects(@g)                                   | @geometry.STIntersects(@g)
Geometry. Vacío                          | @geometry.STIsEmpty()                                        | @geometry.STIsEmpty()
Geometry. IsSimple                         |                                                              | @geometry.STIsSimple()
Geometry. IsValid                          | @geometry.STIsValid()                                        | @geometry.STIsValid()
Geometry. IsWithinDistance (Geom, Distance) | @geometry.STDistance( @geom ) <= @distance                     | @geometry.STDistance( @geom ) <= @distance
Geometry. Longitud                           | @geometry.STLength()                                         | @geometry.STLength()
Geometry. NumGeometries                    | @geometry.STNumGeometries()                                  | @geometry.STNumGeometries()
Geometry. NumPoints                        | @geometry.STNumPoints()                                      | @geometry.STNumPoints()
Geometry. OgcGeometryType                  | CASE @geometry.STGeometryType () cuando N'Point ' then 1... EXTREMO | CASE @geometry.STGeometryType () cuando N'Point ' then 1... EXTREMO
Geometry. Superposiciones (g)                      | @geometry.STOverlaps(@g)                                     | @geometry.STOverlaps(@g)
Geometry. PointOnSurface                   |                                                              | @geometry.STPointOnSurface()
Geometry. Relacionar (g, intersectionPattern)   |                                                              | @geometry.STRelate(@g, @intersectionPattern)
Geometry. SRID                             | @geometry.STSrid                                             | @geometry.STSrid
Geometry. SymmetricDifference (otro)       | @geometry.STSymDifference(@other)                            | @geometry.STSymDifference(@other)
Geometry. ToBinary()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
Geometry. ToText()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
Geometry. Toques (g)                       |                                                              | @geometry.STTouches(@g)
Geometry. Unión (otro)                     | @geometry.STUnion(@other)                                    | @geometry.STUnion(@other)
Geometry. Dentro de (g)                        | @geometry.STWithin(@g)                                       | @geometry.STWithin(@g)
geometryCollection [i]                     | @geometryCollection.STGeometryN( @i + 1)                      | @geometryCollection.STGeometryN( @i + 1)
geometryCollection. Count                  | @geometryCollection.STNumGeometries()                        | @geometryCollection.STNumGeometries()
lineString. Count                          | @lineString.STNumPoints()                                    | @lineString.STNumPoints()
lineString. EndPoint                       | @lineString.STEndPoint()                                     | @lineString.STEndPoint()
lineString. GetPointN (n)                   | @lineString.STPointN( @n + 1)                                 | @lineString.STPointN( @n + 1)
lineString. IsClosed                       | @lineString.STIsClosed()                                     | @lineString.STIsClosed()
lineString. IsRing                         |                                                              | @lineString.IsRing()
lineString. StartPoint                     | @lineString.STStartPoint()                                   | @lineString.STStartPoint()
multiLineString. IsClosed                  | @multiLineString.STIsClosed()                                | @multiLineString.STIsClosed()
Elija. F                                   | @point.M                                                     | @point.M
Elija. X1                                   | @point.Long                                                  | @point.STX
Elija. Sí                                   | @point.Lat                                                   | @point.STY
Elija. Z                                   | @point.Z                                                     | @point.Z
Polígono. ExteriorRing                      | @polygon.RingNdimensional                                            | @polygon.STExteriorRing()
Polígono. GetInteriorRingN (n)               | @polygon.RingN( @n + 2)                                       | @polygon.STInteriorRingN( @n + 1)
Polígono. NumInteriorRings                  | @polygon.NumRings()-1                                      | @polygon.STNumInteriorRing()

## <a name="additional-resources"></a>Recursos adicionales

* [Datos espaciales de SQL Server](/sql/relational-databases/spatial/spatial-data-sql-server)
* [Documentos de NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
