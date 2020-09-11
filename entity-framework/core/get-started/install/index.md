---
title: Instalación de Entity Framework Core - EF Core
description: Instrucciones de instalación para Entity Framework Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 1cc6f8f73739e61adefb375ee37a830933903470
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617987"
---
# <a name="installing-entity-framework-core"></a>Instalación de Entity Framework Core

## <a name="prerequisites"></a>Requisitos previos

* EF Core es una biblioteca de [.NET Standard 2.0](/dotnet/standard/net-standard). Por este motivo, EF Core requiere una implementación de .NET que admita .NET Standard 2.0 para poder ejecutarse. Otras bibliotecas de .NET Standard 2.0 también pueden hacer referencia a EF Core.

* Por ejemplo, puede usar EF Core para desarrollar aplicaciones que tengan como destino .NET Core. La compilación de aplicaciones de .NET Core requiere el [SDK de .NET Core](https://dotnet.microsoft.com/download). También puede usar un entorno de desarrollo como [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac) o [Visual Studio Code](https://code.visualstudio.com). Para obtener más información, vea [Introducción a .NET Core](/dotnet/core/get-started).

* Puede usar EF Core para desarrollar aplicaciones en Windows con Visual Studio. Se recomienda usar la última versión de [Visual Studio](https://visualstudio.microsoft.com/vs).

* EF Core puede ejecutarse en otras implementaciones de .NET, como [Xamarin](https://dotnet.microsoft.com/apps/xamarin) y .NET Native. Pero en la práctica, estas implementaciones tienen limitaciones de runtime que podrían afectar el rendimiento de EF Core en su aplicación. Para obtener más información, vea [Implementaciones de .NET compatibles con EF Core](xref:core/platforms/index).

* Por último, los diferentes proveedores de bases de datos pueden requerir versiones de motores de bases de datos, implementaciones de .NET o sistemas operativos específicas. Asegúrese de que esté disponible un [proveedor de bases de datos de EF Core](xref:core/providers/index) que admita el entorno adecuado para su aplicación.

## <a name="get-the-entity-framework-core-runtime"></a>Obtención del runtime de Entity Framework Core

Para agregar EF Core a una aplicación, instale el paquete NuGet para el proveedor de bases de datos que quiera usar.

Si está desarrollando una aplicación de ASP.NET Core, no tendrá que instalar los proveedores en memoria ni de SQL Server. Estos proveedores están incluidos en las versiones actuales de ASP.NET Core, junto al runtime de EF Core.  

Para instalar o actualizar paquetes NuGet, puede usar la interfaz de la línea de comandos (CLI) de .NET Core, o bien el cuadro de diálogo o la consola del Administrador de paquetes de Visual Studio.

### <a name="net-core-cli"></a>CLI de .NET Core

* Use el comando de la CLI de .NET Core en la línea de comandos del sistema operativo para instalar o actualizar el proveedor de SQL Server de EF Core:

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* Puede indicar una versión específica en el comando `dotnet add package` usando el modificador `-v`. Por ejemplo, para instalar paquetes de EF Core 2.2.0, anexe `-v 2.2.0` al comando.

Para obtener más información, vea [Herramientas de la interfaz de la línea de comandos (CLI) de .NET Core](/dotnet/core/tools/).

### <a name="visual-studio-nuget-package-manager-dialog"></a>Cuadro de diálogo Administrador de paquetes NuGet en Visual Studio

* En el menú de Visual Studio, seleccione **Proyecto > Administrar paquetes NuGet**

* Haga clic en la pestaña **Examinar** o **Actualizaciones**

* Para instalar o actualizar el proveedor de SQL Server, seleccione el paquete `Microsoft.EntityFrameworkCore.SqlServer` y confirme la acción.

Para obtener más información, vea [Diálogo del Administrador de paquetes NuGet](/nuget/tools/package-manager-ui).

### <a name="visual-studio-nuget-package-manager-console"></a>Consola del Administrador de paquetes NuGet de Visual Studio

* En el menú de Visual Studio, seleccione **Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes**.

* Para instalar el proveedor de SQL Server, ejecute el comando siguiente en la consola del Administrador de paquetes:

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* Para actualizar el proveedor, use el comando `Update-Package`.

* Para especificar una versión, use el modificador `-Version`. Por ejemplo, para instalar paquetes de EF Core 2.2.0, anexe `-Version 2.2.0` a los comandos.

Para obtener más información, vea [Consola del Administrador de paquetes](/nuget/tools/package-manager-console).

## <a name="get-the-entity-framework-core-tools"></a>Obtención de las herramientas de Entity Framework Core

Puede instalar herramientas para llevar a cabo tareas relacionadas con EF Core en el proyecto, como crear y aplicar las migraciones de bases de datos o crear un modelo de EF Core basado en una base de datos existente.

Existen dos conjuntos de herramientas:

* Las [herramientas de la interfaz de la línea de comandos (CLI) de .NET Core](xref:core/miscellaneous/cli/dotnet) pueden usarse en Windows, Linux y macOS. Estos comandos comienzan por `dotnet ef`.

* Las [herramientas de la consola del Administrador de paquetes (PMC)](xref:core/miscellaneous/cli/powershell) se ejecutan en Visual Studio (Windows). Estos comandos empiezan por un verbo. Por ejemplo: `Add-Migration`, `Update-Database`.

Aunque puede usar los comandos de `dotnet ef` desde la consola del Administrador de paquetes, le recomendamos que use las herramientas de la consola del Administrador de paquetes en Visual Studio:

* Trabajan automáticamente con el proyecto actual seleccionado en la PMC de Visual Studio sin necesidad de cambiar manualmente entre directorios.  

* Abren automáticamente los archivos generados por los comandos de Visual Studio una vez completado el comando.

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a>Obtención de las herramientas de la CLI de .NET Core

Las herramientas de la CLI de .NET Core requieren el SDK de .NET Core, tal como se indica en [Requisitos previos](#prerequisites).

Los comandos de `dotnet ef` están incluidos en las versiones actuales del SDK de .NET Core, pero es necesario instalar el paquete `Microsoft.EntityFrameworkCore.Design` para habilitarlos en un proyecto específico:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]
> Use siempre la versión del paquete de herramientas que coincida con la versión principal de los paquetes en tiempo de ejecución.

### <a name="get-the-package-manager-console-tools"></a>Obtención de las herramientas de la consola del Administrador de paquetes

Para obtener las herramientas de la consola del Administrador de paquetes para EF Core, instale el paquete `Microsoft.EntityFrameworkCore.Tools`. Por ejemplo, en Visual Studio:

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Para las aplicaciones de ASP.NET Core, este paquete se incluye automáticamente.

## <a name="upgrading-to-the-latest-ef-core"></a>Actualización a la versión más reciente de EF Core

* Cuando publicamos una nueva versión de EF Core, también publicamos una nueva versión de los proveedores que forman parte del proyecto de EF Core, como, por ejemplo: Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite y Microsoft.EntityFrameworkCore.InMemory. Para obtener todas las mejoras, solo tiene que actualizar a la nueva versión del proveedor.

* EF Core y los proveedores de SQL Server y en memoria están incluidos en las versiones actuales de ASP.NET Core. Para actualizar una aplicación de ASP.NET Core existente a una versión más reciente de EF Core, actualice siempre la versión de ASP.NET Core.

* Si necesita actualizar una aplicación que usa un proveedor de base de datos de terceros, busque siempre una actualización del proveedor que sea compatible con la versión de EF Core que quiere usar. Por ejemplo, los proveedores de bases de datos de las versiones anteriores no son compatibles con la versión 2.0 del runtime de EF Core.

* Los proveedores de terceros de EF Core no suelen publicar versiones de revisión junto al runtime de EF Core. Para actualizar una aplicación que use un proveedor de terceros a una versión de revisión de EF Core, puede que deba agregar una referencia directa a determinados componentes de runtime de EF Core, como Microsoft.EntityFrameworkCore o Microsoft.EntityFrameworkCore.Relational.

* Si va a actualizar una aplicación existente a la última versión de EF Core, es posible que algunas referencias a los paquetes más antiguos de EF Core tengan que quitarse manualmente:

  * Los paquetes en tiempo de diseño del proveedor de base de datos, como `Microsoft.EntityFrameworkCore.SqlServer.Design`, ya no son necesarios ni se admiten en EF Core 2.0 y versiones posteriores, pero no se quitan automáticamente al actualizar los demás paquetes.

  * Las herramientas de la CLI de .NET están incluidas en el SDK de .NET desde la versión 2.1, por lo que se puede quitar la referencia a ese paquete desde el archivo del proyecto:

    ``` xml
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```
