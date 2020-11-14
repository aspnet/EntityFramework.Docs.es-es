---
title: Instalación de Entity Framework Core - EF Core
description: Instrucciones de instalación para Entity Framework Core
author: bricelam
ms.date: 08/06/2017
uid: core/get-started/overview/install
ms.openlocfilehash: 039e5339a6dba57f6a8f33acba6e467f8d6439c9
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431193"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="a8854-103">Instalación de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8854-103">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a8854-104">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="a8854-104">Prerequisites</span></span>

* <span data-ttu-id="a8854-105">EF Core es una biblioteca de [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="a8854-105">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="a8854-106">Por este motivo, EF Core requiere una implementación de .NET que admita .NET Standard 2.0 para poder ejecutarse.</span><span class="sxs-lookup"><span data-stu-id="a8854-106">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="a8854-107">Otras bibliotecas de .NET Standard 2.0 también pueden hacer referencia a EF Core.</span><span class="sxs-lookup"><span data-stu-id="a8854-107">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="a8854-108">Por ejemplo, puede usar EF Core para desarrollar aplicaciones que tengan como destino .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8854-108">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="a8854-109">La compilación de aplicaciones de .NET Core requiere el [SDK de .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="a8854-109">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a8854-110">También puede usar un entorno de desarrollo como [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac) o [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="a8854-110">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="a8854-111">Para obtener más información, vea [Introducción a .NET Core](/dotnet/core/get-started).</span><span class="sxs-lookup"><span data-stu-id="a8854-111">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="a8854-112">Puede usar EF Core para desarrollar aplicaciones en Windows con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a8854-112">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="a8854-113">Se recomienda usar la última versión de [Visual Studio](https://visualstudio.microsoft.com/vs).</span><span class="sxs-lookup"><span data-stu-id="a8854-113">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="a8854-114">EF Core puede ejecutarse en otras implementaciones de .NET, como [Xamarin](https://dotnet.microsoft.com/apps/xamarin) y .NET Native.</span><span class="sxs-lookup"><span data-stu-id="a8854-114">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="a8854-115">Pero en la práctica, estas implementaciones tienen limitaciones de runtime que podrían afectar el rendimiento de EF Core en su aplicación.</span><span class="sxs-lookup"><span data-stu-id="a8854-115">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="a8854-116">Para obtener más información, vea [Implementaciones de .NET compatibles con EF Core](xref:core/miscellaneous/platforms).</span><span class="sxs-lookup"><span data-stu-id="a8854-116">For more information, see [.NET implementations supported by EF Core](xref:core/miscellaneous/platforms).</span></span>

* <span data-ttu-id="a8854-117">Por último, los diferentes proveedores de bases de datos pueden requerir versiones de motores de bases de datos, implementaciones de .NET o sistemas operativos específicas.</span><span class="sxs-lookup"><span data-stu-id="a8854-117">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="a8854-118">Asegúrese de que esté disponible un [proveedor de bases de datos de EF Core](xref:core/providers/index) que admita el entorno adecuado para su aplicación.</span><span class="sxs-lookup"><span data-stu-id="a8854-118">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="a8854-119">Obtención del runtime de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8854-119">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="a8854-120">Para agregar EF Core a una aplicación, instale el paquete NuGet para el proveedor de bases de datos que quiera usar.</span><span class="sxs-lookup"><span data-stu-id="a8854-120">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="a8854-121">Si está desarrollando una aplicación de ASP.NET Core, no tendrá que instalar los proveedores en memoria ni de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a8854-121">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="a8854-122">Estos proveedores están incluidos en las versiones actuales de ASP.NET Core, junto al runtime de EF Core.</span><span class="sxs-lookup"><span data-stu-id="a8854-122">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>

<span data-ttu-id="a8854-123">Para instalar o actualizar paquetes NuGet, puede usar la interfaz de la línea de comandos (CLI) de .NET Core, o bien el cuadro de diálogo o la consola del Administrador de paquetes de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a8854-123">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="a8854-124">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="a8854-124">.NET Core CLI</span></span>

* <span data-ttu-id="a8854-125">Use el comando de la CLI de .NET Core en la línea de comandos del sistema operativo para instalar o actualizar el proveedor de SQL Server de EF Core:</span><span class="sxs-lookup"><span data-stu-id="a8854-125">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="a8854-126">Puede indicar una versión específica en el comando `dotnet add package` usando el modificador `-v`.</span><span class="sxs-lookup"><span data-stu-id="a8854-126">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="a8854-127">Por ejemplo, para instalar paquetes de EF Core 2.2.0, anexe `-v 2.2.0` al comando.</span><span class="sxs-lookup"><span data-stu-id="a8854-127">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="a8854-128">Para obtener más información, vea [Herramientas de la interfaz de la línea de comandos (CLI) de .NET Core](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="a8854-128">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="a8854-129">Cuadro de diálogo Administrador de paquetes NuGet en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8854-129">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="a8854-130">En el menú de Visual Studio, seleccione **Proyecto > Administrar paquetes NuGet**</span><span class="sxs-lookup"><span data-stu-id="a8854-130">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="a8854-131">Haga clic en la pestaña **Examinar** o **Actualizaciones**</span><span class="sxs-lookup"><span data-stu-id="a8854-131">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="a8854-132">Para instalar o actualizar el proveedor de SQL Server, seleccione el paquete `Microsoft.EntityFrameworkCore.SqlServer` y confirme la acción.</span><span class="sxs-lookup"><span data-stu-id="a8854-132">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="a8854-133">Para obtener más información, vea [Diálogo del Administrador de paquetes NuGet](/nuget/tools/package-manager-ui).</span><span class="sxs-lookup"><span data-stu-id="a8854-133">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="a8854-134">Consola del Administrador de paquetes NuGet de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8854-134">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="a8854-135">En el menú de Visual Studio, seleccione **Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="a8854-135">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="a8854-136">Para instalar el proveedor de SQL Server, ejecute el comando siguiente en la consola del Administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="a8854-136">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="a8854-137">Para actualizar el proveedor, use el comando `Update-Package`.</span><span class="sxs-lookup"><span data-stu-id="a8854-137">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="a8854-138">Para especificar una versión, use el modificador `-Version`.</span><span class="sxs-lookup"><span data-stu-id="a8854-138">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="a8854-139">Por ejemplo, para instalar paquetes de EF Core 2.2.0, anexe `-Version 2.2.0` a los comandos.</span><span class="sxs-lookup"><span data-stu-id="a8854-139">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="a8854-140">Para obtener más información, vea [Consola del Administrador de paquetes](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="a8854-140">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="a8854-141">Obtención de las herramientas de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8854-141">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="a8854-142">Puede instalar herramientas para llevar a cabo tareas relacionadas con EF Core en el proyecto, como crear y aplicar las migraciones de bases de datos o crear un modelo de EF Core basado en una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="a8854-142">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="a8854-143">Existen dos conjuntos de herramientas:</span><span class="sxs-lookup"><span data-stu-id="a8854-143">Two sets of tools are available:</span></span>

* <span data-ttu-id="a8854-144">Las [herramientas de la interfaz de la línea de comandos (CLI) de .NET Core](xref:core/cli/dotnet) pueden usarse en Windows, Linux y macOS.</span><span class="sxs-lookup"><span data-stu-id="a8854-144">The [.NET Core command-line interface (CLI) tools](xref:core/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="a8854-145">Estos comandos comienzan por `dotnet ef`.</span><span class="sxs-lookup"><span data-stu-id="a8854-145">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="a8854-146">Las [herramientas de la consola del Administrador de paquetes (PMC)](xref:core/cli/powershell) se ejecutan en Visual Studio (Windows).</span><span class="sxs-lookup"><span data-stu-id="a8854-146">The [Package Manager Console (PMC) tools](xref:core/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="a8854-147">Estos comandos empiezan por un verbo. Por ejemplo: `Add-Migration`, `Update-Database`.</span><span class="sxs-lookup"><span data-stu-id="a8854-147">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="a8854-148">Aunque puede usar los comandos de `dotnet ef` desde la consola del Administrador de paquetes, le recomendamos que use las herramientas de la consola del Administrador de paquetes en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a8854-148">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="a8854-149">Trabajan automáticamente con el proyecto actual seleccionado en la PMC de Visual Studio sin necesidad de cambiar manualmente entre directorios.</span><span class="sxs-lookup"><span data-stu-id="a8854-149">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>

* <span data-ttu-id="a8854-150">Abren automáticamente los archivos generados por los comandos de Visual Studio una vez completado el comando.</span><span class="sxs-lookup"><span data-stu-id="a8854-150">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="a8854-151">Obtención de las herramientas de la CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="a8854-151">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="a8854-152">Las herramientas de la CLI de .NET Core requieren el SDK de .NET Core, tal como se indica en [Requisitos previos](#prerequisites).</span><span class="sxs-lookup"><span data-stu-id="a8854-152">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

* <span data-ttu-id="a8854-153">`dotnet ef` debe estar instalado como herramienta global o local.</span><span class="sxs-lookup"><span data-stu-id="a8854-153">`dotnet ef` must be installed as a global or local tool.</span></span> <span data-ttu-id="a8854-154">La mayoría de los desarrolladores prefieren instalar `dotnet ef` como herramienta global con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="a8854-154">Most developers prefer installing `dotnet ef` as a global tool using the following command:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  <span data-ttu-id="a8854-155">`dotnet ef` también se puede usar como herramienta local.</span><span class="sxs-lookup"><span data-stu-id="a8854-155">`dotnet ef` can also be used as a local tool.</span></span> <span data-ttu-id="a8854-156">Para usarlo como herramienta local, restaure las dependencias de un proyecto que lo declare como dependencia de herramientas mediante un [archivo de manifiesto de herramientas](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="a8854-156">To use it as a local tool, restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

* <span data-ttu-id="a8854-157">Para actualizar las herramientas, use el comando `dotnet tool update`.</span><span class="sxs-lookup"><span data-stu-id="a8854-157">To update the tools, use the `dotnet tool update` command.</span></span>

* <span data-ttu-id="a8854-158">Instale la versión más reciente del paquete `Microsoft.EntityFrameworkCore.Design`.</span><span class="sxs-lookup"><span data-stu-id="a8854-158">Install the latest `Microsoft.EntityFrameworkCore.Design` package.</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

> [!IMPORTANT]
> <span data-ttu-id="a8854-159">Use siempre la versión del paquete de herramientas que coincida con la versión principal de los paquetes en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a8854-159">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="a8854-160">Obtención de las herramientas de la consola del Administrador de paquetes</span><span class="sxs-lookup"><span data-stu-id="a8854-160">Get the Package Manager Console tools</span></span>

<span data-ttu-id="a8854-161">Para obtener las herramientas de la consola del Administrador de paquetes para EF Core, instale el paquete `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="a8854-161">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="a8854-162">Por ejemplo, en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a8854-162">For example, from Visual Studio:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="a8854-163">Para las aplicaciones de ASP.NET Core, este paquete se incluye automáticamente.</span><span class="sxs-lookup"><span data-stu-id="a8854-163">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="a8854-164">Actualización a la versión más reciente de EF Core</span><span class="sxs-lookup"><span data-stu-id="a8854-164">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="a8854-165">Cuando publicamos una nueva versión de EF Core, también publicamos una nueva versión de los proveedores que forman parte del proyecto de EF Core, como, por ejemplo: Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite y Microsoft.EntityFrameworkCore.InMemory.</span><span class="sxs-lookup"><span data-stu-id="a8854-165">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="a8854-166">Para obtener todas las mejoras, solo tiene que actualizar a la nueva versión del proveedor.</span><span class="sxs-lookup"><span data-stu-id="a8854-166">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="a8854-167">EF Core y los proveedores de SQL Server y en memoria están incluidos en las versiones actuales de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8854-167">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="a8854-168">Para actualizar una aplicación de ASP.NET Core existente a una versión más reciente de EF Core, actualice siempre la versión de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8854-168">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="a8854-169">Si necesita actualizar una aplicación que usa un proveedor de base de datos de terceros, busque siempre una actualización del proveedor que sea compatible con la versión de EF Core que quiere usar.</span><span class="sxs-lookup"><span data-stu-id="a8854-169">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="a8854-170">Por ejemplo, los proveedores de bases de datos de la versión 1.0 no son compatibles con la versión 2.0 del entorno de ejecución de EF Core.</span><span class="sxs-lookup"><span data-stu-id="a8854-170">For example, database providers for version 1.0 are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="a8854-171">Los proveedores de terceros de EF Core no suelen publicar versiones de revisión junto al runtime de EF Core.</span><span class="sxs-lookup"><span data-stu-id="a8854-171">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="a8854-172">Para actualizar una aplicación que use un proveedor de terceros a una versión de revisión de EF Core, puede que deba agregar una referencia directa a determinados componentes de runtime de EF Core, como Microsoft.EntityFrameworkCore o Microsoft.EntityFrameworkCore.Relational.</span><span class="sxs-lookup"><span data-stu-id="a8854-172">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>