---
title: Obtener Entity Framework-EF6
description: Obtener Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
uid: ef6/fundamentals/install
ms.openlocfilehash: bfd016a93de73ada4487a454ec7abd9251aeecfe
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616255"
---
# <a name="get-entity-framework"></a>Obtener Entity Framework
Entity Framework se compone de las herramientas de EF para Visual Studio y el tiempo de ejecución de EF.

## <a name="ef-tools-for-visual-studio"></a>Herramientas de EF para Visual Studio

El Entity Framework Tools para Visual Studio incluye el diseñador de EF y el Asistente para modelo de EF y son necesarios para los flujos de trabajo primero y modelo primero. Las herramientas de EF se incluyen en todas las versiones recientes de Visual Studio. Si realiza una instalación personalizada de Visual Studio, debe asegurarse de que el elemento "Entity Framework 6 herramientas" esté seleccionado eligiendo una carga de trabajo que lo incluya o seleccionándola como componente individual.

En algunas versiones anteriores de Visual Studio, las herramientas de EF actualizadas están disponibles como descarga. Consulte [versiones de Visual Studio](xref:ef6/what-is-new/visual-studio) para obtener instrucciones sobre cómo obtener la versión más reciente de las herramientas de EF disponibles para su versión de Visual Studio.

## <a name="ef-runtime"></a>Tiempo de ejecución de EF

La versión más reciente de Entity Framework está disponible como el [paquete NuGet EntityFramework](https://nuget.org/packages/EntityFramework/). Si no está familiarizado con el administrador de paquetes NuGet, le recomendamos que lea la [información general de Nuget](/nuget/consume-packages/overview-and-workflow).

### <a name="installing-the-ef-nuget-package"></a>Instalación del paquete de NuGet de EF

Para instalar el paquete EntityFramework, haga clic con el botón derecho en la carpeta **referencias** del proyecto y seleccione **administrar paquetes NuGet..** .

![Administración de paquetes de NuGet](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>Instalar desde la consola del administrador de paquetes

Como alternativa, puede instalar EntityFramework ejecutando el siguiente comando en la consola del [Administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>Instalación de una versión específica de EF

Desde EF 4,1 en adelante, se han publicado nuevas versiones del tiempo de ejecución de EF como el [paquete NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/). Cualquiera de esas versiones se puede Agregar a un proyecto basado en .NET Framework ejecutando el siguiente comando en la [consola del administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)de Visual Studio:

``` powershell
Install-Package EntityFramework -Version <number>
```

Tenga en cuenta que `<number>` representa la versión específica de EF que se va a instalar. Por ejemplo, 6.2.0 es la versión del número para EF 6,2.   

Los tiempos de ejecución de EF anteriores a 4,1 formaban parte de .NET Framework y no se pueden instalar por separado.

### <a name="installing-the-latest-preview"></a>Instalación de la versión preliminar más reciente

Los métodos anteriores le proporcionarán la última versión compatible de Entity Framework. A menudo hay versiones preliminares de Entity Framework disponibles que le encantaría probar y enviarnos sus comentarios.

Para instalar la versión preliminar más reciente de EntityFramework, puede seleccionar **incluir versión** preliminar en la ventana administrar paquetes NuGet. Si no hay disponibles versiones preliminares, obtendrá automáticamente la versión más reciente compatible de Entity Framework.

![Incluir versión preliminar](~/ef6/media/includeprerelease.png)

Como alternativa, puede ejecutar el siguiente comando en la [consola del administrador de paquetes](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

``` powershell
Install-Package EntityFramework -Pre
```
