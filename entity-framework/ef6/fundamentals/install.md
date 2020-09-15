---
title: Obtener Entity Framework-EF6
description: Obtener Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/install
ms.openlocfilehash: 9254f835f5007a213c49557165f6830c30711f1f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072790"
---
# <a name="get-entity-framework"></a><span data-ttu-id="8d865-103">Obtener Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8d865-103">Get Entity Framework</span></span>
<span data-ttu-id="8d865-104">Entity Framework se compone de las herramientas de EF para Visual Studio y el tiempo de ejecución de EF.</span><span class="sxs-lookup"><span data-stu-id="8d865-104">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="8d865-105">Herramientas de EF para Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d865-105">EF Tools for Visual Studio</span></span>

<span data-ttu-id="8d865-106">El Entity Framework Tools para Visual Studio incluye el diseñador de EF y el Asistente para modelo de EF y son necesarios para los flujos de trabajo primero y modelo primero.</span><span class="sxs-lookup"><span data-stu-id="8d865-106">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="8d865-107">Las herramientas de EF se incluyen en todas las versiones recientes de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8d865-107">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="8d865-108">Si realiza una instalación personalizada de Visual Studio, debe asegurarse de que el elemento "Entity Framework 6 herramientas" esté seleccionado eligiendo una carga de trabajo que lo incluya o seleccionándola como componente individual.</span><span class="sxs-lookup"><span data-stu-id="8d865-108">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="8d865-109">En algunas versiones anteriores de Visual Studio, las herramientas de EF actualizadas están disponibles como descarga.</span><span class="sxs-lookup"><span data-stu-id="8d865-109">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="8d865-110">Consulte [versiones de Visual Studio](xref:ef6/what-is-new/visual-studio) para obtener instrucciones sobre cómo obtener la versión más reciente de las herramientas de EF disponibles para su versión de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8d865-110">See [Visual Studio Versions](xref:ef6/what-is-new/visual-studio) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="8d865-111">Tiempo de ejecución de EF</span><span class="sxs-lookup"><span data-stu-id="8d865-111">EF Runtime</span></span>

<span data-ttu-id="8d865-112">La versión más reciente de Entity Framework está disponible como el [paquete NuGet EntityFramework](https://nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="8d865-112">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="8d865-113">Si no está familiarizado con el administrador de paquetes NuGet, le recomendamos que lea la [información general de Nuget](/nuget/consume-packages/overview-and-workflow).</span><span class="sxs-lookup"><span data-stu-id="8d865-113">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="8d865-114">Instalación del paquete de NuGet de EF</span><span class="sxs-lookup"><span data-stu-id="8d865-114">Installing the EF NuGet Package</span></span>

<span data-ttu-id="8d865-115">Para instalar el paquete EntityFramework, haga clic con el botón derecho en la carpeta **referencias** del proyecto y seleccione **administrar paquetes NuGet..** .</span><span class="sxs-lookup"><span data-stu-id="8d865-115">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![Administración de paquetes de NuGet](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="8d865-117">Instalar desde la consola del administrador de paquetes</span><span class="sxs-lookup"><span data-stu-id="8d865-117">Installing from Package Manager Console</span></span>

<span data-ttu-id="8d865-118">Como alternativa, puede instalar EntityFramework ejecutando el siguiente comando en la consola del [Administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="8d865-118">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="8d865-119">Instalación de una versión específica de EF</span><span class="sxs-lookup"><span data-stu-id="8d865-119">Installing a specific version of EF</span></span>

<span data-ttu-id="8d865-120">Desde EF 4,1 en adelante, se han publicado nuevas versiones del tiempo de ejecución de EF como el [paquete NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="8d865-120">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="8d865-121">Cualquiera de esas versiones se puede Agregar a un proyecto basado en .NET Framework ejecutando el siguiente comando en la [consola del administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)de Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8d865-121">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="8d865-122">Tenga en cuenta que `<number>` representa la versión específica de EF que se va a instalar.</span><span class="sxs-lookup"><span data-stu-id="8d865-122">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="8d865-123">Por ejemplo, 6.2.0 es la versión del número para EF 6,2.</span><span class="sxs-lookup"><span data-stu-id="8d865-123">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="8d865-124">Los tiempos de ejecución de EF anteriores a 4,1 formaban parte de .NET Framework y no se pueden instalar por separado.</span><span class="sxs-lookup"><span data-stu-id="8d865-124">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="8d865-125">Instalación de la versión preliminar más reciente</span><span class="sxs-lookup"><span data-stu-id="8d865-125">Installing the Latest Preview</span></span>

<span data-ttu-id="8d865-126">Los métodos anteriores le proporcionarán la última versión compatible de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8d865-126">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="8d865-127">A menudo hay versiones preliminares de Entity Framework disponibles que le encantaría probar y enviarnos sus comentarios.</span><span class="sxs-lookup"><span data-stu-id="8d865-127">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="8d865-128">Para instalar la versión preliminar más reciente de EntityFramework, puede seleccionar **incluir versión** preliminar en la ventana administrar paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="8d865-128">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="8d865-129">Si no hay disponibles versiones preliminares, obtendrá automáticamente la versión más reciente compatible de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8d865-129">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![Incluir versión preliminar](~/ef6/media/includeprerelease.png)

<span data-ttu-id="8d865-131">Como alternativa, puede ejecutar el siguiente comando en la [consola del administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="8d865-131">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
