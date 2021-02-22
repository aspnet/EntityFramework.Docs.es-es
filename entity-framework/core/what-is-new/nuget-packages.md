---
title: Paquetes NuGet de EF Core
description: Información general de los diferentes paquetes NuGet de Entity Framework Core
author: ajcvickers
ms.date: 01/21/2021
uid: core/what-is-new/nuget-packages
ms.openlocfilehash: 4b6e210f2324ea97e006d681d399bfdd6918d1b4
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544588"
---
# <a name="ef-core-nuget-packages"></a>Paquetes NuGet de EF Core

Entity Framework Core (EF Core) se distribuye como paquetes [NuGet](https://www.nuget.org/). Los paquetes que necesita una aplicación dependen de lo siguiente:

- Tipo de sistema de base de datos que se usa (SQL Server, SQLite, etc.)
- Características de EF Core que se necesitan

El proceso habitual que debe seguir para instalar paquetes es este:

- Elija un proveedor de bases de datos e instale el paquete adecuado ([véase a continuación](#database-providers)).
- Instale también [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/) y [Microsoft.EntityFrameworkCore.Relational](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/) si usa un proveedor relacional. Esto ayuda a garantizar que se usen versiones coherentes. Además, NuGet le informará de cuándo se distribuyen las nuevas versiones del paquete.
- Opcionalmente, decida qué tipo de herramientas necesita e instale los paquetes adecuados para ello (véase a continuación).

Si quiere obtener ayuda para empezar a usar EF Core, consulte el [tutorial de introducción a Entity Framework Core](xref:core/get-started/overview/first-app).

## <a name="package-versions"></a>Versiones de paquete

Asegúrese de instalar la misma versión de todos los paquetes de EF Core enviados por Microsoft. Por ejemplo, si se instala la versión 5.0.3 de Microsoft.EntityFrameworkCore.SqlServer, la versión de todos los demás paquetes de Microsoft.EntityFrameworkCore.* también debe ser la 5.0.3.

Asegúrese también de que los paquetes externos sean compatibles con la versión de EF Core que se usa. En concreto, compruebe que el proveedor de bases de datos externas es compatible con la versión de EF Core que se usa. Las nuevas versiones principales de EF Core suelen requerir un proveedor de bases de datos actualizado.

> [!WARNING]
> NuGet no obliga a usar versiones de paquete coherentes. Compruebe siempre cuidadosamente las versiones a las que hace referencia en el archivo `csproj` (o equivalente).

## <a name="database-providers"></a>Proveedores de bases de datos

EF Core admite distintos sistemas de base de datos mediante el uso de "proveedores de bases de datos". Cada sistema tiene su propio proveedor de bases de datos, que se distribuye como paquete NuGet. Las aplicaciones deben instalar uno o varios de estos paquetes del proveedor.

En la tabla siguiente se indican proveedores de bases de datos comunes. Consulte [Proveedores de bases de datos](xref:core/providers/index) para obtener una lista más completa de los proveedores disponibles.

| Sistema de base de datos                   | Paquete
|:----------------------------------|----------------------
| SQL Server y SQL Azure          | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)
| SQLite                            | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)
| Azure Cosmos DB                   | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)
| PostgreSQL                        | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)*
| MySQL                             | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)*
| Base de datos en memoria de EF Core**      | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)

* Se trata de proveedores de código abierto populares y de alta calidad, desarrollados y distribuidos por la comunidad. Microsoft se encarga de la distribución de los demás proveedores enumerados.

** Considere detenidamente si le conviene usar el proveedor en memoria. No está diseñado para su uso en producción y podría no ser [la mejor solución para las pruebas](xref:core/testing/index).

## <a name="tools"></a>Herramientas

El uso de herramientas para [migraciones de EF Core](xref:core/managing-schemas/migrations/index) y de [técnicas de ingeniería inversa (scaffolding) a partir de una base de datos existente](xref:core/managing-schemas/scaffolding) requiere la instalación del paquete de herramientas adecuado:

