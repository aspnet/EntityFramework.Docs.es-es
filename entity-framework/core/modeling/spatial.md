---
title: 'Datos espaciales: EF Core'
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 2222df84be7bfde3f252766bef1cfab39b476efa
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370453"
---
# <a name="spatial-data"></a>Datos espaciales

> [!NOTE]
> Esta característica se agregó en EF Core 2,2.

Los datos espaciales representan la ubicación física y la forma de los objetos. Muchas bases de datos proporcionan compatibilidad con este tipo de datos, por lo que se puede indizar y consultar junto con otros datos. Entre los escenarios comunes se incluyen las consultas de objetos dentro de una distancia determinada desde una ubicación o la selección del objeto cuyo borde contiene una ubicación determinada. EF Core admite la asignación a tipos de datos espaciales mediante la biblioteca espacial [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .

## <a name="installing"></a>Instalación

Para usar los datos espaciales con EF Core, debe instalar el paquete NuGet de soporte adecuado. El paquete que necesita instalar depende del proveedor que esté usando.

Proveedor de EF Core                        | Paquete de NuGet espacial
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. EntityFrameworkCore. SQLite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>Ingeniería inversa

Los paquetes de NuGet espaciales también habilitan los modelos de [ingeniería inversa](../managing-schemas/scaffolding.md) con propiedades espaciales, pero debe instalar el paquete ***antes*** de ejecutar `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` . Si no lo hace, recibirá advertencias sobre cómo no encontrar las asignaciones de tipos para las columnas y se omitirán las columnas.

## <a name="nettopologysuite-nts"></a>NetTopologySuite (NTS)

NetTopologySuite es una biblioteca espacial para .NET. EF Core permite la asignación a tipos de datos espaciales en la base de datos mediante el uso de tipos NTS en el modelo.

Para habilitar la asignación a tipos espaciales a través de NTS, llame al método UseNetTopologySuite en el generador de opciones DbContext del proveedor. Por ejemplo, con SQL Server le llamaría como esto.

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

Hay varios tipos de datos espaciales. El tipo que use dependerá de los tipos de formas que desee permitir. Esta es la jerarquía de tipos NTS que puede usar para las propiedades del modelo. Están ubicados en el `NetTopologySuite.Geometries` espacio de nombres.

* Geometría
  * Punto
  * LineString
  * Polígono
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> La CircularString, CompoundCurve y CurePolygon no son compatibles con NTS.

El uso del tipo de geometría base permite que la propiedad especifique cualquier tipo de forma.

Las siguientes clases de entidad se pueden usar para asignar tablas en la [base de datos de ejemplo Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630).

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a>Crear valores

Puede usar constructores para crear objetos Geometry; sin embargo, NTS recomienda el uso de un generador de geometría en su lugar. Esto le permite especificar un valor predeterminado de SRID (el sistema de referencia espacial que usan las coordenadas) y le proporciona el control sobre aspectos más avanzados, como el modelo de precisión (usado durante los cálculos) y la secuencia de coordenadas (determina qué coordenadas--dimensiones y medidas--están disponibles).

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> 4326 hace referencia a WGS 84, un estándar que se usa en GPS y en otros sistemas geográficos.

### <a name="longitude-and-latitude"></a>Longitud y latitud

Las coordenadas en NTS están en términos de valores X e y. Para representar la longitud y la latitud, use X para longitud e y para latitud. Tenga en cuenta que esto es **hacia atrás** desde el `latitude, longitude` formato en el que normalmente se ven estos valores.

### <a name="srid-ignored-during-client-operations"></a>SRID omitido durante las operaciones de cliente

NTS omite los valores de SRID durante las operaciones. Supone un sistema de coordenadas plano. Esto significa que si especifica coordenadas en términos de longitud y latitud, algunos valores evaluados por el cliente como la distancia, la longitud y el área estarán en grados, no en metros. Para valores más significativos, primero debe proyectar las coordenadas en otro sistema de coordenadas mediante una biblioteca como [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) antes de calcular estos valores.

Si una operación es evaluada por el servidor mediante EF Core a través de SQL, la unidad del resultado se determinará por la base de datos.

Este es un ejemplo del uso de ProjNet4GeoAPI para calcular la distancia entre dos ciudades.

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>Consulta de datos

En LINQ, los métodos y las propiedades NTS disponibles como funciones de base de datos se traducirán a SQL. Por ejemplo, los métodos Distance y Contains se traducen en las siguientes consultas. En la tabla al final de este artículo se muestran los miembros que son compatibles con varios proveedores de EF Core.

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

Si usa SQL Server, hay algunos aspectos adicionales que debe tener en cuenta.

### <a name="geography-or-geometry"></a>Geografía o geometría

De forma predeterminada, las propiedades espaciales se asignan a `geography` las columnas de SQL Server. Para usar `geometry` , [Configure el tipo de columna](xref:core/modeling/entity-properties#column-data-types) en el modelo.

### <a name="geography-polygon-rings"></a>Anillos de polígono de geografía

Al usar el `geography` tipo de columna, SQL Server impone requisitos adicionales en el anillo exterior (o shell) y los anillos interiores (o agujeros). El anillo exterior debe estar orientado en sentido contrario a las agujas del reloj y los anillos interiores hacia la derecha. NTS valida esto antes de enviar los valores a la base de datos.

### <a name="fullglobe"></a>FullGlobe

SQL Server tiene un tipo de geometría no estándar para representar todo el globo terráqueo cuando se usa el `geography` tipo de columna. También tiene una forma de representar polígonos basados en el globo completo (sin un anillo exterior). Ninguno de ellos es compatible con NTS.

> [!WARNING]
> Los FullGlobe y los polígonos basados en ellos no son compatibles con NTS.

## <a name="sqlite"></a>SQLite

A continuación se muestra información adicional para los usuarios que usan SQLite.

### <a name="installing-spatialite"></a>Instalación de SpatiaLite

En Windows, la biblioteca de mod_spatialite nativa se distribuye como una dependencia del paquete NuGet. Otras plataformas deben instalarse por separado. Esto se suele hacer mediante un administrador de paquetes de software. Por ejemplo, puede usar APT en Ubuntu y homebrew en MacOS.

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Desafortunadamente, las versiones más recientes de PROJ (una dependencia de SpatiaLite) son incompatibles con el [paquete SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predeterminado de EF. Para solucionar este fin, puede crear un proveedor de [SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personalizado que use la biblioteca de SQLite del sistema, o bien puede instalar una compilación personalizada de SpatiaLite deshabilitar la compatibilidad con proj.

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a>Configuración de SRID

En SpatiaLite, las columnas deben especificar un SRID por columna. El valor predeterminado de SRID es `0` . Especifique otro SRID con el método ForSqliteHasSrid.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>Dimensión

De forma similar a SRID, la dimensión de una columna (o las ordenadas) también se especifica como parte de la columna. Las ordenadas predeterminadas son X e y. Habilite las ordenadas adicionales (Z y M) mediante el método ForSqliteHasDimension.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>Operaciones traducidas

En esta tabla se muestran los miembros de NTS que cada proveedor de EF Core traduce en SQL.

NetTopologySuite | SQL Server (geometría) | SQL Server (Geografía) | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry. Area | ✔ | ✔ | ✔ | ✔
Geometry. AsBinary () | ✔ | ✔ | ✔ | ✔
Geometry. astext () | ✔ | ✔ | ✔ | ✔
Geometry. Boundary | ✔ | | ✔ | ✔
Geometry. Buffer (Double) | ✔ | ✔ | ✔ | ✔
Geometry. Buffer (Double, int) | | | ✔ | ✔
Geometry. centroide | ✔ | | ✔ | ✔
Geometry. Contains (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ConvexHull () | ✔ | ✔ | ✔ | ✔
Geometry. CoveredBy (Geometry) | | | ✔ | ✔
Geometry. cubiertas (Geometry) | | | ✔ | ✔
Geometry. Crosses (Geometry) | ✔ | | ✔ | ✔
Geometry. Difference (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Dimension | ✔ | ✔ | ✔ | ✔
Geometry. disunion (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Distance (Geometry) | ✔ | ✔ | ✔ | ✔
Geometría. sobre | ✔ | | ✔ | ✔
Geometry. EqualsExact (Geometry) | | | | ✔
Geometry. EqualsTopologically (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. GeometryType | ✔ | ✔ | ✔ | ✔
Geometry. GetGeometryN (int) | ✔ | | ✔ | ✔
Geometry. InteriorPoint | ✔ | | ✔ | ✔
Geometry. Intersection (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Intersects (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry. IsSimple | ✔ | | ✔ | ✔
Geometry. IsValid | ✔ | ✔ | ✔ | ✔
Geometry. IsWithinDistance (Geometry, Double) | ✔ | | ✔ | ✔
Geometry. length | ✔ | ✔ | ✔ | ✔
Geometry. NumGeometries | ✔ | ✔ | ✔ | ✔
Geometry. NumPoints | ✔ | ✔ | ✔ | ✔
Geometry. OgcGeometryType | ✔ | ✔ | ✔ | ✔
Geometry. superpone (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. PointOnSurface | ✔ | | ✔ | ✔
Geometry. Relate (Geometry, String) | ✔ | | ✔ | ✔
Geometry. Reverse () | | | ✔ | ✔
Geometry. SRID | ✔ | ✔ | ✔ | ✔
Geometry. SymmetricDifference (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ToBinary () | ✔ | ✔ | ✔ | ✔
Geometry. ToText () | ✔ | ✔ | ✔ | ✔
Geometry. toques (Geometry) | ✔ | | ✔ | ✔
Geometry. Union () | | | ✔ | ✔
Geometry. Union (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Within (Geometry) | ✔ | ✔ | ✔ | ✔
GeometryCollection. Count | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString. Count | ✔ | ✔ | ✔ | ✔
LineString. EndPoint | ✔ | ✔ | ✔ | ✔
LineString. GetPointN (int) | ✔ | ✔ | ✔ | ✔
LineString. IsClosed | ✔ | ✔ | ✔ | ✔
LineString. IsRing | ✔ | | ✔ | ✔
LineString. StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString. IsClosed | ✔ | ✔ | ✔ | ✔
Punto. M | ✔ | ✔ | ✔ | ✔
Point. X | ✔ | ✔ | ✔ | ✔
Punto. Y | ✔ | ✔ | ✔ | ✔
Punto. Z | ✔ | ✔ | ✔ | ✔
Polygon. ExteriorRing | ✔ | ✔ | ✔ | ✔
Polygon. GetInteriorRingN (int) | ✔ | ✔ | ✔ | ✔
Polygon. NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>Recursos adicionales

* [Datos espaciales de SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Página principal de SpatiaLite](https://www.gaia-gis.it/fossil/libspatialite)
* [Documentación espacial Npgsql](https://www.npgsql.org/efcore/mapping/nts.html)
* [Documentación de PostGIS](https://postgis.net/documentation/)
