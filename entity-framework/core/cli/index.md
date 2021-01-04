---
title: Referencia sobre las herramientas de Entity Framework Core (EF Core)
description: Guía de referencia para la herramienta de la CLI de Entity Framework Core y la consola del administrador de paquetes de Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 1ffc773cb8ed30516d682b90bbd9accef634ae6a
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635386"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="15cf1-103">Referencia sobre las herramientas de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="15cf1-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="15cf1-104">Las herramientas de Entity Framework Core ayudan con las tareas de desarrollo en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="15cf1-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="15cf1-105">Se usan principalmente para administrar migraciones y para aplicar scaffolding a `DbContext` y a tipos de entidad mediante utilización de técnicas de ingeniería inversa en el esquema de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="15cf1-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

<span data-ttu-id="15cf1-106">Puede instalar cualquiera de las herramientas siguientes, ya que las dos exponen la misma funcionalidad:</span><span class="sxs-lookup"><span data-stu-id="15cf1-106">Either of the following tools can be installed, as both tools expose the same functionality:</span></span>

* <span data-ttu-id="15cf1-107">Las [herramientas de la Consola del Administrador de paquetes de EF Core](xref:core/cli/powershell) se ejecutan en la [Consola del Administrador de paquetes](/nuget/tools/package-manager-console) de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="15cf1-107">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span> <span data-ttu-id="15cf1-108">Si desarrolla en Visual Studio, se recomienda usar estas herramientas, ya que proporcionan una experiencia más integrada.</span><span class="sxs-lookup"><span data-stu-id="15cf1-108">We recommend using these tools if you are developing in Visual Studio as they provide a more integrated experience.</span></span>

* <span data-ttu-id="15cf1-109">Las [herramientas de la interfaz de la línea de comandos (CLI) de EF Core .NET](xref:core/cli/dotnet) son una extensión de las [herramientas de la CLI de .NET Core](/dotnet/core/tools/) multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="15cf1-109">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="15cf1-110">Estas herramientas necesitan un proyecto de SDK de .NET Core (uno con `Sdk="Microsoft.NET.Sdk"` o similar en el archivo de proyecto).</span><span class="sxs-lookup"><span data-stu-id="15cf1-110">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

## <a name="next-steps"></a><span data-ttu-id="15cf1-111">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="15cf1-111">Next steps</span></span>

* [<span data-ttu-id="15cf1-112">Referencia sobre las herramientas de la Consola del Administrador de paquetes de EF Core</span><span class="sxs-lookup"><span data-stu-id="15cf1-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="15cf1-113">Referencia sobre las herramientas de la CLI de EF Core .NET</span><span class="sxs-lookup"><span data-stu-id="15cf1-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
