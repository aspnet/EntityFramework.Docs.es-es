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
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="4196e-102">Actualización de EF Core 1,0 RC1 a 1,0 RC2</span><span class="sxs-lookup"><span data-stu-id="4196e-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="4196e-103">En este artículo se proporcionan instrucciones para mover una aplicación compilada con paquetes RC1 a RC2.</span><span class="sxs-lookup"><span data-stu-id="4196e-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="4196e-104">Nombres y versiones de los paquetes</span><span class="sxs-lookup"><span data-stu-id="4196e-104">Package Names and Versions</span></span>

<span data-ttu-id="4196e-105">Entre RC1 y RC2, cambiamos de "Entity Framework 7" a "Entity Framework Core".</span><span class="sxs-lookup"><span data-stu-id="4196e-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="4196e-106">Puede leer más sobre los motivos del cambio en [esta publicación de Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span><span class="sxs-lookup"><span data-stu-id="4196e-106">You can read more about the reasons for the change in [this post by Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="4196e-107">Debido a este cambio, los nombres de los paquetes cambiaron de `EntityFramework.*` a `Microsoft.EntityFrameworkCore.*` y nuestras versiones de `7.0.0-rc1-final` a `1.0.0-rc2-final` (o `1.0.0-preview1-final` para las herramientas).</span><span class="sxs-lookup"><span data-stu-id="4196e-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="4196e-108">**Tendrá que quitar por completo los paquetes RC1 y, a continuación, instalar los RC2.**</span><span class="sxs-lookup"><span data-stu-id="4196e-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="4196e-109">Esta es la asignación para algunos paquetes comunes.</span><span class="sxs-lookup"><span data-stu-id="4196e-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="4196e-110">Paquete RC1</span><span class="sxs-lookup"><span data-stu-id="4196e-110">RC1 Package</span></span>                                               | <span data-ttu-id="4196e-111">Equivalentes de RC2</span><span class="sxs-lookup"><span data-stu-id="4196e-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="4196e-112">EntityFramework. MicrosoftSqlServer 7.0.0-RC1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="4196e-113">Microsoft. EntityFrameworkCore. SqlServer 1.0.0-RC2-final</span><span class="sxs-lookup"><span data-stu-id="4196e-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4196e-114">EntityFramework. SQLite 7.0.0-RC1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="4196e-115">Microsoft. EntityFrameworkCore. SQLite 1.0.0-RC2-final</span><span class="sxs-lookup"><span data-stu-id="4196e-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4196e-116">EntityFramework7. Npgsql 3.1.0-RC1-3</span><span class="sxs-lookup"><span data-stu-id="4196e-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="4196e-117">NpgSql. EntityFrameworkCore. Postgres<to be advised></span><span class="sxs-lookup"><span data-stu-id="4196e-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="4196e-118">EntityFramework. SqlServerCompact35 7.0.0-RC1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="4196e-119">EntityFrameworkCore. SqlServerCompact35 1.0.0-RC2-final</span><span class="sxs-lookup"><span data-stu-id="4196e-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4196e-120">EntityFramework. SqlServerCompact40 7.0.0-RC1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="4196e-121">EntityFrameworkCore. SqlServerCompact40 1.0.0-RC2-final</span><span class="sxs-lookup"><span data-stu-id="4196e-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4196e-122">EntityFramework. inmemory 7.0.0-RC1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="4196e-123">Microsoft. EntityFrameworkCore. inmemory 1.0.0-RC2-final</span><span class="sxs-lookup"><span data-stu-id="4196e-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4196e-124">EntityFramework. IBMDataServer 7.0.0-beta1</span><span class="sxs-lookup"><span data-stu-id="4196e-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="4196e-125">Todavía no está disponible para RC2</span><span class="sxs-lookup"><span data-stu-id="4196e-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="4196e-126">EntityFramework. Commands 7.0.0-RC1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="4196e-127">Microsoft. EntityFrameworkCore. Tools 1.0.0-preview1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="4196e-128">EntityFramework. MicrosoftSqlServer. Design 7.0.0-RC1-final</span><span class="sxs-lookup"><span data-stu-id="4196e-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="4196e-129">Microsoft. EntityFrameworkCore. SqlServer. Design 1.0.0-RC2-final</span><span class="sxs-lookup"><span data-stu-id="4196e-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="4196e-130">Espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="4196e-130">Namespaces</span></span>

