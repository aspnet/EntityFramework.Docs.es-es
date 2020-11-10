---
title: 'Datos espaciales: EF Core'
description: Usar datos espaciales en un modelo de Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: fa9cf30ddb4291d96486934544b568b67b126846
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430526"
---
# <a name="spatial-data"></a>Datos espaciales

> [!NOTE]
> Esta característica se agregó en EF Core 2,2.

Los datos espaciales representan la ubicación física y la forma de los objetos. Muchas bases de datos proporcionan compatibilidad con este tipo de datos, por lo que se puede indizar y consultar junto con otros datos. Entre los escenarios comunes se incluyen las consultas de objetos dentro de una distancia determinada desde una ubicación o la selección del objeto cuyo borde contiene una ubicación determinada. EF Core admite la asignación a tipos de datos espaciales mediante la biblioteca espacial NetTopologySuite.

## <a name="installing"></a>Instalación de

Para usar los datos espaciales con EF Core, debe instalar el paquete NuGet de soporte adecuado. El paquete que necesita instalar depende del proveedor que esté usando.

Proveedor de EF Core                        | Paquete de NuGet espacial
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. EntityFrameworkCore. SQLite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
Pomelo.EntityFrameworkCore.MySql        | [Pomelo. EntityFrameworkCore. MySql. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
Devart.Data.MySql.EFCore                | [Devart. Data. MySql. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
Devart.Data.PostgreSql.EFCore           | [Devart. Data. PostgreSql. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
Devart.Data.SQLite.EFCore               | [Devart. Data. SQLite. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
Teradata.EntityFrameworkCore            | [Teradata. EntityFrameworkCore. NetTopologySuite](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a>NetTopologySuite

[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) es una biblioteca espacial para .net. EF Core permite la asignación a tipos de datos espaciales en la base de datos mediante el uso de tipos NTS en el modelo.

Para habilitar la asignación a tipos espaciales a través de NTS, llame al método UseNetTopologySuite en el generador de opciones DbContext del proveedor. Por ejemplo, con SQL Server le llamaría como esto.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

Hay varios tipos de datos espaciales. El tipo que use dependerá de los tipos de formas que desee permitir. Esta es la jerarquía de tipos NTS que puede usar para las propiedades del modelo. Están ubicados en el `NetTopologySuite.Geometries` espacio de nombres.

* Geometría
  * Punto
  * LineString
  * Polygon
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> La CircularString, CompoundCurve y CurePolygon no son compatibles con NTS.

El uso del tipo de geometría base permite que la propiedad especifique cualquier tipo de forma.

## <a name="longitude-and-latitude"></a>Longitud y latitud

Las coordenadas en NTS están en términos de valores X e y. Para representar la longitud y la latitud, use X para longitud e y para latitud. Tenga en cuenta que esto es **hacia atrás** desde el `latitude, longitude` formato en el que normalmente se ven estos valores.

## <a name="querying-data"></a>Consulta de datos

Las siguientes clases de entidad se pueden usar para asignar tablas en la [base de datos de ejemplo Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630).

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

En LINQ, los métodos y las propiedades NTS disponibles como funciones de base de datos se traducirán a SQL. Por ejemplo, los métodos Distance y Contains se traducen en las siguientes consultas. Consulte la documentación del proveedor para saber qué métodos se admiten.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a>Ingeniería inversa

Los paquetes de NuGet espaciales también habilitan los modelos de [ingeniería inversa](xref:core/managing-schemas/scaffolding) con propiedades espaciales, pero debe instalar el paquete * *_antes_* de ejecutar `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` . Si no lo hace, recibirá advertencias sobre cómo no encontrar las asignaciones de tipos para las columnas y se omitirán las columnas.

## <a name="srid-ignored-during-client-operations"></a>SRID omitido durante las operaciones de cliente

NTS omite los valores de SRID durante las operaciones. Supone un sistema de coordenadas plano. Esto significa que si especifica coordenadas en términos de longitud y latitud, algunos valores evaluados por el cliente como la distancia, la longitud y el área estarán en grados, no en metros. Para valores más significativos, primero debe proyectar las coordenadas en otro sistema de coordenadas mediante una biblioteca como [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) antes de calcular estos valores.

Si una operación es evaluada por el servidor mediante EF Core a través de SQL, la unidad del resultado se determinará por la base de datos.

Este es un ejemplo del uso de ProjNet4GeoAPI para calcular la distancia entre dos ciudades.

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> 4326 hace referencia a WGS 84, un estándar que se usa en GPS y en otros sistemas geográficos.

## <a name="additional-resources"></a>Recursos adicionales

### <a name="database-specific-information"></a>Información específica de la base de datos

Asegúrese de leer la documentación del proveedor para obtener información adicional sobre cómo trabajar con datos espaciales.

_ [Datos espaciales en el proveedor de SQL Server](xref:core/providers/sql-server/spatial)
* [Datos espaciales en el proveedor de SQLite](xref:core/providers/sqlite/spatial)
* [Datos espaciales en el proveedor Npgsql](https://www.npgsql.org/efcore/mapping/nts.html)

### <a name="other-resources"></a>Otros recursos

* [Documentos de NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
* [EF Core sesión reunión](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15)de la comunidad, centrándose en los datos espaciales y NetTopologySuite.
