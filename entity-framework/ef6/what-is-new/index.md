---
title: Novedades de EF6
description: Novedades de Entity Framework 6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: 71854e0a7009b35742a70724210708a284f50a3d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619759"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="f6f6d-103">Novedades de EF6</span><span class="sxs-lookup"><span data-stu-id="f6f6d-103">What's new in EF6</span></span>

<span data-ttu-id="f6f6d-104">Se recomienda encarecidamente usar la versión más reciente de Entity Framework para asegurarse de obtener las últimas características y la mayor estabilidad.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-104">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="f6f6d-105">Pero es posible que deba usar una versión anterior o que quiera experimentar con las nuevas mejoras de la última versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-105">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="f6f6d-106">Para instalar versiones concretas de EF, vea [Get Entity Framework](xref:ef6/fundamentals/install) (Obtener Entity Framework).</span><span class="sxs-lookup"><span data-stu-id="f6f6d-106">To install specific versions of EF, see [Get Entity Framework](xref:ef6/fundamentals/install).</span></span>

## <a name="ef-640"></a><span data-ttu-id="f6f6d-107">EF 6.4.0</span><span class="sxs-lookup"><span data-stu-id="f6f6d-107">EF 6.4.0</span></span>

<span data-ttu-id="f6f6d-108">El runtime de EF 6.4.0 para NuGet se publicó en diciembre de 2019.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-108">The EF 6.4.0 runtime was released to NuGet in December  2019.</span></span> <span data-ttu-id="f6f6d-109">El objetivo principal de EF 6.4 es perfeccionar las características y los escenarios que se ofrecieron en EF 6.3.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-109">The primary goal of EF 6.4 is to polish the features and scenarios that was delivered in EF 6.3.</span></span> <span data-ttu-id="f6f6d-110">Consulte la [lista de correcciones importantes](https://github.com/dotnet/ef6/milestone/14?closed=1) en Github.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-110">See [list of important fixes](https://github.com/dotnet/ef6/milestone/14?closed=1) on Github.</span></span>

## <a name="ef-630"></a><span data-ttu-id="f6f6d-111">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="f6f6d-111">EF 6.3.0</span></span>

<span data-ttu-id="f6f6d-112">El runtime de EF 6.3.0 para NuGet se publicó en septiembre de 2019.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-112">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="f6f6d-113">El objetivo principal de esta versión era facilitar la migración de las aplicaciones existentes que usan EF 6 a .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-113">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="f6f6d-114">La comunidad también ha contribuido con varias correcciones de errores y mejoras.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-114">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="f6f6d-115">Consulte los problemas cerrados en cada [hito](https://github.com/aspnet/EntityFramework6/milestones?state=closed) de la versión 6.3.0 para información detallada.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-115">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="f6f6d-116">Estos son algunos de los más importantes:</span><span class="sxs-lookup"><span data-stu-id="f6f6d-116">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="f6f6d-117">Compatibilidad con .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-117">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="f6f6d-118">El paquete EntityFramework ahora tiene como destino .NET Standard 2.1 además de .NET Framework 4.x.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-118">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="f6f6d-119">Esto significa que EF 6.3 es multiplataforma y se admite en otros sistemas operativos además de Windows, como Linux y macOS.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-119">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="f6f6d-120">Se han vuelto a escribir los comandos de migración para ejecutarlos fuera de proceso y que trabajen con proyectos de estilo de SDK.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-120">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="f6f6d-121">Compatibilidad con HierarchyId de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-121">Support for SQL Server HierarchyId.</span></span>
- <span data-ttu-id="f6f6d-122">Compatibilidad mejorada con PackageReference de Roslyn y NuGet.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-122">Improved compatibility with Roslyn and NuGet PackageReference.</span></span>
- <span data-ttu-id="f6f6d-123">Se agregó la utilidad `ef6.exe` para habilitar, agregar, generar scripts y aplicar migraciones desde los ensamblados.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-123">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="f6f6d-124">Reemplaza a `migrate.exe`.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-124">This replaces `migrate.exe`.</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="f6f6d-125">Compatibilidad con el diseñador de EF</span><span class="sxs-lookup"><span data-stu-id="f6f6d-125">EF designer support</span></span>

<span data-ttu-id="f6f6d-126">Actualmente no se admite el uso del diseñador de EF directamente en proyectos de .NET Core o .NET Standard o en un proyecto de .NET Framework de estilo SDK.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-126">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects or on an SDK-style .NET Framework project.</span></span> 

<span data-ttu-id="f6f6d-127">Puede solucionar esta limitación agregando el archivo EDMX y las clases generadas para las entidades y DbContext como archivos vinculados a un proyecto de .NET Core 3.0 o .NET Standard 2.1 en la misma solución.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-127">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="f6f6d-128">Los archivos vinculados tendrían el aspecto siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6f6d-128">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="f6f6d-129">Tenga en cuenta que el archivo EDMX está vinculado con la acción de compilación EntityDeploy.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-129">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="f6f6d-130">Se trata de una tarea especial de MSBuild (ahora incluida en el paquete EF 6.3) que se encarga de agregar el modelo EF en el ensamblado de destino como recursos incrustados (o de copiarlo como archivos en la carpeta de salida, en función de la configuración de procesamiento de artefactos de metadatos en EDMX).</span><span class="sxs-lookup"><span data-stu-id="f6f6d-130">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="f6f6d-131">Para más información sobre cómo configurar esta configuración, consulte nuestro [ejemplo de .NET Core de EDMX](https://aka.ms/EdmxDotNetCoreSample).</span><span class="sxs-lookup"><span data-stu-id="f6f6d-131">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

<span data-ttu-id="f6f6d-132">Advertencia: Asegúrese de que el estilo antiguo (es decir, no SDK) del proyecto de .NET Framework que define el archivo. edmx "real" se incluye _antes_ del proyecto que define el vínculo dentro del archivo. sln.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-132">Warning: make sure the old style (i.e. non-SDK-style) .NET Framework project defining the "real" .edmx file comes _before_ the project defining the link inside the .sln file.</span></span> <span data-ttu-id="f6f6d-133">De lo contrario, al abrir el archivo. edmx en el diseñador, verá el mensaje de error "The Entity Framework is not available in the target framework currently specified for the project.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-133">Otherwise, when you open the .edmx file in the designer, you see the error message "The Entity Framework is not available in the target framework currently specified for the project.</span></span> <span data-ttu-id="f6f6d-134">You can change the target framework of the project or edit the model in the XmlEditor" ("Entity Framework no está disponible en la plataforma de destino especificada actualmente para el proyecto. Puede cambiar la plataforma de destino del proyecto o editar el modelo en XmlEditor").</span><span class="sxs-lookup"><span data-stu-id="f6f6d-134">You can change the target framework of the project or edit the model in the XmlEditor".</span></span>

## <a name="past-releases"></a><span data-ttu-id="f6f6d-135">Versiones anteriores</span><span class="sxs-lookup"><span data-stu-id="f6f6d-135">Past Releases</span></span>

<span data-ttu-id="f6f6d-136">La página [Versiones anteriores](xref:ef6/what-is-new/past-releases) contiene un archivo de todas las versiones anteriores de EF y de las características principales incluidas en cada versión.</span><span class="sxs-lookup"><span data-stu-id="f6f6d-136">The [Past Releases](xref:ef6/what-is-new/past-releases) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
