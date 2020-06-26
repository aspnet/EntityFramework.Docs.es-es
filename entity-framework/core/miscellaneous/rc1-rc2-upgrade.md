---
title: Actualización de EF Core 1,0 RC1 a RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370531"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>Actualización de EF Core 1,0 RC1 a 1,0 RC2

En este artículo se proporcionan instrucciones para mover una aplicación compilada con paquetes RC1 a RC2.

## <a name="package-names-and-versions"></a>Nombres y versiones de los paquetes

Entre RC1 y RC2, cambiamos de "Entity Framework 7" a "Entity Framework Core". Puede leer más sobre los motivos del cambio en [esta publicación de Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx). Debido a este cambio, los nombres de los paquetes cambiaron de `EntityFramework.*` a `Microsoft.EntityFrameworkCore.*` y nuestras versiones de `7.0.0-rc1-final` a `1.0.0-rc2-final` (o `1.0.0-preview1-final` para las herramientas).

**Tendrá que quitar por completo los paquetes RC1 y, a continuación, instalar los RC2.** Esta es la asignación para algunos paquetes comunes.

| Paquete RC1                                               | Equivalentes de RC2                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework. MicrosoftSqlServer 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. SqlServer 1.0.0-RC2-final      |
| EntityFramework. SQLite 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. SQLite 1.0.0-RC2-final      |
| EntityFramework7. Npgsql 3.1.0-RC1-3     | NpgSql. EntityFrameworkCore. Postgres<to be advised>      |
| EntityFramework. SqlServerCompact35 7.0.0-RC1-final | EntityFrameworkCore. SqlServerCompact35 1.0.0-RC2-final      |
| EntityFramework. SqlServerCompact40 7.0.0-RC1-final | EntityFrameworkCore. SqlServerCompact40 1.0.0-RC2-final      |
| EntityFramework. inmemory 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. inmemory 1.0.0-RC2-final      |
| EntityFramework. IBMDataServer 7.0.0-beta1     | Todavía no está disponible para RC2                                            |
| EntityFramework. Commands 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. Tools 1.0.0-preview1-final |
| EntityFramework. MicrosoftSqlServer. Design 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. SqlServer. Design 1.0.0-RC2-final      |

## <a name="namespaces"></a>Espacios de nombres

Junto con los nombres de paquete, los espacios de nombres cambiaron de `Microsoft.Data.Entity.*` a `Microsoft.EntityFrameworkCore.*` . Puede controlar este cambio con la búsqueda y el reemplazo de `using Microsoft.Data.Entity` con `using Microsoft.EntityFrameworkCore` .

## <a name="table-naming-convention-changes"></a>Cambios en la Convención de nomenclatura de tablas

Un cambio funcional significativo que se llevó a cabo en RC2 era usar el nombre de la `DbSet<TEntity>` propiedad para una entidad determinada como el nombre de tabla al que se asigna, en lugar de simplemente el nombre de clase. Puede leer más sobre este cambio en [el problema del anuncio relacionado](https://github.com/aspnet/Announcements/issues/167).

En el caso de las aplicaciones RC1 existentes, se recomienda agregar el código siguiente al inicio del `OnModelCreating` método para mantener la estrategia de nomenclatura RC1:

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

Si desea adoptar la nueva estrategia de nomenclatura, se recomienda completar correctamente el resto de los pasos de actualización y, a continuación, quitar el código y crear una migración para aplicar el cambio de nombre de la tabla.

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext/Startup.cs cambia (solo proyectos de ASP.NET Core)

En RC1, tenía que agregar Entity Framework servicios al proveedor de servicios de aplicaciones `Startup.ConfigureServices(...)` :

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

En RC2, puede quitar las llamadas a `AddEntityFramework()` , `AddSqlServer()` , etc.:

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

También debe agregar un constructor, al contexto derivado, que toma las opciones de contexto y las pasa al constructor base. Esto es necesario porque hemos quitado algunas de las ideas mágicas que snuck en segundo plano:

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>Pasar un IServiceProvider

Si tiene código RC1 que pasa un `IServiceProvider` al contexto, ahora se ha trasladado a `DbContextOptions` , en lugar de ser un parámetro de constructor independiente. Utilice `DbContextOptionsBuilder.UseInternalServiceProvider(...)` para establecer el proveedor de servicios.

### <a name="testing"></a>Prueba

El escenario más común para hacerlo era controlar el ámbito de una base de datos inmemory al realizar las pruebas. Vea el artículo sobre las [pruebas](testing/index.md) actualizadas para obtener un ejemplo de cómo hacerlo con RC2.

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>Resolver servicios internos desde el proveedor de servicios de aplicación (solo proyectos de ASP.NET Core)

Si tiene una aplicación ASP.NET Core y desea que EF resuelva los servicios internos del proveedor de servicios de aplicación, hay una sobrecarga de `AddDbContext` que le permite configurar lo siguiente:

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> Se recomienda permitir que EF administre internamente sus propios servicios, a menos que tenga un motivo para combinar los servicios de EF internos en el proveedor de servicios de aplicación. La razón principal por la que puede querer hacer esto es usar el proveedor de servicios de aplicación para reemplazar los servicios que EF usa internamente

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a>Comandos de DNX = CLI de .NET Core de> (solo proyectos de ASP.NET Core)

Si anteriormente usó los `dnx ef` comandos para los proyectos de ASP.net 5, ahora se han pasado a `dotnet ef` comandos. Todavía se aplica la misma sintaxis de comando. Puede usar `dotnet ef --help` para obtener información sobre la sintaxis.

La forma en que se registran los comandos ha cambiado en RC2, debido a que DNX se ha reemplazado por el CLI de .NET Core. Los comandos se registran ahora en una `tools` sección de `project.json` :

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> Si usa Visual Studio, ahora puede usar la consola del administrador de paquetes para ejecutar comandos EF para proyectos de ASP.NET Core (esto no se admitía en RC1). Todavía tiene que registrar los comandos en la `tools` sección de `project.json` para hacerlo.

## <a name="package-manager-commands-require-powershell-5"></a>Los comandos del administrador de paquetes requieren PowerShell 5

Si usa los comandos Entity Framework en la consola del administrador de paquetes en Visual Studio, tendrá que asegurarse de que tiene PowerShell 5 instalado. Se trata de un requisito temporal que se quitará en la siguiente versión (consulte el [problema #5327](https://github.com/aspnet/EntityFramework/issues/5327) para obtener más detalles).

## <a name="using-imports-in-projectjson"></a>Usar "Imports" en project.js

Algunas de las dependencias de EF Core no admiten aún .NET Standard. EF Core en .NET Standard y los proyectos de .NET Core pueden requerir la adición de "Imports" a project.jscomo solución temporal.

Al agregar EF, la restauración de NuGet mostrará este mensaje de error:

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

La solución consiste en importar manualmente el perfil portátil "portable-net451 + win8". Esto obliga a NuGet a tratar estos binarios que coinciden con este proporcionado como un marco compatible con .NET Standard, aunque no lo sean. Aunque "portable-net451 + win8" no es 100% compatible con .NET Standard, es lo suficientemente compatible para la transición de PCL a .NET Standard. Las importaciones se pueden quitar cuando las dependencias de EF finalmente se actualizan a .NET Standard.

Se pueden agregar varios marcos a "Imports" en la sintaxis de la matriz. Otras importaciones pueden ser necesarias si agrega bibliotecas adicionales al proyecto.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

Vea el [problema #5176](https://github.com/aspnet/EntityFramework/issues/5176).
