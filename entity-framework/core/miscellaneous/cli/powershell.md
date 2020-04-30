---
title: Referencia de herramientas de EF Core (consola del administrador de paquetes)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 0676475d46a8d21dee7bd10e25dd273a11e96ac3
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538401"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="5581f-102">Referencia de herramientas de Entity Framework Core: consola del administrador de paquetes en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5581f-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="5581f-103">Las herramientas de la consola del administrador de paquetes (PMC) para Entity Framework Core realizar tareas de desarrollo en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="5581f-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="5581f-104">Por ejemplo, se crean [migraciones](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), se aplican migraciones y se genera código para un modelo basado en una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="5581f-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="5581f-105">Los comandos se ejecutan dentro de Visual Studio mediante la [consola del administrador de paquetes](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="5581f-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="5581f-106">Estas herramientas funcionan con proyectos de .NET Framework y .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5581f-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="5581f-107">Si no usa Visual Studio, se recomienda usar en su lugar las [herramientas de línea de comandos de EF Core](dotnet.md) .</span><span class="sxs-lookup"><span data-stu-id="5581f-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="5581f-108">Las herramientas de la CLI son multiplataforma y se ejecutan en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="5581f-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="5581f-109">Instalación de las herramientas</span><span class="sxs-lookup"><span data-stu-id="5581f-109">Installing the tools</span></span>

