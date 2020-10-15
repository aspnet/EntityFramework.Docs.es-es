---
title: 'Actualización de versiones anteriores a EF Core 2: EF Core'
description: Instrucciones y notas para actualizar a Entity Framework Core 2.0
author: ajcvickers
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: c7c736629209da99f191ceb0d4000d19f40414b9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063444"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>Actualización de las aplicaciones de las versiones anteriores a EF Core 2.0

Hemos aprovechado la oportunidad de pulir bastante las API y los comportamientos existentes en la versión 2.0. Hay algunas mejoras que pueden requerir la modificación del código existente de las aplicaciones, aunque creemos que para casi todas el impacto será bajo. En la mayoría de casos, para reemplazar las API obsoletas solo es necesario volver a efectuar la compilación y realizar unos cambios mínimos guiados.

La actualización de una aplicación existente a EF Core 2.0 puede requerir lo siguiente:

1. Actualizar la implementación de la aplicación .NET de destino a una que admita .NET Standard 2.0. Vea [Implementaciones de .NET compatibles](xref:core/platforms/index) para obtener más detalles.

2. Identificar un proveedor para la base de datos de destino que sea compatible con EF Core 2.0. Vea debajo [EF Core 2.0 requiere un proveedor de base de datos 2.0](#ef-core-20-requires-a-20-database-provider).

3. Actualizar todos los paquetes de EF Core (entorno de ejecución y herramientas) a la versión 2.0. Consulte [Instalación de EF Core](xref:core/get-started/install/index) para obtener información detallada.

4. Realizar los cambios de código necesarios para compensar los cambios importantes descritos en el resto de este documento.

## <a name="aspnet-core-now-includes-ef-core"></a>EF Core, ahora incluido en ASP.NET Core

Las aplicaciones para ASP.NET Core 2.0 pueden usar EF Core 2.0 sin dependencias adicionales además de proveedores de bases de datos de terceros. Sin embargo, las aplicaciones destinadas a versiones anteriores de ASP.NET Core deben actualizarse a ASP.NET Core 2.0 para usar EF Core 2.0. Para obtener información detallada sobre la actualización de aplicaciones ASP.NET Core a la versión 2.0, vea la [documentación de ASP.NET Core sobre el tema](/aspnet/core/migration/1x-to-2x/).

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>Nueva forma de obtener servicios de aplicación en ASP.NET Core

El patrón recomendado para las aplicaciones web de ASP.NET Core se ha actualizado a la versión 2.0 de un modo que ha afectado a la lógica de EF Core en tiempo de diseño utilizada en las versiones 1.x. Anteriormente, en tiempo de diseño, EF Core intentaba invocar `Startup.ConfigureServices` directamente para acceder al proveedor de servicios de la aplicación. En ASP.NET Core 2.0, la configuración se inicializa fuera de la clase `Startup`. Las aplicaciones que usan EF Core normalmente acceden a su cadena de conexión desde la configuración, por lo que `Startup` en sí ya no es suficiente. Si actualiza una aplicación ASP.NET Core 1.x, es posible que reciba el error siguiente al usar las herramientas de EF Core.

> No se encontró ningún constructor sin parámetros en "ApplicationContext". Agregue un constructor sin parámetros a "ApplicationContext" o agregue una implementación de "IDesignTimeDbContextFactory&lt;ApplicationContext&gt;" en el mismo ensamblado que "ApplicationContext".

Se ha agregado un nuevo enlace en tiempo de diseño en la plantilla predeterminada de ASP.NET Core 2.0. El método `Program.BuildWebHost` estático permite a EF Core acceder al proveedor de servicios de la aplicación en tiempo de diseño. Si va a actualizar una aplicación ASP.NET Core 1.x, deberá actualizar la clase `Program` para que se parezca a lo siguiente.

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

La adopción de este patrón nuevo al actualizar aplicaciones a la versión 2.0 es muy recomendable y necesaria para que funcionen características de producto como las migraciones de Entity Framework Core. La otra alternativa común es [implementar *IDesignTimeDbContextFactory\<TContext>* ](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).

## <a name="idbcontextfactory-renamed"></a>Cambio de nombre de IDbContextFactory

Con el fin de admitir varios patrones de aplicación y proporcionar a los usuarios un mayor control sobre cómo se utiliza su `DbContext` en tiempo de diseño, en el pasado se ofrecía la interfaz `IDbContextFactory<TContext>`. En tiempo de diseño, las herramientas de EF Core detectarán las implementaciones de esta interfaz en el proyecto y la usarán para crear objetos `DbContext`.

Esta interfaz tenía un nombre muy general y confuso para algunos usuarios, quienes acababan intentando reutilizarlo para otros escenarios de creación de `DbContext`. No estaban protegidos cuando las herramientas de EF intentaban usar su implementación en tiempo de diseño y provocaban que se produjera un error en comandos como `Update-Database` o `dotnet ef database update`.

Con el fin de comunicar la semántica sólida en tiempo de diseño de esta interfaz, se le ha cambiado el nombre a `IDesignTimeDbContextFactory<TContext>`.

En la versión 2.0, `IDbContextFactory<TContext>` todavía existe, pero está marcado como obsoleto.

## <a name="dbcontextfactoryoptions-removed"></a>Eliminación de DbContextFactoryOptions

Debido a los cambios en ASP.NET Core 2.0 descritos anteriormente, encontramos que `DbContextFactoryOptions` ya no era necesario en la nueva interfaz de `IDesignTimeDbContextFactory<TContext>`. Estas son las alternativas que debe usar en su lugar.

| DbContextFactoryOptions | Alternativa                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>Cambio del directorio de trabajo en tiempo de diseño

Los cambios en ASP.NET Core 2.0 también requerían que el directorio de trabajo que usaba `dotnet ef` se alineara con el que usaba Visual Studio al ejecutar la aplicación. Un efecto secundario observable de esto es que los nombres de archivo de SQLite ahora se refieren al directorio del proyecto y no al de salida, como solía ser.

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 requiere un proveedor de base de datos de la versión 2.0

Para EF Core 2.0, hemos realizado muchas simplificaciones y mejoras en el funcionamiento de los proveedores de bases de datos. Esto significa que los proveedores 1.0.x y 1.1.x no funcionarán con EF Core 2.0.

El equipo de EF envía los proveedores de SQL Server y SQLite, y las versiones 2.0 estarán disponibles como parte de la versión 2.0. Los proveedores de terceros de código abierto para [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL) y [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) se están actualizando a la versión 2.0. Para el resto de proveedores, póngase en contacto con el escritor en cuestión.

## <a name="logging-and-diagnostics-events-have-changed"></a>Cambios en los eventos de diagnóstico y registro

Nota: Estos cambios no deberían afectar a gran parte del código de aplicación.

Los id. de evento de los mensajes enviados a un elemento [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) han cambiado en la versión 2.0. Los identificadores de evento ahora son únicos en el código de EF Core. Ahora, estos mensajes también siguen el patrón estándar de registro estructurado que usa, por ejemplo, MVC.

Las categorías de registrador también han cambiado. Ahora hay un conjunto conocido de categorías a las que se accede a través de [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).

Los eventos [DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) ahora usan los mismos nombres de id. de evento que los mensajes de `ILogger` correspondientes. Todas las cargas de evento son tipos nominales derivados de [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).

Los id. de eventos, tipos de carga y categorías se documentan en las clases [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) y [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid).

Los id. también se han pasado de Microsoft.EntityFrameworkCore.Infrastructure al nuevo espacio de nombres Microsoft.EntityFrameworkCore.Diagnostics.

## <a name="ef-core-relational-metadata-api-changes"></a>Cambios en la API de metadatos relacionales de EF Core

EF Core 2.0 ahora compila un elemento [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) diferente para cada proveedor que se va a usar. Esto suele ser transparente para la aplicación. Esto ha permitido una simplificación de las API de metadatos de nivel inferior, de modo que cualquier acceso a _conceptos de metadatos relacionales comunes_ siempre se realiza mediante una llamada a `.Relational` en lugar de a `.SqlServer`, `.Sqlite`, etc. Por ejemplo, un código de 1.1.x similar al siguiente:

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

Ahora debe escribirse de la siguiente manera:

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

En lugar de utilizar métodos como `ForSqlServerToTable`, los métodos de extensión ahora están disponibles para escribir código condicional basado en el proveedor actual en uso. Por ejemplo:

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

Tenga en cuenta que este cambio solo se aplica a las API o los metadatos que se definen para _todos_ los proveedores relacionales. La API y los metadatos siguen siendo los mismos cuando son específicos de un único proveedor. Por ejemplo, los índices en clúster son específicos de SQL Server, por lo que se deben seguir usando `ForSqlServerIsClustered` y `.SqlServer().IsClustered()`.

## <a name="dont-take-control-of-the-ef-service-provider"></a>No tomar el control del proveedor de servicios de EF

EF Core usa un elemento `IServiceProvider` interno (un contenedor de inserción de dependencias) para su implementación interna. Las aplicaciones deben permitir que EF Core cree y administre este proveedor, excepto en casos especiales. Valore seriamente la posibilidad de quitar todas las llamadas a `UseInternalServiceProvider`. Si una aplicación necesita llamar a `UseInternalServiceProvider`, sopese [tramitar una incidencia](https://github.com/dotnet/efcore/Issues) para que podamos investigar otras maneras de controlar su escenario.

El código de aplicación no requiere la llamada a `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. a menos que se llame también a `UseInternalServiceProvider`. Quite todas las llamadas existentes a `AddEntityFramework` o `AddEntityFrameworkSqlServer`, etc.; `AddDbContext` debe seguir utilizándose igual que antes.

## <a name="in-memory-databases-must-be-named"></a>Obligatoriedad de nombre de las bases de datos en memoria

La base de datos global en memoria sin nombre se ha quitado y, en su lugar, todas las bases de datos en memoria deben tener nombre. Por ejemplo:

```csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

Esto crea o usa una base de datos con el nombre "MyDatabase". Si se llama de nuevo a `UseInMemoryDatabase` con el mismo nombre, se usará la misma base de datos en memoria, lo que permite que varias instancias de contexto lo compartan.

## <a name="read-only-api-changes"></a>Cambios en la API de solo lectura

`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave` y `IsStoreGeneratedAlways` han quedado obsoletos y se han reemplazado por [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) y [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior). Estos comportamientos se aplican a cualquier propiedad (no solo a las propiedades generadas por el almacén) y determinan cómo se debe usar el valor de la propiedad al insertarlo en una fila de base de datos (`BeforeSaveBehavior`) o al actualizar una fila de base de datos existente (`AfterSaveBehavior`).

Las propiedades marcadas como [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (por ejemplo, para las columnas calculadas) omitirán de forma predeterminada cualquier valor establecido actualmente en la propiedad. Esto significa que siempre se obtendrá un valor generado por el almacén independientemente de si se ha establecido o modificado algún valor en la entidad de la que se realiza el seguimiento. Esto se puede cambiar estableciendo un elemento `Before\AfterSaveBehavior` distinto.

## <a name="new-clientsetnull-delete-behavior"></a>Comportamiento de eliminación de ClientSetNull nuevo

En versiones anteriores, [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) tenía un comportamiento para las entidades de las que el contexto realizaba un seguimiento que coincidía más estrechamente con la semántica `SetNull`. En EF Core 2.0, se ha introducido un comportamiento de `ClientSetNull` nuevo como valor predeterminado para las relaciones opcionales. Este comportamiento tiene la semántica `SetNull` para las entidades sometidas a seguimiento y el comportamiento `Restrict` para las bases de datos creadas mediante EF Core. La experiencia nos indica que estos son los comportamientos más esperados y útiles para las entidades sometidas a seguimiento y la base de datos. `DeleteBehavior.Restrict` ahora se admite para entidades sometidas a seguimiento cuando se establece para relaciones opcionales.

## <a name="provider-design-time-packages-removed"></a>Eliminación de los paquetes en tiempo de diseño del proveedor

El paquete `Microsoft.EntityFrameworkCore.Relational.Design` se ha eliminado. Su contenido se ha consolidado en `Microsoft.EntityFrameworkCore.Relational` y `Microsoft.EntityFrameworkCore.Design`.

Esto se propaga a los paquetes en tiempo de diseño del proveedor. Estos paquetes (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) se han quitado y su contenido se ha consolidado en los paquetes principales del proveedor.

Para habilitar `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` en EF Core 2.0, solo tiene que hacer referencia al paquete del proveedor único:

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
