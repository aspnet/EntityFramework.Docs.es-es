---
title: Referencia sobre las herramientas de Entity Framework Core (EF Core)
description: Guía de referencia para la herramienta de la CLI de Entity Framework Core y la consola del administrador de paquetes de Visual Studio
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 7d5fb984aafed2cf45efa9e5b83bf4bc6c18a44a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619424"
---
# <a name="entity-framework-core-tools-reference"></a>Referencia sobre las herramientas de Entity Framework Core

Las herramientas de Entity Framework Core ayudan con las tareas de desarrollo en tiempo de diseño. Se usan principalmente para administrar migraciones y para aplicar scaffolding a `DbContext` y a tipos de entidad mediante utilización de técnicas de ingeniería inversa en el esquema de una base de datos.

* Las [herramientas de la Consola del Administrador de paquetes de EF Core](xref:core/miscellaneous/cli/powershell) se ejecutan en la [Consola del Administrador de paquetes](/nuget/tools/package-manager-console) de Visual Studio.

* Las [herramientas de la interfaz de la línea de comandos (CLI) de EF Core .NET](xref:core/miscellaneous/cli/dotnet) son una extensión de las [herramientas de la CLI de .NET Core](/dotnet/core/tools/) multiplataforma. Estas herramientas necesitan un proyecto de SDK de .NET Core (uno con `Sdk="Microsoft.NET.Sdk"` o similar en el archivo de proyecto).

Ambas herramientas exponen la misma funcionalidad. Si está desarrollando en Visual Studio, se recomienda usar las herramientas de la **Consola del Administrador de paquetes**, ya que proporcionan una experiencia más integrada.

## <a name="next-steps"></a>Pasos siguientes

* [Referencia sobre las herramientas de la Consola del Administrador de paquetes de EF Core](xref:core/miscellaneous/cli/powershell)
* [Referencia sobre las herramientas de la CLI de EF Core .NET](xref:core/miscellaneous/cli/dotnet)