<span data-ttu-id="4196e-131">Junto con los nombres de paquete, los espacios de nombres cambiaron de `Microsoft.Data.Entity.*` a `Microsoft.EntityFrameworkCore.*` .</span><span class="sxs-lookup"><span data-stu-id="4196e-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="4196e-132">Puede controlar este cambio con la búsqueda y el reemplazo de `using Microsoft.Data.Entity` con `using Microsoft.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="4196e-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="4196e-133">Cambios en la Convención de nomenclatura de tablas</span><span class="sxs-lookup"><span data-stu-id="4196e-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="4196e-134">Un cambio funcional significativo que se llevó a cabo en RC2 era usar el nombre de la `DbSet<TEntity>` propiedad para una entidad determinada como el nombre de tabla al que se asigna, en lugar de simplemente el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="4196e-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="4196e-135">Puede leer más sobre este cambio en [el problema del anuncio relacionado](https://github.com/aspnet/Announcements/issues/167).</span><span class="sxs-lookup"><span data-stu-id="4196e-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="4196e-136">En el caso de las aplicaciones RC1 existentes, se recomienda agregar el código siguiente al inicio del `OnModelCreating` método para mantener la estrategia de nomenclatura RC1:</span><span class="sxs-lookup"><span data-stu-id="4196e-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="4196e-137">Si desea adoptar la nueva estrategia de nomenclatura, se recomienda completar correctamente el resto de los pasos de actualización y, a continuación, quitar el código y crear una migración para aplicar el cambio de nombre de la tabla.</span><span class="sxs-lookup"><span data-stu-id="4196e-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="4196e-138">AddDbContext/Startup.cs cambia (solo proyectos de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4196e-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="4196e-139">En RC1, tenía que agregar Entity Framework servicios al proveedor de servicios de aplicaciones `Startup.ConfigureServices(...)` :</span><span class="sxs-lookup"><span data-stu-id="4196e-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="4196e-140">En RC2, puede quitar las llamadas a `AddEntityFramework()` , `AddSqlServer()` , etc.:</span><span class="sxs-lookup"><span data-stu-id="4196e-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="4196e-141">También debe agregar un constructor, al contexto derivado, que toma las opciones de contexto y las pasa al constructor base.</span><span class="sxs-lookup"><span data-stu-id="4196e-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="4196e-142">Esto es necesario porque hemos quitado algunas de las ideas mágicas que snuck en segundo plano:</span><span class="sxs-lookup"><span data-stu-id="4196e-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="4196e-143">Pasar un IServiceProvider</span><span class="sxs-lookup"><span data-stu-id="4196e-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="4196e-144">Si tiene código RC1 que pasa un `IServiceProvider` al contexto, ahora se ha trasladado a `DbContextOptions` , en lugar de ser un parámetro de constructor independiente.</span><span class="sxs-lookup"><span data-stu-id="4196e-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="4196e-145">Utilice `DbContextOptionsBuilder.UseInternalServiceProvider(...)` para establecer el proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="4196e-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="4196e-146">Prueba</span><span class="sxs-lookup"><span data-stu-id="4196e-146">Testing</span></span>

<span data-ttu-id="4196e-147">El escenario más común para hacerlo era controlar el ámbito de una base de datos inmemory al realizar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="4196e-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="4196e-148">Vea el artículo sobre las [pruebas](testing/index.md) actualizadas para obtener un ejemplo de cómo hacerlo con RC2.</span><span class="sxs-lookup"><span data-stu-id="4196e-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="4196e-149">Resolver servicios internos desde el proveedor de servicios de aplicación (solo proyectos de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4196e-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="4196e-150">Si tiene una aplicación ASP.NET Core y desea que EF resuelva los servicios internos del proveedor de servicios de aplicación, hay una sobrecarga de `AddDbContext` que le permite configurar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4196e-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> <span data-ttu-id="4196e-151">Se recomienda permitir que EF administre internamente sus propios servicios, a menos que tenga un motivo para combinar los servicios de EF internos en el proveedor de servicios de aplicación.</span><span class="sxs-lookup"><span data-stu-id="4196e-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="4196e-152">La razón principal por la que puede querer hacer esto es usar el proveedor de servicios de aplicación para reemplazar los servicios que EF usa internamente</span><span class="sxs-lookup"><span data-stu-id="4196e-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a><span data-ttu-id="4196e-153">Comandos de DNX = CLI de .NET Core de> (solo proyectos de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4196e-153">DNX Commands => .NET Core CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="4196e-154">Si anteriormente usó los `dnx ef` comandos para los proyectos de ASP.net 5, ahora se han pasado a `dotnet ef` comandos.</span><span class="sxs-lookup"><span data-stu-id="4196e-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="4196e-155">Todavía se aplica la misma sintaxis de comando.</span><span class="sxs-lookup"><span data-stu-id="4196e-155">The same command syntax still applies.</span></span> <span data-ttu-id="4196e-156">Puede usar `dotnet ef --help` para obtener información sobre la sintaxis.</span><span class="sxs-lookup"><span data-stu-id="4196e-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="4196e-157">La forma en que se registran los comandos ha cambiado en RC2, debido a que DNX se ha reemplazado por el CLI de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4196e-157">The way commands are registered has changed in RC2, due to DNX being replaced by the .NET Core CLI.</span></span> <span data-ttu-id="4196e-158">Los comandos se registran ahora en una `tools` sección de `project.json` :</span><span class="sxs-lookup"><span data-stu-id="4196e-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

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
> <span data-ttu-id="4196e-159">Si usa Visual Studio, ahora puede usar la consola del administrador de paquetes para ejecutar comandos EF para proyectos de ASP.NET Core (esto no se admitía en RC1).</span><span class="sxs-lookup"><span data-stu-id="4196e-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="4196e-160">Todavía tiene que registrar los comandos en la `tools` sección de `project.json` para hacerlo.</span><span class="sxs-lookup"><span data-stu-id="4196e-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="4196e-161">Los comandos del administrador de paquetes requieren PowerShell 5</span><span class="sxs-lookup"><span data-stu-id="4196e-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="4196e-162">Si usa los comandos Entity Framework en la consola del administrador de paquetes en Visual Studio, tendrá que asegurarse de que tiene PowerShell 5 instalado.</span><span class="sxs-lookup"><span data-stu-id="4196e-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="4196e-163">Se trata de un requisito temporal que se quitará en la siguiente versión (consulte el [problema #5327](https://github.com/aspnet/EntityFramework/issues/5327) para obtener más detalles).</span><span class="sxs-lookup"><span data-stu-id="4196e-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="4196e-164">Usar "Imports" en project.js</span><span class="sxs-lookup"><span data-stu-id="4196e-164">Using "imports" in project.json</span></span>

<span data-ttu-id="4196e-165">Algunas de las dependencias de EF Core no admiten aún .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="4196e-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="4196e-166">EF Core en .NET Standard y los proyectos de .NET Core pueden requerir la adición de "Imports" a project.jscomo solución temporal.</span><span class="sxs-lookup"><span data-stu-id="4196e-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="4196e-167">Al agregar EF, la restauración de NuGet mostrará este mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="4196e-167">When adding EF, NuGet restore will display this error message:</span></span>

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

<span data-ttu-id="4196e-168">La solución consiste en importar manualmente el perfil portátil "portable-net451 + win8".</span><span class="sxs-lookup"><span data-stu-id="4196e-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="4196e-169">Esto obliga a NuGet a tratar estos binarios que coinciden con este proporcionado como un marco compatible con .NET Standard, aunque no lo sean.</span><span class="sxs-lookup"><span data-stu-id="4196e-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="4196e-170">Aunque "portable-net451 + win8" no es 100% compatible con .NET Standard, es lo suficientemente compatible para la transición de PCL a .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="4196e-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="4196e-171">Las importaciones se pueden quitar cuando las dependencias de EF finalmente se actualizan a .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="4196e-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="4196e-172">Se pueden agregar varios marcos a "Imports" en la sintaxis de la matriz.</span><span class="sxs-lookup"><span data-stu-id="4196e-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="4196e-173">Otras importaciones pueden ser necesarias si agrega bibliotecas adicionales al proyecto.</span><span class="sxs-lookup"><span data-stu-id="4196e-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="4196e-174">Vea el [problema #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span><span class="sxs-lookup"><span data-stu-id="4196e-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
