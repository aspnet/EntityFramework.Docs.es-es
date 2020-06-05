---
title: Proveedores de bases de datos - EF Core
author: ajcvickers
ms.date: 12/17/2019
uid: core/providers/index
ms.openlocfilehash: c7b4b1796d4f3523d320ce62779fae222c21187c
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418928"
---
# <a name="database-providers"></a>Proveedores de bases de datos

Entity Framework Core puede tener acceso a muchas bases de datos diferentes a través de bibliotecas de complementos denominadas proveedores de bases de datos.

## <a name="current-providers"></a>Proveedores actuales

> [!IMPORTANT]  
> Los proveedores de EF Core se componen de una serie de orígenes. No todos los proveedores se mantienen como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore). Al considerar un proveedor, evalúe la calidad, las licencias, el soporte técnico, etc. a fin de asegurarse de que satisface los requisitos. Además, asegúrese de revisar la documentación de cada proveedor para obtener información detallada de compatibilidad de versiones.

> [!IMPORTANT]  
> Por lo general, los proveedores de EF Core funcionan en versiones secundarias, pero no en todas las versiones principales. Por ejemplo, un proveedor publicado para EF Core 2.1 debería funcionar con EF Core 2.2, pero no con EF Core 3.0. 

| Paquete NuGet                                                                                                        | Motores de base de datos compatibles | Mantenedor o proveedor                                                           | Notas o requisitos | Creado para la versión | Vínculos útiles                                                                                                                                                                                       |
|:---------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:---------------------|:------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)    | De SQL Server 2012 en adelante    | [Proyecto EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [Documentación](xref:core/providers/sql-server/index)                                                                                                                                                       |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)          | De SQLite 3.7 en adelante         | [Proyecto EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [Documentación](xref:core/providers/sqlite/index)                                                                                                                                                           |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)      | Base de datos en memoria de EF Core | [Proyecto EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | [Limitaciones](xref:core/miscellaneous/testing/in-memory)                 | 3.1               | [Documentación](xref:core/providers/in-memory/index)                                                                                                                                                        |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)          | API de SQL de Azure Cosmos DB    | [Proyecto EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [Documentación](xref:core/providers/cosmos/index)                                                                                                                                                           |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)        | PostgreSQL                 | [Equipo de desarrollo de Npgsql](https://github.com/npgsql)                          |                      | 3.1               | [Documentación](https://www.npgsql.org/efcore/index.html)                                                                                                                                                   |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                  | MySQL, MariaDB             | [Proyecto Pomelo Foundation](https://github.com/PomeloFoundation)              |                      | 3.1               | [Archivo Léame](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                                               |
| [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                      | MySQL                      | [Proyecto MySQL](https://dev.mysql.com) (Oracle)                               |                      | 3.1               | [Documentación](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                                                                         |
| [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                             | Oracle DB 11.2 y versiones posteriores     | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                   | Beta                 | 3.1               | [Sitio web](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                                                                       |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                 | De MySQL 5 en adelante            | [DevArt](https://www.devart.com/)                                             | Pagado                 | 3.1               | [Documentación](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                                              |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                               | Oracle DB 9.2.0.4 y versiones posteriores  | [DevArt](https://www.devart.com/)                                             | Pagado                 | 3.1               | [Documentación](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                                             |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                       | De PostgreSQL 8.0 en adelante     | [DevArt](https://www.devart.com/)                                             | Pagado                 | 3.1               | [Documentación](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                                         |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                               | De SQLite 3 en adelante           | [DevArt](https://www.devart.com/)                                             | Pagado                 | 3.1               | [Documentación](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                                             |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                                   | Almacena datos en archivos.       | [Morris Janatzek](https://github.com/morrisjdev)                              | Con fines de desarrollo. | 3.0               | [Archivo Léame](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                                                                                              |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                   | Archivos de Microsoft Access     | [Bubi](https://github.com/bubibubi)                                           | .NET Framework       | 2.2               | [Archivo Léame](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                                           |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)      | SQL Server Compact 3,5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2.2               | [Wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)      | SQL Server Compact 4.0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2.2               | [Wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/) | Firebird 2.5 y 3.x       | [Jiří Činčura](https://github.com/cincuranet)                                 |                      | 3.1               | [Documentación](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                                                                           |
| [Teradata.EntityFrameworkCore](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                         | Teradata Database 16.10 en adelante | [Teradata](https://downloads.teradata.com/download/connectivity/net-data-provider-for-teradata) | | 2.2               |[Sitio web](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                                                            |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                   | Firebird 2.5 y 3.x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           |                      | 2.1               | [Wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                                                     |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                         | Progress OpenEdge          | [Alex Wiese](https://github.com/alexwiese)                                    |                      | 2.1               | [Archivo Léame](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                                                                          |
| [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore)                                    | Db2, Informix              | [IBM](https://ibm.com)                                                        | Versión de Windows      | 2.0               | [blog](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)                            | Db2, Informix              | [IBM](https://ibm.com)                                                        | Versión de Linux        | 2.0               | [blog](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-osx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-osx)                            | Db2, Informix              | [IBM](https://ibm.com)                                                        | Versión de macOS        | 2.0               | [blog](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)                  | Servidor MyCAT               | [Proyecto Pomelo Foundation](https://github.com/PomeloFoundation)              | Solo versión preliminar      | 1.1               | [Archivo Léame](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                                               |

## <a name="adding-a-database-provider-to-your-application"></a>Agregar un proveedor de bases de datos a la aplicación

La mayoría de los proveedores de bases de datos para EF Core se distribuyen como paquetes NuGet y se pueden instalar siguiendo estos pasos:

## <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package provider_package_name
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
install-package provider_package_name
```

***

Una vez instalado, se configurará el proveedor en su `DbContext`, ya sea en el método `OnConfiguring` o el método `AddDbContext` si se usa un contenedor de inserción de dependencias.
Por ejemplo, la línea siguiente configura el proveedor de SQL Server con la cadena de conexión pasada:

``` csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```  

Los proveedores de bases de datos pueden ampliar EF Core para habilitar una funcionalidad única para bases de datos específicas.
Algunos conceptos son comunes a la mayoría de las bases de datos y se incluyen en los componentes principales de EF Core.
Estos conceptos incluyen la expresión de consultas en LINQ, las transacciones y el seguimiento de cambios en objetos una vez cargados desde la base de datos.
Algunos conceptos son específicos de un proveedor determinado.
Por ejemplo, el proveedor de SQL Server permite [configurar tablas optimizadas para memoria](xref:core/providers/sql-server/memory-optimized-tables) (una característica específica de SQL Server).
Otros conceptos son específicos de una clase de proveedores.
Por ejemplo, los proveedores de EF Core para bases de datos relacionales se basan en la biblioteca común `Microsoft.EntityFrameworkCore.Relational`, que proporciona API para configurar asignaciones de tabla y columna, restricciones de clave externa, etc. Los proveedores normalmente se distribuyen como paquetes NuGet.

> [!IMPORTANT]  
> Cuando se publica una nueva versión de revisión de EF Core, suele incluir actualizaciones del paquete `Microsoft.EntityFrameworkCore.Relational`.
> Cuando se agrega un proveedor de bases de datos relacional, este paquete se convierte en una dependencia transitiva de la aplicación.
> Pero muchos proveedores se publican independientemente de EF Core y es posible que no puedan actualizarse para que se basen en la versión de revisión más reciente de ese paquete.
> A fin de asegurarse de que obtendrá todas las correcciones de errores, se recomienda agregar la versión de revisión de `Microsoft.EntityFrameworkCore.Relational` como dependencia directa de la aplicación.
