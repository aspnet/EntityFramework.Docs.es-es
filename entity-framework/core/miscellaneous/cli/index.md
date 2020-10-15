---
title: Referencia sobre las herramientas de Entity Framework Core (EF Core)
description: Guía de referencia para la herramienta de la CLI de Entity Framework Core y la consola del administrador de paquetes de Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 90e8d799de8fa9426de6ac3d286bcd5458a43f0d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062001"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="23f83-103">Referencia sobre las herramientas de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="23f83-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="23f83-104">Las herramientas de Entity Framework Core ayudan con las tareas de desarrollo en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="23f83-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="23f83-105">Se usan principalmente para administrar migraciones y para aplicar scaffolding a `DbContext` y a tipos de entidad mediante utilización de técnicas de ingeniería inversa en el esquema de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="23f83-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="23f83-106">Las [herramientas de la Consola del Administrador de paquetes de EF Core](xref:core/miscellaneous/cli/powershell) se ejecutan en la [Consola del Administrador de paquetes](/nuget/tools/package-manager-console) de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="23f83-106">The [EF Core Package Manager Console tools](xref:core/miscellaneous/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="23f83-107">Las [herramientas de la interfaz de la línea de comandos (CLI) de EF Core .NET](xref:core/miscellaneous/cli/dotnet) son una extensión de las [herramientas de la CLI de .NET Core](/dotnet/core/tools/) multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="23f83-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="23f83-108">Estas herramientas necesitan un proyecto de SDK de .NET Core (uno con `Sdk="Microsoft.NET.Sdk"` o similar en el archivo de proyecto).</span><span class="sxs-lookup"><span data-stu-id="23f83-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="23f83-109">Ambas herramientas exponen la misma funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="23f83-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="23f83-110">Si está desarrollando en Visual Studio, se recomienda usar las herramientas de la **Consola del Administrador de paquetes**, ya que proporcionan una experiencia más integrada.</span><span class="sxs-lookup"><span data-stu-id="23f83-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="23f83-111">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="23f83-111">Next steps</span></span>

* [<span data-ttu-id="23f83-112">Referencia sobre las herramientas de la Consola del Administrador de paquetes de EF Core</span><span class="sxs-lookup"><span data-stu-id="23f83-112">EF Core Package Manager Console tools reference</span></span>](xref:core/miscellaneous/cli/powershell)
* [<span data-ttu-id="23f83-113">Referencia sobre las herramientas de la CLI de EF Core .NET</span><span class="sxs-lookup"><span data-stu-id="23f83-113">EF Core .NET CLI tools reference</span></span>](xref:core/miscellaneous/cli/dotnet)
