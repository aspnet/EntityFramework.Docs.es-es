---
title: Introducción - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 7181df6ee73b09f196940178ffed38d96b075258
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370323"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="02368-102">Introducción a EF Core</span><span class="sxs-lookup"><span data-stu-id="02368-102">Getting Started with EF Core</span></span>

<span data-ttu-id="02368-103">En este tutorial se crea una aplicación de consola de .NET Core que realiza el acceso a datos en una base de datos SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="02368-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="02368-104">Puede seguir el tutorial con Visual Studio en Windows o mediante la CLI de .NET Core en Windows, macOS o Linux.</span><span class="sxs-lookup"><span data-stu-id="02368-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="02368-105">[Vea un ejemplo de este artículo en GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="02368-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="02368-106">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="02368-106">Prerequisites</span></span>

<span data-ttu-id="02368-107">Instale el software siguiente:</span><span class="sxs-lookup"><span data-stu-id="02368-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="02368-108">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="02368-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="02368-109">[SDK de .NET Core](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="02368-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="02368-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02368-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02368-111">[Visual Studio 2019, versión 16.3 o posterior](https://www.visualstudio.com/downloads/), con esta carga de trabajo:</span><span class="sxs-lookup"><span data-stu-id="02368-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="02368-112">**Desarrollo multiplataforma de .NET Core** (en **Otros conjuntos de herramientas**)</span><span class="sxs-lookup"><span data-stu-id="02368-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="02368-113">Crear un proyecto nuevo</span><span class="sxs-lookup"><span data-stu-id="02368-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="02368-114">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="02368-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="02368-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02368-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02368-116">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02368-116">Open Visual Studio</span></span>
* <span data-ttu-id="02368-117">Haga clic en **Crear un proyecto nuevo**</span><span class="sxs-lookup"><span data-stu-id="02368-117">Click **Create a new project**</span></span>
* <span data-ttu-id="02368-118">Seleccione **Aplicación de consola (.NET Core)** con la etiqueta **C#** y haga clic en **Siguiente**</span><span class="sxs-lookup"><span data-stu-id="02368-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="02368-119">Escriba **EFGetStarted** para el nombre y haga clic en **Crear**</span><span class="sxs-lookup"><span data-stu-id="02368-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="02368-120">Instalación de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="02368-120">Install Entity Framework Core</span></span>

<span data-ttu-id="02368-121">Para instalar EF Core, instale el paquete de los proveedores de bases de datos de EF Core que quiera establecer como destino.</span><span class="sxs-lookup"><span data-stu-id="02368-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="02368-122">Este tutorial usa SQLite porque se ejecuta en todas las plataformas compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="02368-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="02368-123">Para obtener una lista de proveedores disponibles, vea [Proveedores de bases de datos](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="02368-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="02368-124">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="02368-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="02368-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02368-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02368-126">**Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes**</span><span class="sxs-lookup"><span data-stu-id="02368-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="02368-127">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="02368-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="02368-128">Sugerencia: También puede instalar paquetes si hace clic con el botón derecho en el proyecto y selecciona **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="02368-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="02368-129">Creación del modelo</span><span class="sxs-lookup"><span data-stu-id="02368-129">Create the model</span></span>

<span data-ttu-id="02368-130">Defina una clase de contexto y clases de entidad que conformen el modelo.</span><span class="sxs-lookup"><span data-stu-id="02368-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="02368-131">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="02368-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="02368-132">En el directorio del proyecto, cree **Model.cs** con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="02368-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="02368-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02368-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02368-134">Haga clic con el botón derecho en el proyecto y seleccione **Agregar > Clase**</span><span class="sxs-lookup"><span data-stu-id="02368-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="02368-135">Escriba **Model.cs** como el nombre y haga clic en **Agregar**</span><span class="sxs-lookup"><span data-stu-id="02368-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="02368-136">Reemplace el contenido del archivo por el código siguiente</span><span class="sxs-lookup"><span data-stu-id="02368-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="02368-137">EF Core también puede [aplicar ingeniería inversa](../managing-schemas/scaffolding.md) en un modelo desde una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="02368-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="02368-138">Sugerencia: Esta aplicación lo hace todo más fácil de forma intencionada.</span><span class="sxs-lookup"><span data-stu-id="02368-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="02368-139">Las [cadenas de conexión](../miscellaneous/connection-strings.md) no se deben almacenar en el código para aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="02368-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="02368-140">Además, es recomendable que divida cada clase de C# en su archivo correspondiente.</span><span class="sxs-lookup"><span data-stu-id="02368-140">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="02368-141">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="02368-141">Create the database</span></span>

<span data-ttu-id="02368-142">Los pasos siguientes usan [migraciones](xref:core/managing-schemas/migrations/index) para crear una base de datos.</span><span class="sxs-lookup"><span data-stu-id="02368-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="02368-143">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="02368-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="02368-144">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="02368-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="02368-145">Esto instala [dotnet ef](../miscellaneous/cli/dotnet.md) y el paquete de diseño necesario para ejecutar el comando en un proyecto.</span><span class="sxs-lookup"><span data-stu-id="02368-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="02368-146">El comando `migrations` aplica la técnica scaffolding a una migración para crear el conjunto inicial de tablas para el modelo.</span><span class="sxs-lookup"><span data-stu-id="02368-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="02368-147">El comando `database update` crea la base de datos y le aplica la nueva migración.</span><span class="sxs-lookup"><span data-stu-id="02368-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="02368-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02368-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02368-149">Ejecute los comandos siguientes en la **Consola del administrador de paquetes (PMC)** .</span><span class="sxs-lookup"><span data-stu-id="02368-149">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="02368-150">Esto instala las [herramientas de PMC para EF Core](../miscellaneous/cli/powershell.md).</span><span class="sxs-lookup"><span data-stu-id="02368-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="02368-151">El comando `Add-Migration` aplica la técnica scaffolding a una migración para crear el conjunto inicial de tablas para el modelo.</span><span class="sxs-lookup"><span data-stu-id="02368-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="02368-152">El comando `Update-Database` crea la base de datos y le aplica la nueva migración.</span><span class="sxs-lookup"><span data-stu-id="02368-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="02368-153">Creación, lectura, actualización y eliminación</span><span class="sxs-lookup"><span data-stu-id="02368-153">Create, read, update & delete</span></span>

* <span data-ttu-id="02368-154">Abra *Program.cs* y reemplace el contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="02368-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="02368-155">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="02368-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="02368-156">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="02368-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="02368-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02368-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02368-158">Visual Studio usa un directorio de trabajo incoherente al ejecutar las aplicaciones de consola de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="02368-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="02368-159">(consulte [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)). Esto hace que se genere una excepción: *no such table: Blogs* (No existe dicha tabla: blogs).</span><span class="sxs-lookup"><span data-stu-id="02368-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="02368-160">Para actualizar el directorio de trabajo:</span><span class="sxs-lookup"><span data-stu-id="02368-160">To update the working directory:</span></span>

* <span data-ttu-id="02368-161">Haga clic en el proyecto y seleccione **Editar archivo del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="02368-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="02368-162">Justo debajo de la propiedad *TargetFramework*, agregue lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="02368-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="02368-163">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="02368-163">Save the file</span></span>

<span data-ttu-id="02368-164">Ahora puede ejecutar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="02368-164">Now you can run the app:</span></span>

* <span data-ttu-id="02368-165">**Depurar > Iniciar sin depuración**</span><span class="sxs-lookup"><span data-stu-id="02368-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="02368-166">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="02368-166">Next steps</span></span>

* <span data-ttu-id="02368-167">Siga el [tutorial de ASP.NET Core](/aspnet/core/data/ef-rp/intro) para usar EF Core en una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="02368-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="02368-168">Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).</span><span class="sxs-lookup"><span data-stu-id="02368-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="02368-169">[Configure su modelo](xref:core/modeling/index) para especificar aspectos como [requerido](xref:core/modeling/entity-properties#required-and-optional-properties) y [longitud máxima](xref:core/modeling/entity-properties#maximum-length).</span><span class="sxs-lookup"><span data-stu-id="02368-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="02368-170">Use [Migraciones](xref:core/managing-schemas/migrations/index) para actualizar el esquema de la base de datos después de cambiar el modelo.</span><span class="sxs-lookup"><span data-stu-id="02368-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
