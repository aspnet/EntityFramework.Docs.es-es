---
title: 'Portabilidad de EF6 a EF Core: portabilidad de un modelo basado en EDMX - EF'
description: Información específica acerca de cómo migrar una aplicación de modelo basada en EDMX de Entity Framework 6 a Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 27b37ad1c2e3436ae96a71bc97e953763c48ee50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064268"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="5d7d5-103">Portabilidad de un modelo basado en EDMX de EF6 a EF Core</span><span class="sxs-lookup"><span data-stu-id="5d7d5-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="5d7d5-104">EF Core no admite el formato de archivo EDMX para los modelos.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="5d7d5-105">La mejor opción para realizar la portabilidad de estos modelos consiste en generar un modelo nuevo basado en código a partir de la base de datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="5d7d5-106">Instalación de los paquetes NuGet de EF Core</span><span class="sxs-lookup"><span data-stu-id="5d7d5-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="5d7d5-107">Instale el paquete NuGet `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="5d7d5-108">Regeneración del modelo</span><span class="sxs-lookup"><span data-stu-id="5d7d5-108">Regenerate the model</span></span>

<span data-ttu-id="5d7d5-109">Ahora puede usar la funcionalidad de ingeniería inversa para crear un modelo basado en la base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="5d7d5-110">Ejecute el comando siguiente en la consola del Administrador de paquetes NuGet (Herramientas –> Administrador de paquetes NuGet –> Consola del Administrador de paquetes).</span><span class="sxs-lookup"><span data-stu-id="5d7d5-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="5d7d5-111">Vea [Consola del Administrador de paquetes (Visual Studio)](xref:core/miscellaneous/cli/powershell) para conocer las opciones de comando para aplicar scaffolding a un subconjunto de tablas, etc.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-111">See [Package Manager Console (Visual Studio)](xref:core/miscellaneous/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="5d7d5-112">Por ejemplo, este es el comando para aplicar scaffolding a un modelo a partir de la base de datos Blogging en la instancia de LocalDB de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="5d7d5-113">Eliminación del modelo de EF6</span><span class="sxs-lookup"><span data-stu-id="5d7d5-113">Remove EF6 model</span></span>

<span data-ttu-id="5d7d5-114">Ahora se quitará el modelo de EF6 de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="5d7d5-115">No hay problema por dejar el paquete NuGet de EF6 (EntityFramework) instalado, ya que EF Core y EF6 se pueden usar en paralelo en la misma aplicación.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="5d7d5-116">Sin embargo, si no va a usar EF6 en ninguna de las áreas de la aplicación, la desinstalación del paquete le ayudará a dar errores de compilación en fragmentos de código que requieren atención.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="5d7d5-117">Actualización del código</span><span class="sxs-lookup"><span data-stu-id="5d7d5-117">Update your code</span></span>

<span data-ttu-id="5d7d5-118">En este punto, es cuestión de solucionar los errores de compilación y de revisar el código para ver si los cambios de comportamiento entre EF6 y EF Core le afectarán.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="5d7d5-119">Prueba del puerto</span><span class="sxs-lookup"><span data-stu-id="5d7d5-119">Test the port</span></span>

<span data-ttu-id="5d7d5-120">El hecho de que la aplicación se compile no significa que se haya trasladado correctamente a EF Core.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="5d7d5-121">Tendrá que probar todas las áreas de la aplicación para asegurarse de que ninguno de los cambios de comportamiento afecte de forma negativa a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5d7d5-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
