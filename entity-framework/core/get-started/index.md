---
title: Introducción - EF Core
description: Tutorial de introducción para Entity Framework Core
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/index
ms.openlocfilehash: 132586aa41916d94ebef156330cc553a789ed5a7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062118"
---
# <a name="getting-started-with-ef-core"></a>Introducción a EF Core

En este tutorial se crea una aplicación de consola de .NET Core que realiza el acceso a datos en una base de datos SQLite mediante Entity Framework Core.

Puede seguir el tutorial con Visual Studio en Windows o mediante la CLI de .NET Core en Windows, macOS o Linux.

[Vea un ejemplo de este artículo en GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).

## <a name="prerequisites"></a>Requisitos previos

Instale el software siguiente:

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

* [SDK de .NET Core](https://www.microsoft.com/net/download/core).

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019, versión 16.3 o posterior](https://www.visualstudio.com/downloads/), con esta carga de trabajo:
  * **Desarrollo multiplataforma de .NET Core** (en **Otros conjuntos de herramientas**)

---

## <a name="create-a-new-project"></a>Crear un proyecto nuevo

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Apertura de Visual Studio
* Haga clic en **Crear un proyecto nuevo**
* Seleccione **Aplicación de consola (.NET Core)** con la etiqueta **C#** y haga clic en **Siguiente**
* Escriba **EFGetStarted** para el nombre y haga clic en **Crear**

---

## <a name="install-entity-framework-core"></a>Instalación de Entity Framework Core

Para instalar EF Core, instale el paquete de los proveedores de bases de datos de EF Core que quiera establecer como destino. Este tutorial usa SQLite porque se ejecuta en todas las plataformas compatibles con .NET Core. Para obtener una lista de proveedores disponibles, vea [Proveedores de bases de datos](xref:core/providers/index).

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes**
* Ejecute los comandos siguientes:

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

Sugerencia: También puede instalar paquetes si hace clic con el botón derecho en el proyecto y selecciona **Administrar paquetes NuGet**.

---

## <a name="create-the-model"></a>Creación del modelo

Defina una clase de contexto y clases de entidad que conformen el modelo.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

* En el directorio del proyecto, cree **Model.cs** con el código siguiente.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Haga clic con el botón derecho en el proyecto y seleccione **Agregar > Clase**
* Escriba **Model.cs** como el nombre y haga clic en **Agregar**
* Reemplace el contenido del archivo por el código siguiente

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

EF Core también puede [aplicar ingeniería inversa](xref:core/managing-schemas/scaffolding) en un modelo desde una base de datos existente.

Sugerencia: Esta aplicación lo hace todo más fácil de forma intencionada. Las [cadenas de conexión](xref:core/miscellaneous/connection-strings) no se deben almacenar en el código para aplicaciones de producción. Además, es recomendable que divida cada clase de C# en su archivo correspondiente.

## <a name="create-the-database"></a>Creación de la base de datos

Los pasos siguientes usan [migraciones](xref:core/managing-schemas/migrations/index) para crear una base de datos.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

* Ejecute los comandos siguientes:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  Esto instala [dotnet ef](xref:core/miscellaneous/cli/dotnet) y el paquete de diseño necesario para ejecutar el comando en un proyecto. El comando `migrations` aplica la técnica scaffolding a una migración para crear el conjunto inicial de tablas para el modelo. El comando `database update` crea la base de datos y le aplica la nueva migración.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ejecute los comandos siguientes en la **Consola del administrador de paquetes (PMC)** .

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  Esto instala las [herramientas de PMC para EF Core](xref:core/miscellaneous/cli/powershell). El comando `Add-Migration` aplica la técnica scaffolding a una migración para crear el conjunto inicial de tablas para el modelo. El comando `Update-Database` crea la base de datos y le aplica la nueva migración.

---

## <a name="create-read-update--delete"></a>Creación, lectura, actualización y eliminación

* Abra *Program.cs* y reemplace el contenido por el código siguiente:

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>Ejecutar la aplicación

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio usa un directorio de trabajo incoherente al ejecutar las aplicaciones de consola de .NET Core. (consulte [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)). Esto hace que se genere una excepción: *no such table: Blogs* (No existe dicha tabla: blogs). Para actualizar el directorio de trabajo:

* Haga clic en el proyecto y seleccione **Editar archivo del proyecto**.
* Justo debajo de la propiedad *TargetFramework*, agregue lo siguiente:

  ```xml
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* Guarde el archivo.

Ahora puede ejecutar la aplicación:

* **Depurar > Iniciar sin depuración**

---

## <a name="next-steps"></a>Pasos siguientes

* Siga el [tutorial de ASP.NET Core](/aspnet/core/data/ef-rp/intro) para usar EF Core en una aplicación web.
* Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).
* [Configure su modelo](xref:core/modeling/index) para especificar aspectos como [requerido](xref:core/modeling/entity-properties#required-and-optional-properties) y [longitud máxima](xref:core/modeling/entity-properties#maximum-length).
* Use [Migraciones](xref:core/managing-schemas/migrations/index) para actualizar el esquema de la base de datos después de cambiar el modelo.
