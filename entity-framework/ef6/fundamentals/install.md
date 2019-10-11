---
title: Obtener Entity Framework-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 2bdec6a9be228fbe934d0f46aa1bfafdfb2c971c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181735"
---
# <a name="get-entity-framework"></a><span data-ttu-id="bc060-102">Obtener Entity Framework</span><span class="sxs-lookup"><span data-stu-id="bc060-102">Get Entity Framework</span></span>
<span data-ttu-id="bc060-103">Entity Framework está formado por EF Tools para Visual Studio y EF runtime.</span><span class="sxs-lookup"><span data-stu-id="bc060-103">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="bc060-104">EF Tools para Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc060-104">EF Tools for Visual Studio</span></span>

<span data-ttu-id="bc060-105">Entity Framework Tools para Visual Studio incluyen EF Designer y el EF Model Wizard y son necesarios para los flujos de trabajo _database first_ y _model first_.</span><span class="sxs-lookup"><span data-stu-id="bc060-105">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="bc060-106">EF Tools es includo en todas las versiones recientes de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bc060-106">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="bc060-107">Si lleva a cabo una instalación personalizada de Visual Studio, deberá asegurarse el elemento "Entity Frameworks 6 Tools" está seleccionado eligiendo un _workload_ que lo incluya o seleccionandolo como un componente individual.</span><span class="sxs-lookup"><span data-stu-id="bc060-107">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="bc060-108">Para algunas versiones anteriores de Visual Studio, EF Tools actualizadas están disponibles como descarga.</span><span class="sxs-lookup"><span data-stu-id="bc060-108">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="bc060-109">Consulte [versiones de Visual Studio](~/ef6/what-is-new/visual-studio.md) para obtener instrucciones sobre cómo obtener la versión más reciente disponible de EF Tools para su versión de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bc060-109">See [Visual Studio Versions](~/ef6/what-is-new/visual-studio.md) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="bc060-110">EF runtime</span><span class="sxs-lookup"><span data-stu-id="bc060-110">EF Runtime</span></span>

<span data-ttu-id="bc060-111">La versión más reciente de Entity Framework está disponible como [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="bc060-111">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="bc060-112">Si no está familiarizado con el Administrador de paquetes NuGet, le recomendamos que lea [información general sobre NuGet](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span><span class="sxs-lookup"><span data-stu-id="bc060-112">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="bc060-113">Instalar EF NuGet package</span><span class="sxs-lookup"><span data-stu-id="bc060-113">Installing the EF NuGet Package</span></span>

<span data-ttu-id="bc060-114">Puede instalar Entity Framework package con el botón secundario sobre el directorio **References** del proyecto y seleccionando **Administrar paquetes NuGet...**</span><span class="sxs-lookup"><span data-stu-id="bc060-114">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![Administrar paquetes NuGet](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="bc060-116">Instalación desde Package Manager Console</span><span class="sxs-lookup"><span data-stu-id="bc060-116">Installing from Package Manager Console</span></span>

<span data-ttu-id="bc060-117">Como alternativa, puede instalar EntityFramework ejecutando el siguiente comando en la consola del [Administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="bc060-117">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="bc060-118">Instalación de una versión específica de EF</span><span class="sxs-lookup"><span data-stu-id="bc060-118">Installing a specific version of EF</span></span>

<span data-ttu-id="bc060-119">De EF 4.1 y versiones posteriores, se publican nuevas versiones del tiempo de ejecución EF como [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span><span class="sxs-lookup"><span data-stu-id="bc060-119">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="bc060-120">Cualquiera de esas versiones se puede Agregar a un proyecto basado en .NET Framework ejecutando el siguiente comando en la [consola del administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)de Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="bc060-120">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="bc060-121">Tenga en cuenta que `<number>` representa la versión específica de EF que se va a instalar.</span><span class="sxs-lookup"><span data-stu-id="bc060-121">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="bc060-122">Por ejemplo, 6.2.0 es la versión del número para EF 6,2.</span><span class="sxs-lookup"><span data-stu-id="bc060-122">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="bc060-123">Los EF runtimes anteriores a 4.1 formaban parte de .NET Framework y no se puede instalar por separado.</span><span class="sxs-lookup"><span data-stu-id="bc060-123">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="bc060-124">Instalación de la versión preliminar más reciente</span><span class="sxs-lookup"><span data-stu-id="bc060-124">Installing the Latest Preview</span></span>

<span data-ttu-id="bc060-125">Los métodos anteriores le proporcionarán la versión más reciente totalmente compatible con la versión de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bc060-125">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="bc060-126">A menudo hay versiones preliminares disponibles de Entity Framework que nos encantaría que probara y de las que puede enviarnos sus comentarios.</span><span class="sxs-lookup"><span data-stu-id="bc060-126">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="bc060-127">Para instalar la versión preliminar más reciente de Entity Framework puede seleccionar **Incluir versión preliminar** en la ventana Administrar paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="bc060-127">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="bc060-128">Si no hay ninguna versión preliminar disponible automáticamente obtendrá la última versión compatible de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bc060-128">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![Incluir versión preliminar](~/ef6/media/includeprerelease.png)

<span data-ttu-id="bc060-130">Como alternativa, puede ejecutar el siguiente comando en el [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="bc060-130">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
