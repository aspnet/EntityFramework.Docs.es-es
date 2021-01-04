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
# <a name="entity-framework-core-tools-reference"></a>Referencia sobre las herramientas de Entity Framework Core

Las herramientas de Entity Framework Core ayudan con las tareas de desarrollo en tiempo de diseño. Se usan principalmente para administrar migraciones y para aplicar scaffolding a `DbContext` y a tipos de entidad mediante utilización de técnicas de ingeniería inversa en el esquema de una base de datos.

Puede instalar cualquiera de las herramientas siguientes, ya que las dos exponen la misma funcionalidad:

* Las [herramientas de la Consola del Administrador de paquetes de EF Core](xref:core/cli/powershell) se ejecutan en la [Consola del Administrador de paquetes](/nuget/tools/package-manager-console) de Visual Studio. Si desarrolla en Visual Studio, se recomienda usar estas herramientas, ya que proporcionan una experiencia más integrada.

* Las [herramientas de la interfaz de la línea de comandos (CLI) de EF Core .NET](xref:core/cli/dotnet) son una extensión de las [herramientas de la CLI de .NET Core](/dotnet/core/tools/) multiplataforma. Estas herramientas necesitan un proyecto de SDK de .NET Core (uno con `Sdk="Microsoft.NET.Sdk"` o similar en el archivo de proyecto).

## <a name="next-steps"></a>Pasos siguientes

* [Referencia sobre las herramientas de la Consola del Administrador de paquetes de EF Core](xref:core/cli/powershell)
* [Referencia sobre las herramientas de la CLI de EF Core .NET](xref:core/cli/dotnet)