<span data-ttu-id="5581f-110">Los procedimientos para instalar y actualizar las herramientas difieren entre ASP.NET Core 2.1 + y versiones anteriores u otros tipos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="5581f-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="5581f-111">ASP.NET Core versión 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="5581f-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="5581f-112">Las herramientas se incluyen automáticamente en un proyecto ASP.NET Core 2.1 + porque el `Microsoft.EntityFrameworkCore.Tools` paquete se incluye en el [metapaquete Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5581f-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5581f-113">Por lo tanto, no tiene que hacer nada para instalar las herramientas, pero tiene que:</span><span class="sxs-lookup"><span data-stu-id="5581f-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="5581f-114">Restaure los paquetes antes de usar las herramientas en un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="5581f-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="5581f-115">Instale un paquete para actualizar las herramientas a una versión más reciente.</span><span class="sxs-lookup"><span data-stu-id="5581f-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="5581f-116">Para asegurarse de que está obteniendo la versión más reciente de las herramientas, se recomienda que realice también el siguiente paso:</span><span class="sxs-lookup"><span data-stu-id="5581f-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="5581f-117">Edite el archivo *. csproj* y agregue una línea que especifique la versión más reciente del paquete [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="5581f-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="5581f-118">Por ejemplo, el archivo *. csproj* podría incluir un `ItemGroup` que tiene el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="5581f-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="5581f-119">Actualice las herramientas cuando reciba un mensaje similar al del ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5581f-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="5581f-120">La versión de herramientas de EF Core ' 2.1.1-RTM-30846 ' es anterior a la del runtime ' 2.1.3-RTM-32065 '.</span><span class="sxs-lookup"><span data-stu-id="5581f-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="5581f-121">Actualice las herramientas para las últimas características y correcciones de errores.</span><span class="sxs-lookup"><span data-stu-id="5581f-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="5581f-122">Para actualizar las herramientas:</span><span class="sxs-lookup"><span data-stu-id="5581f-122">To update the tools:</span></span>

* <span data-ttu-id="5581f-123">Instale la versión más reciente del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5581f-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="5581f-124">Actualice Visual Studio a la última versión.</span><span class="sxs-lookup"><span data-stu-id="5581f-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="5581f-125">Edite el archivo *. csproj* para que incluya una referencia de paquete al paquete de herramientas más reciente, como se mostró anteriormente.</span><span class="sxs-lookup"><span data-stu-id="5581f-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="5581f-126">Otras versiones y tipos de proyecto</span><span class="sxs-lookup"><span data-stu-id="5581f-126">Other versions and project types</span></span>

<span data-ttu-id="5581f-127">Instale las herramientas de la consola del administrador de paquetes ejecutando el siguiente comando en la **consola del administrador de paquetes**:</span><span class="sxs-lookup"><span data-stu-id="5581f-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="5581f-128">Actualice las herramientas ejecutando el siguiente comando en la **consola del administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="5581f-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="5581f-129">Comprobación de la instalación</span><span class="sxs-lookup"><span data-stu-id="5581f-129">Verify the installation</span></span>

<span data-ttu-id="5581f-130">Ejecute este comando para comprobar que las herramientas están instaladas:</span><span class="sxs-lookup"><span data-stu-id="5581f-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="5581f-131">La salida tiene el siguiente aspecto (no indica qué versión de las herramientas está usando):</span><span class="sxs-lookup"><span data-stu-id="5581f-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a><span data-ttu-id="5581f-132">Uso de las herramientas</span><span class="sxs-lookup"><span data-stu-id="5581f-132">Using the tools</span></span>

<span data-ttu-id="5581f-133">Antes de usar las herramientas:</span><span class="sxs-lookup"><span data-stu-id="5581f-133">Before using the tools:</span></span>

* <span data-ttu-id="5581f-134">Comprenda la diferencia entre el proyecto de destino y el de inicio.</span><span class="sxs-lookup"><span data-stu-id="5581f-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="5581f-135">Aprenda a usar las herramientas con .NET Standard bibliotecas de clases.</span><span class="sxs-lookup"><span data-stu-id="5581f-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="5581f-136">En el caso de los proyectos de ASP.NET Core, establezca el entorno.</span><span class="sxs-lookup"><span data-stu-id="5581f-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="5581f-137">Proyecto de destino e inicio</span><span class="sxs-lookup"><span data-stu-id="5581f-137">Target and startup project</span></span>

<span data-ttu-id="5581f-138">Los comandos hacen referencia a un *proyecto* y un *proyecto de inicio*.</span><span class="sxs-lookup"><span data-stu-id="5581f-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="5581f-139">El *proyecto* también se conoce como *proyecto de destino* porque es donde los comandos agregan o quitan archivos.</span><span class="sxs-lookup"><span data-stu-id="5581f-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="5581f-140">De forma predeterminada, el proyecto **predeterminado** seleccionado en la **consola del administrador de paquetes** es el proyecto de destino.</span><span class="sxs-lookup"><span data-stu-id="5581f-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="5581f-141">Puede especificar otro proyecto como proyecto de destino mediante la <nobr>`--project`</nobr> opción.</span><span class="sxs-lookup"><span data-stu-id="5581f-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="5581f-142">El *proyecto de inicio* es el que las herramientas compilan y ejecutan.</span><span class="sxs-lookup"><span data-stu-id="5581f-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="5581f-143">Las herramientas tienen que ejecutar código de aplicación en tiempo de diseño para obtener información sobre el proyecto, como la cadena de conexión a la base de datos y la configuración del modelo.</span><span class="sxs-lookup"><span data-stu-id="5581f-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="5581f-144">De forma predeterminada, el **proyecto de inicio** en **Explorador de soluciones** es el proyecto de inicio.</span><span class="sxs-lookup"><span data-stu-id="5581f-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="5581f-145">Puede especificar otro proyecto como proyecto de inicio mediante la <nobr>`--startup-project`</nobr> opción.</span><span class="sxs-lookup"><span data-stu-id="5581f-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="5581f-146">El proyecto de inicio y el proyecto de destino suelen ser el mismo proyecto.</span><span class="sxs-lookup"><span data-stu-id="5581f-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="5581f-147">Un escenario típico en el que se trata de proyectos independientes es cuando:</span><span class="sxs-lookup"><span data-stu-id="5581f-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="5581f-148">El contexto de EF Core y las clases de entidad se encuentran en una biblioteca de clases de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5581f-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="5581f-149">Una aplicación de consola de .NET Core o una aplicación web hace referencia a la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="5581f-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="5581f-150">También es posible [colocar el código de las migraciones en una biblioteca de clases independiente del contexto de EF Core](xref:core/managing-schemas/migrations/projects).</span><span class="sxs-lookup"><span data-stu-id="5581f-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="5581f-151">Otras plataformas de destino</span><span class="sxs-lookup"><span data-stu-id="5581f-151">Other target frameworks</span></span>

<span data-ttu-id="5581f-152">Las herramientas de la consola del administrador de paquetes funcionan con proyectos de .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5581f-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="5581f-153">Es posible que las aplicaciones que tienen el modelo de EF Core en una biblioteca de clases .NET Standard no tengan un proyecto de .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5581f-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="5581f-154">Por ejemplo, esto es cierto para las aplicaciones Xamarin y Plataforma universal de Windows.</span><span class="sxs-lookup"><span data-stu-id="5581f-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="5581f-155">En tales casos, puede crear un proyecto de aplicación de consola de .NET Core o .NET Framework cuyo único propósito es actuar como proyecto de inicio para las herramientas.</span><span class="sxs-lookup"><span data-stu-id="5581f-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="5581f-156">El proyecto puede ser un proyecto ficticio sin código &mdash; real, solo es necesario para proporcionar un destino para las herramientas.</span><span class="sxs-lookup"><span data-stu-id="5581f-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="5581f-157">¿Por qué es necesario un proyecto ficticio?</span><span class="sxs-lookup"><span data-stu-id="5581f-157">Why is a dummy project required?</span></span> <span data-ttu-id="5581f-158">Como se mencionó anteriormente, las herramientas tienen que ejecutar código de aplicación en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="5581f-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="5581f-159">Para ello, deben usar .NET Core o .NET Framework Runtime.</span><span class="sxs-lookup"><span data-stu-id="5581f-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="5581f-160">Cuando el modelo de EF Core está en un proyecto que tiene como destino .NET Core o .NET Framework, las herramientas de EF Core toman prestado el tiempo de ejecución del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5581f-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="5581f-161">No pueden hacerlo si el modelo de EF Core está en una biblioteca de clases .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="5581f-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="5581f-162">El .NET Standard no es una implementación real de .NET; es una especificación de un conjunto de API que las implementaciones de .NET deben admitir.</span><span class="sxs-lookup"><span data-stu-id="5581f-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="5581f-163">Por lo tanto .NET Standard no es suficiente para que las herramientas de EF Core ejecuten código de aplicación.</span><span class="sxs-lookup"><span data-stu-id="5581f-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="5581f-164">El proyecto ficticio que cree para usarlo como proyecto de inicio proporciona una plataforma de destino concreta en la que las herramientas pueden cargar la biblioteca de clases de .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="5581f-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="5581f-165">Entorno de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5581f-165">ASP.NET Core environment</span></span>

<span data-ttu-id="5581f-166">Para especificar el entorno de ASP.NET Core proyectos, establezca **env: ASPNETCORE_ENVIRONMENT** antes de ejecutar los comandos.</span><span class="sxs-lookup"><span data-stu-id="5581f-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="5581f-167">Parámetros comunes</span><span class="sxs-lookup"><span data-stu-id="5581f-167">Common parameters</span></span>

<span data-ttu-id="5581f-168">En la tabla siguiente se muestran los parámetros que son comunes a todos los comandos EF Core:</span><span class="sxs-lookup"><span data-stu-id="5581f-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="5581f-169">Parámetro</span><span class="sxs-lookup"><span data-stu-id="5581f-169">Parameter</span></span>                 | <span data-ttu-id="5581f-170">Descripción</span><span class="sxs-lookup"><span data-stu-id="5581f-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5581f-171">-Cadena \<de contexto></span><span class="sxs-lookup"><span data-stu-id="5581f-171">-Context \<String></span></span>        | <span data-ttu-id="5581f-172">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="5581f-172">The `DbContext` class to use.</span></span> <span data-ttu-id="5581f-173">Nombre de clase solo o completo con espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="5581f-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="5581f-174">Si se omite este parámetro, EF Core encuentra la clase de contexto.</span><span class="sxs-lookup"><span data-stu-id="5581f-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="5581f-175">Si hay varias clases de contexto, este parámetro es obligatorio.</span><span class="sxs-lookup"><span data-stu-id="5581f-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="5581f-176">-> \<de la cadena de proyecto</span><span class="sxs-lookup"><span data-stu-id="5581f-176">-Project \<String></span></span>        | <span data-ttu-id="5581f-177">Proyecto de destino.</span><span class="sxs-lookup"><span data-stu-id="5581f-177">The target project.</span></span> <span data-ttu-id="5581f-178">Si se omite este parámetro, el **proyecto predeterminado** de la **consola del administrador de paquetes** se utiliza como proyecto de destino.</span><span class="sxs-lookup"><span data-stu-id="5581f-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="5581f-179"><nobr>-Proyecto</nobr> \<cadena></span><span class="sxs-lookup"><span data-stu-id="5581f-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="5581f-180">Proyecto de inicio.</span><span class="sxs-lookup"><span data-stu-id="5581f-180">The startup project.</span></span> <span data-ttu-id="5581f-181">Si se omite este parámetro, el **proyecto de inicio** de **las propiedades** de la solución se usa como proyecto de destino.</span><span class="sxs-lookup"><span data-stu-id="5581f-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="5581f-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="5581f-182">-Verbose</span></span>                  | <span data-ttu-id="5581f-183">Mostrar resultado detallado.</span><span class="sxs-lookup"><span data-stu-id="5581f-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="5581f-184">Para mostrar información de ayuda sobre un comando, use el `Get-Help` comando de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5581f-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="5581f-185">Los parámetros context, Project y proyecto admiten la expansión de pestañas.</span><span class="sxs-lookup"><span data-stu-id="5581f-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="5581f-186">Agregar-migración</span><span class="sxs-lookup"><span data-stu-id="5581f-186">Add-Migration</span></span>

<span data-ttu-id="5581f-187">Agrega una nueva migración.</span><span class="sxs-lookup"><span data-stu-id="5581f-187">Adds a new migration.</span></span>

<span data-ttu-id="5581f-188">Parámetros:</span><span class="sxs-lookup"><span data-stu-id="5581f-188">Parameters:</span></span>

| <span data-ttu-id="5581f-189">Parámetro</span><span class="sxs-lookup"><span data-stu-id="5581f-189">Parameter</span></span>                         | <span data-ttu-id="5581f-190">Descripción</span><span class="sxs-lookup"><span data-stu-id="5581f-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5581f-191"><nobr>-Nombre \<de cadena><nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="5581f-192">El nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="5581f-192">The name of the migration.</span></span> <span data-ttu-id="5581f-193">Este es un parámetro posicional y es obligatorio.</span><span class="sxs-lookup"><span data-stu-id="5581f-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="5581f-194"><nobr>-OutputDir \<cadena></nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="5581f-195">El directorio que se usa para generar los archivos.</span><span class="sxs-lookup"><span data-stu-id="5581f-195">The directory use to output the files.</span></span> <span data-ttu-id="5581f-196">Las rutas de acceso son relativas al directorio del proyecto de destino.</span><span class="sxs-lookup"><span data-stu-id="5581f-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="5581f-197">El valor predeterminado es "migraciones".</span><span class="sxs-lookup"><span data-stu-id="5581f-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="5581f-198"><nobr>->\<de cadena de espacio de nombres</nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="5581f-199">Espacio de nombres que se va a usar para las clases generadas.</span><span class="sxs-lookup"><span data-stu-id="5581f-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="5581f-200">De forma predeterminada, se genera desde el directorio de salida.</span><span class="sxs-lookup"><span data-stu-id="5581f-200">Defaults to generated from the output directory.</span></span> |

## <a name="drop-database"></a><span data-ttu-id="5581f-201">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="5581f-201">Drop-Database</span></span>

<span data-ttu-id="5581f-202">Quita la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5581f-202">Drops the database.</span></span>

<span data-ttu-id="5581f-203">Parámetros:</span><span class="sxs-lookup"><span data-stu-id="5581f-203">Parameters:</span></span>

| <span data-ttu-id="5581f-204">Parámetro</span><span class="sxs-lookup"><span data-stu-id="5581f-204">Parameter</span></span> | <span data-ttu-id="5581f-205">Descripción</span><span class="sxs-lookup"><span data-stu-id="5581f-205">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="5581f-206">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="5581f-206">-WhatIf</span></span>   | <span data-ttu-id="5581f-207">Mostrar la base de datos que se va a quitar, pero no quitarla.</span><span class="sxs-lookup"><span data-stu-id="5581f-207">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="5581f-208">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="5581f-208">Get-DbContext</span></span>

<span data-ttu-id="5581f-209">Obtiene información sobre un `DbContext` tipo.</span><span class="sxs-lookup"><span data-stu-id="5581f-209">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="5581f-210">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="5581f-210">Remove-Migration</span></span>

<span data-ttu-id="5581f-211">Quita la última migración (revierte los cambios de código que se realizaron para la migración).</span><span class="sxs-lookup"><span data-stu-id="5581f-211">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="5581f-212">Parámetros:</span><span class="sxs-lookup"><span data-stu-id="5581f-212">Parameters:</span></span>

| <span data-ttu-id="5581f-213">Parámetro</span><span class="sxs-lookup"><span data-stu-id="5581f-213">Parameter</span></span> | <span data-ttu-id="5581f-214">Descripción</span><span class="sxs-lookup"><span data-stu-id="5581f-214">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="5581f-215">-Force</span><span class="sxs-lookup"><span data-stu-id="5581f-215">-Force</span></span>    | <span data-ttu-id="5581f-216">Revertir la migración (revertir los cambios que se aplicaron a la base de datos).</span><span class="sxs-lookup"><span data-stu-id="5581f-216">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="5581f-217">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="5581f-217">Scaffold-DbContext</span></span>

<span data-ttu-id="5581f-218">Genera código para los `DbContext` tipos de entidad y para una base de datos.</span><span class="sxs-lookup"><span data-stu-id="5581f-218">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="5581f-219">Para `Scaffold-DbContext` que genere un tipo de entidad, la tabla de base de datos debe tener una clave principal.</span><span class="sxs-lookup"><span data-stu-id="5581f-219">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="5581f-220">Parámetros:</span><span class="sxs-lookup"><span data-stu-id="5581f-220">Parameters:</span></span>

| <span data-ttu-id="5581f-221">Parámetro</span><span class="sxs-lookup"><span data-stu-id="5581f-221">Parameter</span></span>                          | <span data-ttu-id="5581f-222">Descripción</span><span class="sxs-lookup"><span data-stu-id="5581f-222">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5581f-223"><nobr>:>\<de la cadena de conexión</nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-223"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="5581f-224">La cadena de conexión a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5581f-224">The connection string to the database.</span></span> <span data-ttu-id="5581f-225">En el caso de los proyectos de ASP.NET Core 2. x, el valor puede ser *name =\<nombre de la cadena de conexión>*.</span><span class="sxs-lookup"><span data-stu-id="5581f-225">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="5581f-226">En ese caso, el nombre procede de los orígenes de configuración que se configuran para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5581f-226">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="5581f-227">Este es un parámetro posicional y es obligatorio.</span><span class="sxs-lookup"><span data-stu-id="5581f-227">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="5581f-228"><nobr>->\<de cadena del proveedor</nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-228"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="5581f-229">Proveedor que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="5581f-229">The provider to use.</span></span> <span data-ttu-id="5581f-230">Normalmente, es el nombre del paquete NuGet, por ejemplo: `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="5581f-230">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="5581f-231">Este es un parámetro posicional y es obligatorio.</span><span class="sxs-lookup"><span data-stu-id="5581f-231">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="5581f-232">-OutputDir \<cadena></span><span class="sxs-lookup"><span data-stu-id="5581f-232">-OutputDir \<String></span></span>               | <span data-ttu-id="5581f-233">Directorio en el que se colocarán los archivos.</span><span class="sxs-lookup"><span data-stu-id="5581f-233">The directory to put files in.</span></span> <span data-ttu-id="5581f-234">Las rutas de acceso son relativas al directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5581f-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="5581f-235">-ContextDir \<cadena></span><span class="sxs-lookup"><span data-stu-id="5581f-235">-ContextDir \<String></span></span>              | <span data-ttu-id="5581f-236">Directorio en el que se `DbContext` va a colocar el archivo.</span><span class="sxs-lookup"><span data-stu-id="5581f-236">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="5581f-237">Las rutas de acceso son relativas al directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5581f-237">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="5581f-238">-> \<de cadena de espacio de nombres</span><span class="sxs-lookup"><span data-stu-id="5581f-238">-Namespace \<String></span></span>               | <span data-ttu-id="5581f-239">Espacio de nombres que se va a usar para todas las clases generadas.</span><span class="sxs-lookup"><span data-stu-id="5581f-239">The namespace to use for all generated classes.</span></span> <span data-ttu-id="5581f-240">De forma predeterminada, se genera a partir del espacio de nombres raíz y el directorio de salida.</span><span class="sxs-lookup"><span data-stu-id="5581f-240">Defaults to generated from the root namespace and the output directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="5581f-241">-ContextNamespace \<cadena></span><span class="sxs-lookup"><span data-stu-id="5581f-241">-ContextNamespace \<String></span></span>        | <span data-ttu-id="5581f-242">Espacio de nombres que se va a `DbContext` utilizar para la clase generada.</span><span class="sxs-lookup"><span data-stu-id="5581f-242">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="5581f-243">Nota: invalida `-Namespace`.</span><span class="sxs-lookup"><span data-stu-id="5581f-243">Note: overrides `-Namespace`.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="5581f-244">-Cadena \<de contexto></span><span class="sxs-lookup"><span data-stu-id="5581f-244">-Context \<String></span></span>                 | <span data-ttu-id="5581f-245">Nombre de la `DbContext` clase que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="5581f-245">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="5581f-246">-Schemas \<cadena [] ></span><span class="sxs-lookup"><span data-stu-id="5581f-246">-Schemas \<String[]></span></span>               | <span data-ttu-id="5581f-247">Esquemas de las tablas para las que se van a generar tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="5581f-247">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="5581f-248">Si se omite este parámetro, se incluyen todos los esquemas.</span><span class="sxs-lookup"><span data-stu-id="5581f-248">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="5581f-249">-Tables \<String [] ></span><span class="sxs-lookup"><span data-stu-id="5581f-249">-Tables \<String[]></span></span>                | <span data-ttu-id="5581f-250">Tablas para las que se van a generar tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="5581f-250">The tables to generate entity types for.</span></span> <span data-ttu-id="5581f-251">Si se omite este parámetro, se incluyen todas las tablas.</span><span class="sxs-lookup"><span data-stu-id="5581f-251">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="5581f-252">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="5581f-252">-DataAnnotations</span></span>                   | <span data-ttu-id="5581f-253">Use los atributos para configurar el modelo (siempre que sea posible).</span><span class="sxs-lookup"><span data-stu-id="5581f-253">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="5581f-254">Si se omite este parámetro, solo se usa la API fluida.</span><span class="sxs-lookup"><span data-stu-id="5581f-254">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="5581f-255">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="5581f-255">-UseDatabaseNames</span></span>                  | <span data-ttu-id="5581f-256">Utilice nombres de tabla y columna exactamente como aparecen en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5581f-256">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="5581f-257">Si se omite este parámetro, los nombres de base de datos se cambian para ajustarse mejor a las convenciones de estilo de nombre de C#.</span><span class="sxs-lookup"><span data-stu-id="5581f-257">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="5581f-258">-Force</span><span class="sxs-lookup"><span data-stu-id="5581f-258">-Force</span></span>                             | <span data-ttu-id="5581f-259">Sobrescribe los archivos existentes.</span><span class="sxs-lookup"><span data-stu-id="5581f-259">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="5581f-260">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5581f-260">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="5581f-261">Ejemplo que scaffolding solo selecciona tablas y crea el contexto en una carpeta independiente con un nombre y un espacio de nombres especificados:</span><span class="sxs-lookup"><span data-stu-id="5581f-261">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="5581f-262">Script: migración</span><span class="sxs-lookup"><span data-stu-id="5581f-262">Script-Migration</span></span>

<span data-ttu-id="5581f-263">Genera un script SQL que aplica todos los cambios de una migración seleccionada a otra migración seleccionada.</span><span class="sxs-lookup"><span data-stu-id="5581f-263">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="5581f-264">Parámetros:</span><span class="sxs-lookup"><span data-stu-id="5581f-264">Parameters:</span></span>

| <span data-ttu-id="5581f-265">Parámetro</span><span class="sxs-lookup"><span data-stu-id="5581f-265">Parameter</span></span>                | <span data-ttu-id="5581f-266">Descripción</span><span class="sxs-lookup"><span data-stu-id="5581f-266">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5581f-267">*-Desde> de* \<cadena</span><span class="sxs-lookup"><span data-stu-id="5581f-267">*-From* \<String></span></span>        | <span data-ttu-id="5581f-268">La migración inicial.</span><span class="sxs-lookup"><span data-stu-id="5581f-268">The starting migration.</span></span> <span data-ttu-id="5581f-269">Las migraciones pueden identificarse por nombre o por identificador.</span><span class="sxs-lookup"><span data-stu-id="5581f-269">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="5581f-270">El número 0 es un caso especial que significa *antes de la primera migración*.</span><span class="sxs-lookup"><span data-stu-id="5581f-270">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="5581f-271">El valor predeterminado es 0.</span><span class="sxs-lookup"><span data-stu-id="5581f-271">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="5581f-272">*-A* \<cadena></span><span class="sxs-lookup"><span data-stu-id="5581f-272">*-To* \<String></span></span>          | <span data-ttu-id="5581f-273">La migración final.</span><span class="sxs-lookup"><span data-stu-id="5581f-273">The ending migration.</span></span> <span data-ttu-id="5581f-274">Tiene como valor predeterminado la última migración.</span><span class="sxs-lookup"><span data-stu-id="5581f-274">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="5581f-275"><nobr>-Idempotente</nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-275"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="5581f-276">Generar un script que se puede usar en una base de datos en cualquier migración.</span><span class="sxs-lookup"><span data-stu-id="5581f-276">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="5581f-277">:> \<de la cadena de salida</span><span class="sxs-lookup"><span data-stu-id="5581f-277">-Output \<String></span></span>        | <span data-ttu-id="5581f-278">Archivo en el que se va a escribir el resultado.</span><span class="sxs-lookup"><span data-stu-id="5581f-278">The file to write the result to.</span></span> <span data-ttu-id="5581f-279">Si se omite este parámetro, el archivo se crea con un nombre generado en la misma carpeta en que se crean los archivos en tiempo de ejecución de la aplicación, por ejemplo: */obj/Debug/netcoreapp2.1/ghbkztfz.SQL/*.</span><span class="sxs-lookup"><span data-stu-id="5581f-279">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="5581f-280">Los parámetros para, de y de salida admiten la expansión de pestañas.</span><span class="sxs-lookup"><span data-stu-id="5581f-280">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="5581f-281">En el ejemplo siguiente se crea un script para la migración de InitialCreate con el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="5581f-281">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="5581f-282">En el ejemplo siguiente se crea un script para todas las migraciones después de la migración de InitialCreate con el identificador de migración.</span><span class="sxs-lookup"><span data-stu-id="5581f-282">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="5581f-283">Actualizar base de datos</span><span class="sxs-lookup"><span data-stu-id="5581f-283">Update-Database</span></span>

<span data-ttu-id="5581f-284">Actualiza la base de datos a la última migración o a una migración especificada.</span><span class="sxs-lookup"><span data-stu-id="5581f-284">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="5581f-285">Parámetro</span><span class="sxs-lookup"><span data-stu-id="5581f-285">Parameter</span></span>                           | <span data-ttu-id="5581f-286">Descripción</span><span class="sxs-lookup"><span data-stu-id="5581f-286">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5581f-287"><nobr>*->de la cadena de migración* \<</nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-287"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="5581f-288">La migración de destino.</span><span class="sxs-lookup"><span data-stu-id="5581f-288">The target migration.</span></span> <span data-ttu-id="5581f-289">Las migraciones pueden identificarse por nombre o por identificador.</span><span class="sxs-lookup"><span data-stu-id="5581f-289">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="5581f-290">El número 0 es un caso especial que significa *antes de la primera migración* y hace que se reviertan todas las migraciones.</span><span class="sxs-lookup"><span data-stu-id="5581f-290">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="5581f-291">Si no se especifica ninguna migración, el comando toma como valor predeterminado la última migración.</span><span class="sxs-lookup"><span data-stu-id="5581f-291">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="5581f-292"><nobr>:>\<de la cadena de conexión</nobr></span><span class="sxs-lookup"><span data-stu-id="5581f-292"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="5581f-293">La cadena de conexión a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5581f-293">The connection string to the database.</span></span> <span data-ttu-id="5581f-294">Tiene como valor predeterminado el especificado en `AddDbContext` o `OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="5581f-294">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="5581f-295">El parámetro Migration admite la expansión de pestañas.</span><span class="sxs-lookup"><span data-stu-id="5581f-295">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="5581f-296">En el ejemplo siguiente se revierten todas las migraciones.</span><span class="sxs-lookup"><span data-stu-id="5581f-296">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="5581f-297">En los siguientes ejemplos se actualiza la base de datos a una migración especificada.</span><span class="sxs-lookup"><span data-stu-id="5581f-297">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="5581f-298">El primero usa el nombre de la migración y el segundo usa el identificador de migración y una conexión especificada:</span><span class="sxs-lookup"><span data-stu-id="5581f-298">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="5581f-299">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5581f-299">Additional resources</span></span>

* [<span data-ttu-id="5581f-300">Migraciones</span><span class="sxs-lookup"><span data-stu-id="5581f-300">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="5581f-301">Ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="5581f-301">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
