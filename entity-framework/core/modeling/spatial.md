---
title: 'Datos espaciales: EF Core'
description: Usar datos espaciales en un modelo de Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 42386fb132f135d725a2068d91dc49c7f613e277
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616694"
---
# <a name="spatial-data"></a><span data-ttu-id="7e223-103">Datos espaciales</span><span class="sxs-lookup"><span data-stu-id="7e223-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="7e223-104">Esta característica se agregó en EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="7e223-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="7e223-105">Los datos espaciales representan la ubicación física y la forma de los objetos.</span><span class="sxs-lookup"><span data-stu-id="7e223-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="7e223-106">Muchas bases de datos proporcionan compatibilidad con este tipo de datos, por lo que se puede indizar y consultar junto con otros datos.</span><span class="sxs-lookup"><span data-stu-id="7e223-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="7e223-107">Entre los escenarios comunes se incluyen las consultas de objetos dentro de una distancia determinada desde una ubicación o la selección del objeto cuyo borde contiene una ubicación determinada.</span><span class="sxs-lookup"><span data-stu-id="7e223-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="7e223-108">EF Core admite la asignación a tipos de datos espaciales mediante la biblioteca espacial [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="7e223-108">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="7e223-109">Instalación de</span><span class="sxs-lookup"><span data-stu-id="7e223-109">Installing</span></span>

<span data-ttu-id="7e223-110">Para usar los datos espaciales con EF Core, debe instalar el paquete NuGet de soporte adecuado.</span><span class="sxs-lookup"><span data-stu-id="7e223-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="7e223-111">El paquete que necesita instalar depende del proveedor que esté usando.</span><span class="sxs-lookup"><span data-stu-id="7e223-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="7e223-112">Proveedor de EF Core</span><span class="sxs-lookup"><span data-stu-id="7e223-112">EF Core Provider</span></span>                        | <span data-ttu-id="7e223-113">Paquete de NuGet espacial</span><span class="sxs-lookup"><span data-stu-id="7e223-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="7e223-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7e223-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="7e223-115">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7e223-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="7e223-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7e223-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="7e223-117">Microsoft. EntityFrameworkCore. SQLite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7e223-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="7e223-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="7e223-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="7e223-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7e223-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="7e223-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="7e223-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="7e223-121">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7e223-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="7e223-122">Ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="7e223-122">Reverse engineering</span></span>