- [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) para herramientas de PowerShell que funcionan en la [consola del administrador de paquetes](/nuget/consume-packages/install-use-packages-powershell) de Visual Studio
- [dotnet-ef](https://www.nuget.org/packages/dotnet-ef/) y [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design/) para herramientas de línea de comandos multiplataforma

Consulte [Referencia sobre las herramientas de Entity Framework Core](xref:core/cli/index) para obtener más información sobre el uso de las herramientas de EF Core, incluido cómo instalar correctamente la herramienta `dotnet-ef` en un proyecto o globalmente.

> [!TIP]
> De forma predeterminada, el paquete Microsoft.EntityFrameworkCore.Design se instala de manera que no se implemente con la aplicación. Esto también significa que sus tipos no se pueden usar de forma transitiva en otros proyectos. Use un elemento `PackageReference` normal en el archivo `.csproj` (o equivalente) si necesita acceder a los tipos del paquete. Consulte [Servicios en tiempo de diseño](xref:core/cli/services) para obtener más información.

## <a name="extension-packages"></a>Paquetes de extensión

Hay muchas [extensiones para EF Core](xref:core/extensions/index) publicadas por Microsoft y terceros como paquetes NuGet. Estos son algunos de los paquetes usados comúnmente:

| Funcionalidad                                | Paquete | Dependencias adicionales
|:---------------------------------------------|---------|------------------------
| Servidores proxy para la carga diferida y el seguimiento de cambios | [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) | [Castle.Core](https://www.nuget.org/packages/Castle.Core/)
| Compatibilidad espacial con SQL Server               | [Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) y [NetTopologySuite.IO.SqlServerBytes](https://www.nuget.org/packages/NetTopologySuite.IO.SqlServerBytes/)
| Compatibilidad espacial con SQLite                   | [Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) y [NetTopologySuite.IO.SpatiaLite](https://www.nuget.org/packages/NetTopologySuite.IO.SpatiaLite/)
| Compatibilidad espacial con PostgreSQL               | [Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) y [NetTopologySuite.IO.PostGIS](https://www.nuget.org/packages/NetTopologySuite.IO.PostGIS/) (mediante [Npgsql.NetTopologySuite](https://www.nuget.org/packages/Npgsql.NetTopologySuite/))
| Compatibilidad espacial con MySQL                    | [Pomelo.EntityFrameworkCore.MySql.NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/)

## <a name="other-packages"></a>Otros paquetes

Otros paquetes de EF Core se extraen como dependencias del paquete del proveedor de bases de datos. Aun así, puede que le interese agregar referencias de paquete explícitas para estos paquetes, de modo que NuGet proporcione notificaciones cuando se publiquen nuevas versiones.

| Funcionalidad                                              | Paquete
|:-----------------------------------------------------------|--------
| Funcionalidad básica de EF Core                                | [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)
| Funcionalidad común de bases de datos relacionales                   | [Microsoft.EntityFrameworkCore.Relational](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/)
| Paquete ligero para atributos de EF Core, etc.           | [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)
| Analizadores de código de Roslyn para el uso de EF Core                    | [Microsoft.EntityFrameworkCore.Analyzers](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Analyzers/)
| Proveedor de SQLite para EF Core sin dependencia de SQLite nativa | [Microsoft.EntityFrameworkCore.Sqlite.Core](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.Core/)

## <a name="packages-for-database-provider-testing"></a>Paquetes para pruebas del proveedor de bases de datos

Los siguientes paquetes se usan para probar los proveedores de bases de datos integrados en repositorios de GitHub externos. Consulte [efcore.pg](https://github.com/npgsql/efcore.pg) y [Pomelo.EntityFrameworkCore.MySql](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) para obtener ejemplos. Las aplicaciones no deben instalar estos paquetes.

| Funcionalidad                                              | Paquete
|:-----------------------------------------------------------|--------
| Pruebas para cualquier proveedor de bases de datos                            | [Microsoft.EntityFrameworkCore.Specification.Tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Specification.Tests/)
| Pruebas para proveedores de bases de datos relacionales                    | [Microsoft.EntityFrameworkCore.Relational.Specification.Tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational.Specification.Tests/)

## <a name="obsolete-packages"></a>Paquetes obsoletos

**No** instale los siguientes paquetes obsoletos y quítelos si están instalados actualmente en los proyectos:

- Microsoft.EntityFrameworkCore.Relational.Design
- Microsoft.EntityFrameworkCore.Tools.DotNet
- Microsoft.EntityFrameworkCore.SqlServer.Design
- Microsoft.EntityFrameworkCore.Sqlite.Design
- Microsoft.EntityFrameworkCore.Relational.Design.Specification.Tests
