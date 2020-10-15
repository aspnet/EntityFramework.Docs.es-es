---
title: 'Selección de Entity Framework versión en tiempo de ejecución para los modelos de EF Designer: EF6'
description: Selección de Entity Framework versión en tiempo de ejecución para los modelos EF Designer en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: dcd06ed9585028b3a318d867b03980254979a32f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066101"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="47c07-103">Selección de Entity Framework versión en tiempo de ejecución para los modelos EF Designer</span><span class="sxs-lookup"><span data-stu-id="47c07-103">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="47c07-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="47c07-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="47c07-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="47c07-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="47c07-106">A partir de EF6, se ha agregado la siguiente pantalla a EF Designer para que pueda seleccionar la versión del tiempo de ejecución al que desea dirigirse al crear un modelo.</span><span class="sxs-lookup"><span data-stu-id="47c07-106">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="47c07-107">La pantalla aparecerá cuando la versión más reciente de Entity Framework no esté ya instalada en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="47c07-107">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="47c07-108">Si ya está instalada la versión más reciente, se usará de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="47c07-108">If the latest version is already installed it will just be used by default.</span></span>

![Seleccionar versión de tiempo de ejecución](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a><span data-ttu-id="47c07-110">Destino de EF6. x</span><span class="sxs-lookup"><span data-stu-id="47c07-110">Targeting EF6.x</span></span>

<span data-ttu-id="47c07-111">Puede elegir EF6 en la pantalla "elegir su versión" para agregar el tiempo de ejecución de EF6 al proyecto.</span><span class="sxs-lookup"><span data-stu-id="47c07-111">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="47c07-112">Una vez que haya agregado EF6, dejará de ver esta pantalla en el proyecto actual.</span><span class="sxs-lookup"><span data-stu-id="47c07-112">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="47c07-113">EF6 se deshabilitará si ya tiene instalada una versión anterior de EF (ya que no puede tener como destino varias versiones del tiempo de ejecución del mismo proyecto).</span><span class="sxs-lookup"><span data-stu-id="47c07-113">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="47c07-114">Si la opción EF6 no está habilitada aquí, siga estos pasos para actualizar el proyecto a EF6:</span><span class="sxs-lookup"><span data-stu-id="47c07-114">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="47c07-115">Haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar paquetes NuGet...**</span><span class="sxs-lookup"><span data-stu-id="47c07-115">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="47c07-116">Seleccionar **actualizaciones**</span><span class="sxs-lookup"><span data-stu-id="47c07-116">Select **Updates**</span></span>
3.  <span data-ttu-id="47c07-117">Seleccione **EntityFramework** (Asegúrese de que se va a actualizar a la versión que quiera)</span><span class="sxs-lookup"><span data-stu-id="47c07-117">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="47c07-118">Haga clic en **Update** (Actualizar).</span><span class="sxs-lookup"><span data-stu-id="47c07-118">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="47c07-119">Destino de EF5. x</span><span class="sxs-lookup"><span data-stu-id="47c07-119">Targeting EF5.x</span></span>

<span data-ttu-id="47c07-120">Puede elegir EF5 en la pantalla "elegir su versión" para agregar el tiempo de ejecución de EF5 al proyecto.</span><span class="sxs-lookup"><span data-stu-id="47c07-120">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="47c07-121">Una vez que haya agregado EF5, seguirá viendo la pantalla con la opción EF6 deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="47c07-121">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="47c07-122">Si ya tiene instalada una versión de EF4. x del Runtime, verá que la versión de EF aparece en la pantalla en lugar de EF5.</span><span class="sxs-lookup"><span data-stu-id="47c07-122">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="47c07-123">En esta situación, puede actualizar a EF5 siguiendo estos pasos:</span><span class="sxs-lookup"><span data-stu-id="47c07-123">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="47c07-124">Seleccione **herramientas- &gt; Administrador de paquetes de la biblioteca- &gt; consola del administrador de paquetes**</span><span class="sxs-lookup"><span data-stu-id="47c07-124">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="47c07-125">Ejecute **Install-Package EntityFramework-version 5.0.0**</span><span class="sxs-lookup"><span data-stu-id="47c07-125">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="47c07-126">Destino de EF4. x</span><span class="sxs-lookup"><span data-stu-id="47c07-126">Targeting EF4.x</span></span>

<span data-ttu-id="47c07-127">Puede instalar el tiempo de ejecución de EF4. x en el proyecto mediante los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="47c07-127">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="47c07-128">Seleccione **herramientas- &gt; Administrador de paquetes de la biblioteca- &gt; consola del administrador de paquetes**</span><span class="sxs-lookup"><span data-stu-id="47c07-128">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="47c07-129">Ejecute **Install-Package EntityFramework-version 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="47c07-129">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