<span data-ttu-id="7e223-123">Los paquetes de NuGet espaciales también habilitan los modelos de [ingeniería inversa](xref:core/managing-schemas/scaffolding) con propiedades espaciales, pero debe instalar el paquete ***antes*** de ejecutar `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="7e223-123">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="7e223-124">Si no lo hace, recibirá advertencias sobre cómo no encontrar las asignaciones de tipos para las columnas y se omitirán las columnas.</span><span class="sxs-lookup"><span data-stu-id="7e223-124">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="7e223-125">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="7e223-125">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="7e223-126">NetTopologySuite es una biblioteca espacial para .NET.</span><span class="sxs-lookup"><span data-stu-id="7e223-126">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="7e223-127">EF Core permite la asignación a tipos de datos espaciales en la base de datos mediante el uso de tipos NTS en el modelo.</span><span class="sxs-lookup"><span data-stu-id="7e223-127">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="7e223-128">Para habilitar la asignación a tipos espaciales a través de NTS, llame al método UseNetTopologySuite en el generador de opciones DbContext del proveedor.</span><span class="sxs-lookup"><span data-stu-id="7e223-128">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="7e223-129">Por ejemplo, con SQL Server le llamaría como esto.</span><span class="sxs-lookup"><span data-stu-id="7e223-129">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="7e223-130">Hay varios tipos de datos espaciales.</span><span class="sxs-lookup"><span data-stu-id="7e223-130">There are several spatial data types.</span></span> <span data-ttu-id="7e223-131">El tipo que use dependerá de los tipos de formas que desee permitir.</span><span class="sxs-lookup"><span data-stu-id="7e223-131">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="7e223-132">Esta es la jerarquía de tipos NTS que puede usar para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="7e223-132">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="7e223-133">Están ubicados en el `NetTopologySuite.Geometries` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="7e223-133">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="7e223-134">Geometría</span><span class="sxs-lookup"><span data-stu-id="7e223-134">Geometry</span></span>
  * <span data-ttu-id="7e223-135">Punto</span><span class="sxs-lookup"><span data-stu-id="7e223-135">Point</span></span>
  * <span data-ttu-id="7e223-136">LineString</span><span class="sxs-lookup"><span data-stu-id="7e223-136">LineString</span></span>
  * <span data-ttu-id="7e223-137">Polygon</span><span class="sxs-lookup"><span data-stu-id="7e223-137">Polygon</span></span>
  * <span data-ttu-id="7e223-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="7e223-138">GeometryCollection</span></span>
    * <span data-ttu-id="7e223-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="7e223-139">MultiPoint</span></span>
    * <span data-ttu-id="7e223-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="7e223-140">MultiLineString</span></span>
    * <span data-ttu-id="7e223-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="7e223-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="7e223-142">La CircularString, CompoundCurve y CurePolygon no son compatibles con NTS.</span><span class="sxs-lookup"><span data-stu-id="7e223-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="7e223-143">El uso del tipo de geometría base permite que la propiedad especifique cualquier tipo de forma.</span><span class="sxs-lookup"><span data-stu-id="7e223-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="7e223-144">Las siguientes clases de entidad se pueden usar para asignar tablas en la [base de datos de ejemplo Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630).</span><span class="sxs-lookup"><span data-stu-id="7e223-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="7e223-145">Crear valores</span><span class="sxs-lookup"><span data-stu-id="7e223-145">Creating values</span></span>

<span data-ttu-id="7e223-146">Puede usar constructores para crear objetos Geometry; sin embargo, NTS recomienda el uso de un generador de geometría en su lugar.</span><span class="sxs-lookup"><span data-stu-id="7e223-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="7e223-147">Esto le permite especificar un valor predeterminado de SRID (el sistema de referencia espacial que usan las coordenadas) y le proporciona el control sobre aspectos más avanzados, como el modelo de precisión (usado durante los cálculos) y la secuencia de coordenadas (determina qué coordenadas--dimensiones y medidas--están disponibles).</span><span class="sxs-lookup"><span data-stu-id="7e223-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="7e223-148">4326 hace referencia a WGS 84, un estándar que se usa en GPS y en otros sistemas geográficos.</span><span class="sxs-lookup"><span data-stu-id="7e223-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="7e223-149">Longitud y latitud</span><span class="sxs-lookup"><span data-stu-id="7e223-149">Longitude and Latitude</span></span>

<span data-ttu-id="7e223-150">Las coordenadas en NTS están en términos de valores X e y.</span><span class="sxs-lookup"><span data-stu-id="7e223-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="7e223-151">Para representar la longitud y la latitud, use X para longitud e y para latitud.</span><span class="sxs-lookup"><span data-stu-id="7e223-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="7e223-152">Tenga en cuenta que esto es **hacia atrás** desde el `latitude, longitude` formato en el que normalmente se ven estos valores.</span><span class="sxs-lookup"><span data-stu-id="7e223-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="7e223-153">SRID omitido durante las operaciones de cliente</span><span class="sxs-lookup"><span data-stu-id="7e223-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="7e223-154">NTS omite los valores de SRID durante las operaciones.</span><span class="sxs-lookup"><span data-stu-id="7e223-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="7e223-155">Supone un sistema de coordenadas plano.</span><span class="sxs-lookup"><span data-stu-id="7e223-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="7e223-156">Esto significa que si especifica coordenadas en términos de longitud y latitud, algunos valores evaluados por el cliente como la distancia, la longitud y el área estarán en grados, no en metros.</span><span class="sxs-lookup"><span data-stu-id="7e223-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="7e223-157">Para valores más significativos, primero debe proyectar las coordenadas en otro sistema de coordenadas mediante una biblioteca como [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) antes de calcular estos valores.</span><span class="sxs-lookup"><span data-stu-id="7e223-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="7e223-158">Si una operación es evaluada por el servidor mediante EF Core a través de SQL, la unidad del resultado se determinará por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7e223-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="7e223-159">Este es un ejemplo del uso de ProjNet4GeoAPI para calcular la distancia entre dos ciudades.</span><span class="sxs-lookup"><span data-stu-id="7e223-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="7e223-160">Consulta de datos</span><span class="sxs-lookup"><span data-stu-id="7e223-160">Querying Data</span></span>

<span data-ttu-id="7e223-161">En LINQ, los métodos y las propiedades NTS disponibles como funciones de base de datos se traducirán a SQL.</span><span class="sxs-lookup"><span data-stu-id="7e223-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="7e223-162">Por ejemplo, los métodos Distance y Contains se traducen en las siguientes consultas.</span><span class="sxs-lookup"><span data-stu-id="7e223-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="7e223-163">En la tabla al final de este artículo se muestran los miembros que son compatibles con varios proveedores de EF Core.</span><span class="sxs-lookup"><span data-stu-id="7e223-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="7e223-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="7e223-164">SQL Server</span></span>

<span data-ttu-id="7e223-165">Si usa SQL Server, hay algunos aspectos adicionales que debe tener en cuenta.</span><span class="sxs-lookup"><span data-stu-id="7e223-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="7e223-166">Geografía o geometría</span><span class="sxs-lookup"><span data-stu-id="7e223-166">Geography or geometry</span></span>

<span data-ttu-id="7e223-167">De forma predeterminada, las propiedades espaciales se asignan a `geography` las columnas de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7e223-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="7e223-168">Para usar `geometry` , [Configure el tipo de columna](xref:core/modeling/entity-properties#column-data-types) en el modelo.</span><span class="sxs-lookup"><span data-stu-id="7e223-168">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="7e223-169">Anillos de polígono de geografía</span><span class="sxs-lookup"><span data-stu-id="7e223-169">Geography polygon rings</span></span>

<span data-ttu-id="7e223-170">Al usar el `geography` tipo de columna, SQL Server impone requisitos adicionales en el anillo exterior (o shell) y los anillos interiores (o agujeros).</span><span class="sxs-lookup"><span data-stu-id="7e223-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="7e223-171">El anillo exterior debe estar orientado en sentido contrario a las agujas del reloj y los anillos interiores hacia la derecha.</span><span class="sxs-lookup"><span data-stu-id="7e223-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="7e223-172">NTS valida esto antes de enviar los valores a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7e223-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="7e223-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="7e223-173">FullGlobe</span></span>

<span data-ttu-id="7e223-174">SQL Server tiene un tipo de geometría no estándar para representar todo el globo terráqueo cuando se usa el `geography` tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="7e223-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="7e223-175">También tiene una forma de representar polígonos basados en el globo completo (sin un anillo exterior).</span><span class="sxs-lookup"><span data-stu-id="7e223-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="7e223-176">Ninguno de ellos es compatible con NTS.</span><span class="sxs-lookup"><span data-stu-id="7e223-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="7e223-177">Los FullGlobe y los polígonos basados en ellos no son compatibles con NTS.</span><span class="sxs-lookup"><span data-stu-id="7e223-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="7e223-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="7e223-178">SQLite</span></span>

<span data-ttu-id="7e223-179">A continuación se muestra información adicional para los usuarios que usan SQLite.</span><span class="sxs-lookup"><span data-stu-id="7e223-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="7e223-180">Instalación de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="7e223-180">Installing SpatiaLite</span></span>

<span data-ttu-id="7e223-181">En Windows, la biblioteca de mod_spatialite nativa se distribuye como una dependencia del paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="7e223-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="7e223-182">Otras plataformas deben instalarse por separado.</span><span class="sxs-lookup"><span data-stu-id="7e223-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="7e223-183">Esto se suele hacer mediante un administrador de paquetes de software.</span><span class="sxs-lookup"><span data-stu-id="7e223-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="7e223-184">Por ejemplo, puede usar APT en Ubuntu y homebrew en MacOS.</span><span class="sxs-lookup"><span data-stu-id="7e223-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="7e223-185">Desafortunadamente, las versiones más recientes de PROJ (una dependencia de SpatiaLite) son incompatibles con el [paquete SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predeterminado de EF.</span><span class="sxs-lookup"><span data-stu-id="7e223-185">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="7e223-186">Para solucionar este fin, puede crear un proveedor de [SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personalizado que use la biblioteca de SQLite del sistema, o bien puede instalar una compilación personalizada de SpatiaLite deshabilitar la compatibilidad con proj.</span><span class="sxs-lookup"><span data-stu-id="7e223-186">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="7e223-187">Configuración de SRID</span><span class="sxs-lookup"><span data-stu-id="7e223-187">Configuring SRID</span></span>

<span data-ttu-id="7e223-188">En SpatiaLite, las columnas deben especificar un SRID por columna.</span><span class="sxs-lookup"><span data-stu-id="7e223-188">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="7e223-189">El valor predeterminado de SRID es `0` .</span><span class="sxs-lookup"><span data-stu-id="7e223-189">The default SRID is `0`.</span></span> <span data-ttu-id="7e223-190">Especifique otro SRID con el método ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="7e223-190">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="7e223-191">Dimensión</span><span class="sxs-lookup"><span data-stu-id="7e223-191">Dimension</span></span>

<span data-ttu-id="7e223-192">De forma similar a SRID, la dimensión de una columna (o las ordenadas) también se especifica como parte de la columna.</span><span class="sxs-lookup"><span data-stu-id="7e223-192">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="7e223-193">Las ordenadas predeterminadas son X e y. Habilite las ordenadas adicionales (Z y M) mediante el método ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="7e223-193">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="7e223-194">Operaciones traducidas</span><span class="sxs-lookup"><span data-stu-id="7e223-194">Translated Operations</span></span>

<span data-ttu-id="7e223-195">En esta tabla se muestran los miembros de NTS que cada proveedor de EF Core traduce en SQL.</span><span class="sxs-lookup"><span data-stu-id="7e223-195">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="7e223-196">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7e223-196">NetTopologySuite</span></span> | <span data-ttu-id="7e223-197">SQL Server (geometría)</span><span class="sxs-lookup"><span data-stu-id="7e223-197">SQL Server (geometry)</span></span> | <span data-ttu-id="7e223-198">SQL Server (Geografía)</span><span class="sxs-lookup"><span data-stu-id="7e223-198">SQL Server (geography)</span></span> | <span data-ttu-id="7e223-199">SQLite</span><span class="sxs-lookup"><span data-stu-id="7e223-199">SQLite</span></span> | <span data-ttu-id="7e223-200">Npgsql</span><span class="sxs-lookup"><span data-stu-id="7e223-200">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="7e223-201">Geometry. Area</span><span class="sxs-lookup"><span data-stu-id="7e223-201">Geometry.Area</span></span> | <span data-ttu-id="7e223-202">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-202">✔</span></span> | <span data-ttu-id="7e223-203">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-203">✔</span></span> | <span data-ttu-id="7e223-204">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-204">✔</span></span> | <span data-ttu-id="7e223-205">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-205">✔</span></span>
<span data-ttu-id="7e223-206">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="7e223-206">Geometry.AsBinary()</span></span> | <span data-ttu-id="7e223-207">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-207">✔</span></span> | <span data-ttu-id="7e223-208">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-208">✔</span></span> | <span data-ttu-id="7e223-209">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-209">✔</span></span> | <span data-ttu-id="7e223-210">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-210">✔</span></span>
<span data-ttu-id="7e223-211">Geometry. astext ()</span><span class="sxs-lookup"><span data-stu-id="7e223-211">Geometry.AsText()</span></span> | <span data-ttu-id="7e223-212">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-212">✔</span></span> | <span data-ttu-id="7e223-213">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-213">✔</span></span> | <span data-ttu-id="7e223-214">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-214">✔</span></span> | <span data-ttu-id="7e223-215">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-215">✔</span></span>
<span data-ttu-id="7e223-216">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="7e223-216">Geometry.Boundary</span></span> | <span data-ttu-id="7e223-217">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-217">✔</span></span> | | <span data-ttu-id="7e223-218">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-218">✔</span></span> | <span data-ttu-id="7e223-219">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-219">✔</span></span>
<span data-ttu-id="7e223-220">Geometry. Buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="7e223-220">Geometry.Buffer(double)</span></span> | <span data-ttu-id="7e223-221">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-221">✔</span></span> | <span data-ttu-id="7e223-222">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-222">✔</span></span> | <span data-ttu-id="7e223-223">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-223">✔</span></span> | <span data-ttu-id="7e223-224">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-224">✔</span></span>
<span data-ttu-id="7e223-225">Geometry. Buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="7e223-225">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="7e223-226">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-226">✔</span></span> | <span data-ttu-id="7e223-227">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-227">✔</span></span>
<span data-ttu-id="7e223-228">Geometry. centroide</span><span class="sxs-lookup"><span data-stu-id="7e223-228">Geometry.Centroid</span></span> | <span data-ttu-id="7e223-229">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-229">✔</span></span> | | <span data-ttu-id="7e223-230">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-230">✔</span></span> | <span data-ttu-id="7e223-231">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-231">✔</span></span>
<span data-ttu-id="7e223-232">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-232">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="7e223-233">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-233">✔</span></span> | <span data-ttu-id="7e223-234">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-234">✔</span></span> | <span data-ttu-id="7e223-235">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-235">✔</span></span> | <span data-ttu-id="7e223-236">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-236">✔</span></span>
<span data-ttu-id="7e223-237">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="7e223-237">Geometry.ConvexHull()</span></span> | <span data-ttu-id="7e223-238">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-238">✔</span></span> | <span data-ttu-id="7e223-239">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-239">✔</span></span> | <span data-ttu-id="7e223-240">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-240">✔</span></span> | <span data-ttu-id="7e223-241">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-241">✔</span></span>
<span data-ttu-id="7e223-242">Geometry. CoveredBy (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-242">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="7e223-243">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-243">✔</span></span> | <span data-ttu-id="7e223-244">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-244">✔</span></span>
<span data-ttu-id="7e223-245">Geometry. cubiertas (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-245">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="7e223-246">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-246">✔</span></span> | <span data-ttu-id="7e223-247">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-247">✔</span></span>
<span data-ttu-id="7e223-248">Geometry. Crosses (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-248">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="7e223-249">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-249">✔</span></span> | | <span data-ttu-id="7e223-250">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-250">✔</span></span> | <span data-ttu-id="7e223-251">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-251">✔</span></span>
<span data-ttu-id="7e223-252">Geometry. Difference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-252">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="7e223-253">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-253">✔</span></span> | <span data-ttu-id="7e223-254">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-254">✔</span></span> | <span data-ttu-id="7e223-255">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-255">✔</span></span> | <span data-ttu-id="7e223-256">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-256">✔</span></span>
<span data-ttu-id="7e223-257">Geometry. Dimension</span><span class="sxs-lookup"><span data-stu-id="7e223-257">Geometry.Dimension</span></span> | <span data-ttu-id="7e223-258">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-258">✔</span></span> | <span data-ttu-id="7e223-259">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-259">✔</span></span> | <span data-ttu-id="7e223-260">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-260">✔</span></span> | <span data-ttu-id="7e223-261">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-261">✔</span></span>
<span data-ttu-id="7e223-262">Geometry. disunion (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-262">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="7e223-263">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-263">✔</span></span> | <span data-ttu-id="7e223-264">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-264">✔</span></span> | <span data-ttu-id="7e223-265">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-265">✔</span></span> | <span data-ttu-id="7e223-266">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-266">✔</span></span>
<span data-ttu-id="7e223-267">Geometry. Distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-267">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="7e223-268">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-268">✔</span></span> | <span data-ttu-id="7e223-269">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-269">✔</span></span> | <span data-ttu-id="7e223-270">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-270">✔</span></span> | <span data-ttu-id="7e223-271">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-271">✔</span></span>
<span data-ttu-id="7e223-272">Geometría. sobre</span><span class="sxs-lookup"><span data-stu-id="7e223-272">Geometry.Envelope</span></span> | <span data-ttu-id="7e223-273">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-273">✔</span></span> | | <span data-ttu-id="7e223-274">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-274">✔</span></span> | <span data-ttu-id="7e223-275">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-275">✔</span></span>
<span data-ttu-id="7e223-276">Geometry. EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-276">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="7e223-277">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-277">✔</span></span>
<span data-ttu-id="7e223-278">Geometry. EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-278">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="7e223-279">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-279">✔</span></span> | <span data-ttu-id="7e223-280">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-280">✔</span></span> | <span data-ttu-id="7e223-281">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-281">✔</span></span> | <span data-ttu-id="7e223-282">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-282">✔</span></span>
<span data-ttu-id="7e223-283">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="7e223-283">Geometry.GeometryType</span></span> | <span data-ttu-id="7e223-284">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-284">✔</span></span> | <span data-ttu-id="7e223-285">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-285">✔</span></span> | <span data-ttu-id="7e223-286">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-286">✔</span></span> | <span data-ttu-id="7e223-287">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-287">✔</span></span>
<span data-ttu-id="7e223-288">Geometry. GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="7e223-288">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="7e223-289">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-289">✔</span></span> | | <span data-ttu-id="7e223-290">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-290">✔</span></span> | <span data-ttu-id="7e223-291">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-291">✔</span></span>
<span data-ttu-id="7e223-292">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="7e223-292">Geometry.InteriorPoint</span></span> | <span data-ttu-id="7e223-293">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-293">✔</span></span> | | <span data-ttu-id="7e223-294">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-294">✔</span></span> | <span data-ttu-id="7e223-295">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-295">✔</span></span>
<span data-ttu-id="7e223-296">Geometry. Intersection (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-296">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="7e223-297">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-297">✔</span></span> | <span data-ttu-id="7e223-298">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-298">✔</span></span> | <span data-ttu-id="7e223-299">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-299">✔</span></span> | <span data-ttu-id="7e223-300">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-300">✔</span></span>
<span data-ttu-id="7e223-301">Geometry. Intersects (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-301">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="7e223-302">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-302">✔</span></span> | <span data-ttu-id="7e223-303">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-303">✔</span></span> | <span data-ttu-id="7e223-304">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-304">✔</span></span> | <span data-ttu-id="7e223-305">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-305">✔</span></span>
<span data-ttu-id="7e223-306">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="7e223-306">Geometry.IsEmpty</span></span> | <span data-ttu-id="7e223-307">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-307">✔</span></span> | <span data-ttu-id="7e223-308">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-308">✔</span></span> | <span data-ttu-id="7e223-309">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-309">✔</span></span> | <span data-ttu-id="7e223-310">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-310">✔</span></span>
<span data-ttu-id="7e223-311">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="7e223-311">Geometry.IsSimple</span></span> | <span data-ttu-id="7e223-312">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-312">✔</span></span> | | <span data-ttu-id="7e223-313">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-313">✔</span></span> | <span data-ttu-id="7e223-314">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-314">✔</span></span>
<span data-ttu-id="7e223-315">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="7e223-315">Geometry.IsValid</span></span> | <span data-ttu-id="7e223-316">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-316">✔</span></span> | <span data-ttu-id="7e223-317">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-317">✔</span></span> | <span data-ttu-id="7e223-318">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-318">✔</span></span> | <span data-ttu-id="7e223-319">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-319">✔</span></span>
<span data-ttu-id="7e223-320">Geometry. IsWithinDistance (Geometry, Double)</span><span class="sxs-lookup"><span data-stu-id="7e223-320">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="7e223-321">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-321">✔</span></span> | | <span data-ttu-id="7e223-322">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-322">✔</span></span> | <span data-ttu-id="7e223-323">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-323">✔</span></span>
<span data-ttu-id="7e223-324">Geometry. length</span><span class="sxs-lookup"><span data-stu-id="7e223-324">Geometry.Length</span></span> | <span data-ttu-id="7e223-325">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-325">✔</span></span> | <span data-ttu-id="7e223-326">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-326">✔</span></span> | <span data-ttu-id="7e223-327">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-327">✔</span></span> | <span data-ttu-id="7e223-328">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-328">✔</span></span>
<span data-ttu-id="7e223-329">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="7e223-329">Geometry.NumGeometries</span></span> | <span data-ttu-id="7e223-330">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-330">✔</span></span> | <span data-ttu-id="7e223-331">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-331">✔</span></span> | <span data-ttu-id="7e223-332">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-332">✔</span></span> | <span data-ttu-id="7e223-333">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-333">✔</span></span>
<span data-ttu-id="7e223-334">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="7e223-334">Geometry.NumPoints</span></span> | <span data-ttu-id="7e223-335">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-335">✔</span></span> | <span data-ttu-id="7e223-336">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-336">✔</span></span> | <span data-ttu-id="7e223-337">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-337">✔</span></span> | <span data-ttu-id="7e223-338">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-338">✔</span></span>
<span data-ttu-id="7e223-339">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="7e223-339">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="7e223-340">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-340">✔</span></span> | <span data-ttu-id="7e223-341">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-341">✔</span></span> | <span data-ttu-id="7e223-342">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-342">✔</span></span> | <span data-ttu-id="7e223-343">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-343">✔</span></span>
<span data-ttu-id="7e223-344">Geometry. superpone (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-344">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="7e223-345">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-345">✔</span></span> | <span data-ttu-id="7e223-346">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-346">✔</span></span> | <span data-ttu-id="7e223-347">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-347">✔</span></span> | <span data-ttu-id="7e223-348">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-348">✔</span></span>
<span data-ttu-id="7e223-349">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="7e223-349">Geometry.PointOnSurface</span></span> | <span data-ttu-id="7e223-350">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-350">✔</span></span> | | <span data-ttu-id="7e223-351">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-351">✔</span></span> | <span data-ttu-id="7e223-352">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-352">✔</span></span>
<span data-ttu-id="7e223-353">Geometry. Relate (Geometry, String)</span><span class="sxs-lookup"><span data-stu-id="7e223-353">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="7e223-354">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-354">✔</span></span> | | <span data-ttu-id="7e223-355">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-355">✔</span></span> | <span data-ttu-id="7e223-356">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-356">✔</span></span>
<span data-ttu-id="7e223-357">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="7e223-357">Geometry.Reverse()</span></span> | | | <span data-ttu-id="7e223-358">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-358">✔</span></span> | <span data-ttu-id="7e223-359">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-359">✔</span></span>
<span data-ttu-id="7e223-360">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="7e223-360">Geometry.SRID</span></span> | <span data-ttu-id="7e223-361">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-361">✔</span></span> | <span data-ttu-id="7e223-362">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-362">✔</span></span> | <span data-ttu-id="7e223-363">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-363">✔</span></span> | <span data-ttu-id="7e223-364">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-364">✔</span></span>
<span data-ttu-id="7e223-365">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-365">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="7e223-366">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-366">✔</span></span> | <span data-ttu-id="7e223-367">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-367">✔</span></span> | <span data-ttu-id="7e223-368">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-368">✔</span></span> | <span data-ttu-id="7e223-369">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-369">✔</span></span>
<span data-ttu-id="7e223-370">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="7e223-370">Geometry.ToBinary()</span></span> | <span data-ttu-id="7e223-371">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-371">✔</span></span> | <span data-ttu-id="7e223-372">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-372">✔</span></span> | <span data-ttu-id="7e223-373">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-373">✔</span></span> | <span data-ttu-id="7e223-374">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-374">✔</span></span>
<span data-ttu-id="7e223-375">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="7e223-375">Geometry.ToText()</span></span> | <span data-ttu-id="7e223-376">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-376">✔</span></span> | <span data-ttu-id="7e223-377">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-377">✔</span></span> | <span data-ttu-id="7e223-378">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-378">✔</span></span> | <span data-ttu-id="7e223-379">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-379">✔</span></span>
<span data-ttu-id="7e223-380">Geometry. toques (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-380">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="7e223-381">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-381">✔</span></span> | | <span data-ttu-id="7e223-382">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-382">✔</span></span> | <span data-ttu-id="7e223-383">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-383">✔</span></span>
<span data-ttu-id="7e223-384">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="7e223-384">Geometry.Union()</span></span> | | | <span data-ttu-id="7e223-385">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-385">✔</span></span> | <span data-ttu-id="7e223-386">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-386">✔</span></span>
<span data-ttu-id="7e223-387">Geometry. Union (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-387">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="7e223-388">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-388">✔</span></span> | <span data-ttu-id="7e223-389">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-389">✔</span></span> | <span data-ttu-id="7e223-390">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-390">✔</span></span> | <span data-ttu-id="7e223-391">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-391">✔</span></span>
<span data-ttu-id="7e223-392">Geometry. Within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7e223-392">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="7e223-393">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-393">✔</span></span> | <span data-ttu-id="7e223-394">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-394">✔</span></span> | <span data-ttu-id="7e223-395">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-395">✔</span></span> | <span data-ttu-id="7e223-396">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-396">✔</span></span>
<span data-ttu-id="7e223-397">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="7e223-397">GeometryCollection.Count</span></span> | <span data-ttu-id="7e223-398">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-398">✔</span></span> | <span data-ttu-id="7e223-399">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-399">✔</span></span> | <span data-ttu-id="7e223-400">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-400">✔</span></span> | <span data-ttu-id="7e223-401">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-401">✔</span></span>
<span data-ttu-id="7e223-402">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="7e223-402">GeometryCollection[int]</span></span> | <span data-ttu-id="7e223-403">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-403">✔</span></span> | <span data-ttu-id="7e223-404">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-404">✔</span></span> | <span data-ttu-id="7e223-405">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-405">✔</span></span> | <span data-ttu-id="7e223-406">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-406">✔</span></span>
<span data-ttu-id="7e223-407">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="7e223-407">LineString.Count</span></span> | <span data-ttu-id="7e223-408">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-408">✔</span></span> | <span data-ttu-id="7e223-409">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-409">✔</span></span> | <span data-ttu-id="7e223-410">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-410">✔</span></span> | <span data-ttu-id="7e223-411">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-411">✔</span></span>
<span data-ttu-id="7e223-412">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="7e223-412">LineString.EndPoint</span></span> | <span data-ttu-id="7e223-413">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-413">✔</span></span> | <span data-ttu-id="7e223-414">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-414">✔</span></span> | <span data-ttu-id="7e223-415">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-415">✔</span></span> | <span data-ttu-id="7e223-416">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-416">✔</span></span>
<span data-ttu-id="7e223-417">LineString. GetPointN (int)</span><span class="sxs-lookup"><span data-stu-id="7e223-417">LineString.GetPointN(int)</span></span> | <span data-ttu-id="7e223-418">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-418">✔</span></span> | <span data-ttu-id="7e223-419">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-419">✔</span></span> | <span data-ttu-id="7e223-420">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-420">✔</span></span> | <span data-ttu-id="7e223-421">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-421">✔</span></span>
<span data-ttu-id="7e223-422">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="7e223-422">LineString.IsClosed</span></span> | <span data-ttu-id="7e223-423">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-423">✔</span></span> | <span data-ttu-id="7e223-424">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-424">✔</span></span> | <span data-ttu-id="7e223-425">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-425">✔</span></span> | <span data-ttu-id="7e223-426">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-426">✔</span></span>
<span data-ttu-id="7e223-427">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="7e223-427">LineString.IsRing</span></span> | <span data-ttu-id="7e223-428">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-428">✔</span></span> | | <span data-ttu-id="7e223-429">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-429">✔</span></span> | <span data-ttu-id="7e223-430">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-430">✔</span></span>
<span data-ttu-id="7e223-431">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="7e223-431">LineString.StartPoint</span></span> | <span data-ttu-id="7e223-432">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-432">✔</span></span> | <span data-ttu-id="7e223-433">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-433">✔</span></span> | <span data-ttu-id="7e223-434">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-434">✔</span></span> | <span data-ttu-id="7e223-435">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-435">✔</span></span>
<span data-ttu-id="7e223-436">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="7e223-436">MultiLineString.IsClosed</span></span> | <span data-ttu-id="7e223-437">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-437">✔</span></span> | <span data-ttu-id="7e223-438">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-438">✔</span></span> | <span data-ttu-id="7e223-439">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-439">✔</span></span> | <span data-ttu-id="7e223-440">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-440">✔</span></span>
<span data-ttu-id="7e223-441">Punto. M</span><span class="sxs-lookup"><span data-stu-id="7e223-441">Point.M</span></span> | <span data-ttu-id="7e223-442">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-442">✔</span></span> | <span data-ttu-id="7e223-443">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-443">✔</span></span> | <span data-ttu-id="7e223-444">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-444">✔</span></span> | <span data-ttu-id="7e223-445">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-445">✔</span></span>
<span data-ttu-id="7e223-446">Point. X</span><span class="sxs-lookup"><span data-stu-id="7e223-446">Point.X</span></span> | <span data-ttu-id="7e223-447">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-447">✔</span></span> | <span data-ttu-id="7e223-448">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-448">✔</span></span> | <span data-ttu-id="7e223-449">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-449">✔</span></span> | <span data-ttu-id="7e223-450">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-450">✔</span></span>
<span data-ttu-id="7e223-451">Punto. Y</span><span class="sxs-lookup"><span data-stu-id="7e223-451">Point.Y</span></span> | <span data-ttu-id="7e223-452">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-452">✔</span></span> | <span data-ttu-id="7e223-453">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-453">✔</span></span> | <span data-ttu-id="7e223-454">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-454">✔</span></span> | <span data-ttu-id="7e223-455">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-455">✔</span></span>
<span data-ttu-id="7e223-456">Punto. Z</span><span class="sxs-lookup"><span data-stu-id="7e223-456">Point.Z</span></span> | <span data-ttu-id="7e223-457">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-457">✔</span></span> | <span data-ttu-id="7e223-458">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-458">✔</span></span> | <span data-ttu-id="7e223-459">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-459">✔</span></span> | <span data-ttu-id="7e223-460">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-460">✔</span></span>
<span data-ttu-id="7e223-461">Polygon. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="7e223-461">Polygon.ExteriorRing</span></span> | <span data-ttu-id="7e223-462">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-462">✔</span></span> | <span data-ttu-id="7e223-463">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-463">✔</span></span> | <span data-ttu-id="7e223-464">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-464">✔</span></span> | <span data-ttu-id="7e223-465">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-465">✔</span></span>
<span data-ttu-id="7e223-466">Polygon. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="7e223-466">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="7e223-467">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-467">✔</span></span> | <span data-ttu-id="7e223-468">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-468">✔</span></span> | <span data-ttu-id="7e223-469">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-469">✔</span></span> | <span data-ttu-id="7e223-470">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-470">✔</span></span>
<span data-ttu-id="7e223-471">Polygon. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="7e223-471">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="7e223-472">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-472">✔</span></span> | <span data-ttu-id="7e223-473">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-473">✔</span></span> | <span data-ttu-id="7e223-474">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-474">✔</span></span> | <span data-ttu-id="7e223-475">✔</span><span class="sxs-lookup"><span data-stu-id="7e223-475">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e223-476">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7e223-476">Additional resources</span></span>

* [<span data-ttu-id="7e223-477">Datos espaciales de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7e223-477">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="7e223-478">Página principal de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="7e223-478">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="7e223-479">Documentación espacial Npgsql</span><span class="sxs-lookup"><span data-stu-id="7e223-479">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="7e223-480">Documentación de PostGIS</span><span class="sxs-lookup"><span data-stu-id="7e223-480">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
