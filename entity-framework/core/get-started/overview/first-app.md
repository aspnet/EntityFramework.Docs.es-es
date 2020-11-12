---
title: Introducción - EF Core
description: Tutorial de introducción para Entity Framework Core
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/overview/first-app
ms.openlocfilehash: ee2b8f87e48f466f8385548b0b9e20a3791ba2a3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431194"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="c5b29-103">Introducción a EF Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-103">Getting Started with EF Core</span></span>

<span data-ttu-id="c5b29-104">En este tutorial se crea una aplicación de consola de .NET Core que realiza el acceso a datos en una base de datos SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c5b29-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="c5b29-105">Puede seguir el tutorial con Visual Studio en Windows o mediante la CLI de .NET Core en Windows, macOS o Linux.</span><span class="sxs-lookup"><span data-stu-id="c5b29-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="c5b29-106">[Vea un ejemplo de este artículo en GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="c5b29-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c5b29-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="c5b29-107">Prerequisites</span></span>

<span data-ttu-id="c5b29-108">Instale el software siguiente:</span><span class="sxs-lookup"><span data-stu-id="c5b29-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c5b29-109">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="c5b29-110">[SDK de .NET Core](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="c5b29-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="c5b29-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5b29-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5b29-112">[Visual Studio 2019, versión 16.3 o posterior](https://www.visualstudio.com/downloads/), con esta carga de trabajo:</span><span class="sxs-lookup"><span data-stu-id="c5b29-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="c5b29-113">**Desarrollo multiplataforma de .NET Core** (en **Otros conjuntos de herramientas** )</span><span class="sxs-lookup"><span data-stu-id="c5b29-113">**.NET Core cross-platform development** (under **Other Toolsets** )</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="c5b29-114">Crear un proyecto nuevo</span><span class="sxs-lookup"><span data-stu-id="c5b29-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c5b29-115">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="c5b29-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5b29-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5b29-117">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5b29-117">Open Visual Studio</span></span>
* <span data-ttu-id="c5b29-118">Haga clic en **Crear un proyecto nuevo**</span><span class="sxs-lookup"><span data-stu-id="c5b29-118">Click **Create a new project**</span></span>
* <span data-ttu-id="c5b29-119">Seleccione **Aplicación de consola (.NET Core)** con la etiqueta **C#** y haga clic en **Siguiente**</span><span class="sxs-lookup"><span data-stu-id="c5b29-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="c5b29-120">Escriba **EFGetStarted** para el nombre y haga clic en **Crear**</span><span class="sxs-lookup"><span data-stu-id="c5b29-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="c5b29-121">Instalación de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-121">Install Entity Framework Core</span></span>

<span data-ttu-id="c5b29-122">Para instalar EF Core, instale el paquete de los proveedores de bases de datos de EF Core que quiera establecer como destino.</span><span class="sxs-lookup"><span data-stu-id="c5b29-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="c5b29-123">Este tutorial usa SQLite porque se ejecuta en todas las plataformas compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5b29-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="c5b29-124">Para obtener una lista de proveedores disponibles, vea [Proveedores de bases de datos](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="c5b29-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c5b29-125">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="c5b29-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5b29-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5b29-127">**Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes**</span><span class="sxs-lookup"><span data-stu-id="c5b29-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="c5b29-128">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c5b29-128">Run the following commands:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="c5b29-129">Sugerencia: También puede instalar paquetes si hace clic con el botón derecho en el proyecto y selecciona **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="c5b29-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="c5b29-130">Creación del modelo</span><span class="sxs-lookup"><span data-stu-id="c5b29-130">Create the model</span></span>

<span data-ttu-id="c5b29-131">Defina una clase de contexto y clases de entidad que conformen el modelo.</span><span class="sxs-lookup"><span data-stu-id="c5b29-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c5b29-132">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="c5b29-133">En el directorio del proyecto, cree **Model.cs** con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="c5b29-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="c5b29-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5b29-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5b29-135">Haga clic con el botón derecho en el proyecto y seleccione **Agregar > Clase**</span><span class="sxs-lookup"><span data-stu-id="c5b29-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="c5b29-136">Escriba **Model.cs** como el nombre y haga clic en **Agregar**</span><span class="sxs-lookup"><span data-stu-id="c5b29-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="c5b29-137">Reemplace el contenido del archivo por el código siguiente</span><span class="sxs-lookup"><span data-stu-id="c5b29-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="c5b29-138">EF Core también puede [aplicar ingeniería inversa](xref:core/managing-schemas/scaffolding) en un modelo desde una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="c5b29-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="c5b29-139">Sugerencia: Esta aplicación lo hace todo más fácil de forma intencionada.</span><span class="sxs-lookup"><span data-stu-id="c5b29-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="c5b29-140">Las [cadenas de conexión](xref:core/miscellaneous/connection-strings) no se deben almacenar en el código para aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="c5b29-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="c5b29-141">Además, es recomendable que divida cada clase de C# en su archivo correspondiente.</span><span class="sxs-lookup"><span data-stu-id="c5b29-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="c5b29-142">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="c5b29-142">Create the database</span></span>

<span data-ttu-id="c5b29-143">Los pasos siguientes usan [migraciones](xref:core/managing-schemas/migrations/index) para crear una base de datos.</span><span class="sxs-lookup"><span data-stu-id="c5b29-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c5b29-144">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="c5b29-145">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c5b29-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="c5b29-146">Esto instala [dotnet ef](xref:core/cli/dotnet) y el paquete de diseño necesario para ejecutar el comando en un proyecto.</span><span class="sxs-lookup"><span data-stu-id="c5b29-146">This installs [dotnet ef](xref:core/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="c5b29-147">El comando `migrations` aplica la técnica scaffolding a una migración para crear el conjunto inicial de tablas para el modelo.</span><span class="sxs-lookup"><span data-stu-id="c5b29-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="c5b29-148">El comando `database update` crea la base de datos y le aplica la nueva migración.</span><span class="sxs-lookup"><span data-stu-id="c5b29-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="c5b29-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5b29-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5b29-150">Ejecute los comandos siguientes en la **Consola del administrador de paquetes (PMC)** .</span><span class="sxs-lookup"><span data-stu-id="c5b29-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="c5b29-151">Esto instala las [herramientas de PMC para EF Core](xref:core/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="c5b29-151">This installs the [PMC tools for EF Core](xref:core/cli/powershell).</span></span> <span data-ttu-id="c5b29-152">El comando `Add-Migration` aplica la técnica scaffolding a una migración para crear el conjunto inicial de tablas para el modelo.</span><span class="sxs-lookup"><span data-stu-id="c5b29-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="c5b29-153">El comando `Update-Database` crea la base de datos y le aplica la nueva migración.</span><span class="sxs-lookup"><span data-stu-id="c5b29-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="c5b29-154">Creación, lectura, actualización y eliminación</span><span class="sxs-lookup"><span data-stu-id="c5b29-154">Create, read, update & delete</span></span>

* <span data-ttu-id="c5b29-155">Abra *Program.cs* y reemplace el contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c5b29-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="c5b29-156">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="c5b29-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c5b29-157">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5b29-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="c5b29-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5b29-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c5b29-159">Visual Studio usa un directorio de trabajo incoherente al ejecutar las aplicaciones de consola de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5b29-159">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="c5b29-160">(consulte [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)). Esto hace que se genere una excepción: *no such table: Blogs* (No existe dicha tabla: blogs).</span><span class="sxs-lookup"><span data-stu-id="c5b29-160">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="c5b29-161">Para actualizar el directorio de trabajo:</span><span class="sxs-lookup"><span data-stu-id="c5b29-161">To update the working directory:</span></span>

* <span data-ttu-id="c5b29-162">Haga clic en el proyecto y seleccione **Editar archivo del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="c5b29-162">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="c5b29-163">Justo debajo de la propiedad *TargetFramework* , agregue lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c5b29-163">Just below the *TargetFramework* property, add the following:</span></span>

  ```xml
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="c5b29-164">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="c5b29-164">Save the file</span></span>

<span data-ttu-id="c5b29-165">Ahora puede ejecutar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c5b29-165">Now you can run the app:</span></span>

* <span data-ttu-id="c5b29-166">**Depurar > Iniciar sin depuración**</span><span class="sxs-lookup"><span data-stu-id="c5b29-166">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="c5b29-167">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="c5b29-167">Next steps</span></span>

* <span data-ttu-id="c5b29-168">Siga el [tutorial de ASP.NET Core](/aspnet/core/data/ef-rp/intro) para usar EF Core en una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="c5b29-168">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="c5b29-169">Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).</span><span class="sxs-lookup"><span data-stu-id="c5b29-169">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="c5b29-170">[Configure su modelo](xref:core/modeling/index) para especificar aspectos como [requerido](xref:core/modeling/entity-properties#required-and-optional-properties) y [longitud máxima](xref:core/modeling/entity-properties#maximum-length).</span><span class="sxs-lookup"><span data-stu-id="c5b29-170">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="c5b29-171">Use [Migraciones](xref:core/managing-schemas/migrations/index) para actualizar el esquema de la base de datos después de cambiar el modelo.</span><span class="sxs-lookup"><span data-stu-id="c5b29-171">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
