---
title: 'Introducción a WPF: EF Core'
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 958fc579a72a9bf3c97c5551d55777df6c32293f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535607"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="e6cd2-102">Introducción a WPF</span><span class="sxs-lookup"><span data-stu-id="e6cd2-102">Getting Started with WPF</span></span>

<span data-ttu-id="e6cd2-103">En este tutorial paso a paso se muestra cómo enlazar tipos POCO a controles de WPF en un formulario "main-detail".</span><span class="sxs-lookup"><span data-stu-id="e6cd2-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="e6cd2-104">La aplicación utiliza las API de Entity Framework para rellenar los objetos con datos de la base de datos, realizar un seguimiento de los cambios y conservar los datos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="e6cd2-105">El modelo define dos tipos que participan en una relación de uno a varios: **Category** (principal\\main) y **Product** (dependent\\detail).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="e6cd2-106">El marco de enlace de datos de WPF permite la navegación entre objetos relacionados: la selección de filas en la vista maestra hace que la vista de detalles se actualice con los datos secundarios correspondientes.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-106">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="e6cd2-107">Las capturas de pantalla y las listas de código de este tutorial se han tomado de Visual Studio 2019 16.6.5.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-107">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="e6cd2-108">Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-108">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="e6cd2-109">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e6cd2-109">Pre-Requisites</span></span>

* <span data-ttu-id="e6cd2-110">Debe tener Visual Studio 2019 16.3 o posterior instalado con la **carga de trabajo de escritorio de .NET** seleccionada para completar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-110">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="e6cd2-111">Para obtener más información sobre cómo instalar la última versión de Visual Studio, vea [Instalación de Visual Studio](/visualstudio/install/install-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-111">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="e6cd2-112">Crear la aplicación</span><span class="sxs-lookup"><span data-stu-id="e6cd2-112">Create the Application</span></span>

1. <span data-ttu-id="e6cd2-113">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6cd2-113">Open Visual Studio</span></span>
2. <span data-ttu-id="e6cd2-114">En la ventana de inicio, elija **Crear proyecto**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-114">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="e6cd2-115">Busque "WPF", elija **Aplicación WPF (.NET Core)** y después seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-115">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="e6cd2-116">En la pantalla siguiente, asigne un nombre al proyecto, por ejemplo, **GetStartedWPF**, y elija **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-116">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="e6cd2-117">Instalación de los paquetes de NuGet Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e6cd2-117">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="e6cd2-118">Haga clic con el botón derecho en la solución y elija **Administrar paquetes NuGet para la solución...**</span><span class="sxs-lookup"><span data-stu-id="e6cd2-118">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![Administración de paquetes de NuGet](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="e6cd2-120">Escriba `entityframeworkcore.sqlite` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-120">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="e6cd2-121">Instale el paquete **Microsoft.EntityFrameworkCore.Sqlite**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-121">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="e6cd2-122">Compruebe el proyecto en el panel derecho y haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-122">Check the project in the right pane and click **Install**</span></span>

    ![Paquete de Sqlite](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="e6cd2-124">Repita los pasos para buscar `entityframeworkcore.proxies` e instalar **Microsoft.EntityFrameworkCore.Proxies**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-124">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="e6cd2-125">Al instalar el paquete de Sqlite, se extrae automáticamente el paquete de base **Microsoft.EntityFrameworkCore** relacionado.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-125">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="e6cd2-126">El paquete **Microsoft.EntityFrameworkCore.Proxies** proporciona compatibilidad con los datos de "carga diferida".</span><span class="sxs-lookup"><span data-stu-id="e6cd2-126">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="e6cd2-127">Esto significa que, si tiene entidades con entidades secundarias, solo se capturan los elementos primarios en la carga inicial.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-127">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="e6cd2-128">Los proxies detectan cuándo se produce un intento de acceso a las entidades secundarias, y las cargan automáticamente a petición.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-128">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="e6cd2-129">Definición de un modelo</span><span class="sxs-lookup"><span data-stu-id="e6cd2-129">Define a Model</span></span>

<span data-ttu-id="e6cd2-130">En este tutorial, implementará un modelo con "Code First".</span><span class="sxs-lookup"><span data-stu-id="e6cd2-130">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="e6cd2-131">Esto significa que EF Core creará las tablas de base de datos y el esquema en función de las clases de C# que defina.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-131">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="e6cd2-132">Agregue una nueva clase.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-132">Add a new class.</span></span> <span data-ttu-id="e6cd2-133">Asígnele el nombre `Product.cs` y rellénelo como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="e6cd2-133">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="e6cd2-134">A continuación, agregue una clase denominada `Category.cs` y rellénela con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e6cd2-134">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="e6cd2-135">La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-135">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="e6cd2-136">En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-136">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="e6cd2-137">Además de definir entidades, debe definir una clase que derive de DbContext y exponga las propiedades DbSet&lt;TEntity&gt;.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="e6cd2-138">Las propiedades DbSet&lt;TEntity&gt; permiten que el contexto sepa qué tipos desea incluir en el modelo.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="e6cd2-139">Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="e6cd2-140">Agregue una nueva clase `ProductContext.cs` al proyecto con la siguiente definición:</span><span class="sxs-lookup"><span data-stu-id="e6cd2-140">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="e6cd2-141">`DbSet` informa a EF Core de qué entidades de C# se deben asignar a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-141">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="e6cd2-142">Hay varias maneras de configurar `DbContext` de EF Core.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-142">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="e6cd2-143">Puede leer sobre ellas en: [Configuración de un DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-143">You can read about them in: [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="e6cd2-144">En este ejemplo se usa la invalidación `OnConfiguring` para especificar un archivo de datos de Sqlite.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-144">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="e6cd2-145">La llamada a `UseLazyLoadingProxies` indica a EF Core que implemente la carga diferida, por lo que las entidades secundarias se cargan automáticamente cuando se obtiene acceso a ellas desde el elemento primario.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-145">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="e6cd2-146">Presione **CTRL + MAYÚS + B** o vaya a **Compilar &gt; Compilar solución** para compilar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-146">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="e6cd2-147">Obtenga información sobre los distintos pasos para mantener sincronizados la base de datos y los modelos de EF Core: [Administración de esquemas de base de datos](/ef/core/managing-schemas).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-147">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](/ef/core/managing-schemas).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="e6cd2-148">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="e6cd2-148">Lazy Loading</span></span>

<span data-ttu-id="e6cd2-149">La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-149">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="e6cd2-150">En Entity Framework Core, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-150">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="e6cd2-151">EF Core ofrece una opción para cargar automáticamente las entidades relacionadas desde la base de datos la primera vez que se accede a la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-151">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="e6cd2-152">Con este tipo de carga (denominado carga diferida), tenga en cuenta que la primera vez que se accede a cada propiedad de navegación se ejecutará una consulta independiente en la base de datos si el contenido no está ya en el contexto.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-152">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="e6cd2-153">Al usar tipos de entidad "Plain Old C# Object" (POCO), EF Core logra la carga diferida mediante la creación de instancias de tipos de proxy derivados durante el tiempo de ejecución y, después, la invalidación de las propiedades virtuales de las clases para agregar el enlace de carga.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-153">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="e6cd2-154">Para obtener la carga diferida de los objetos relacionados, debe declarar captadores de propiedades de navegación como **público** y **virtual** (**Overridable** en Visual Basic), y la clase no debe **sellarse** (**NotOverridable** en Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-154">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="e6cd2-155">Al usar Database First, las propiedades de navegación se convierten automáticamente en virtuales para habilitar la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-155">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="e6cd2-156">Enlace de objetos a controles</span><span class="sxs-lookup"><span data-stu-id="e6cd2-156">Bind Object to Controls</span></span>

<span data-ttu-id="e6cd2-157">Agregue las clases que se definen en el modelo como orígenes de datos para esta aplicación WPF.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-157">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="e6cd2-158">Haga doble clic en **MainWindow.xaml** en el Explorador de soluciones para abrir el formulario principal.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-158">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="e6cd2-159">Elija la pestaña **XAML** para editar el código XAML.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-159">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="e6cd2-160">Inmediatamente después de la etiqueta de apertura `Window`, agregue los orígenes siguientes para conectarse a las entidades de EF Core.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-160">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="e6cd2-161">De esta forma, se configura el origen para las categorías "principales" y el segundo origen para los productos "detallados".</span><span class="sxs-lookup"><span data-stu-id="e6cd2-161">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="e6cd2-162">A continuación, agregue el marcado siguiente al código XAML después de la etiqueta de cierre `Window.Resources`.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-162">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="e6cd2-163">Tenga en cuenta que el elemento `CategoryId` se establece en `ReadOnly` porque está asignado por la base de datos y no se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-163">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="e6cd2-164">Adición de una cuadrícula de detalles</span><span class="sxs-lookup"><span data-stu-id="e6cd2-164">Adding a Details Grid</span></span>

<span data-ttu-id="e6cd2-165">Ahora que la cuadrícula existe para mostrar las categorías, se puede agregar la cuadrícula de detalles para mostrar los productos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-165">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="e6cd2-166">Por último, agregue un botón `Save` y una conexión en el evento de clic a `Button_Click`.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-166">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="e6cd2-167">La vista de diseño debería tener un aspecto similar a este:</span><span class="sxs-lookup"><span data-stu-id="e6cd2-167">Your design view should look like this:</span></span>

![Captura de pantalla de WPF Designer](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="e6cd2-169">Adición de código que controla la interacción con los datos</span><span class="sxs-lookup"><span data-stu-id="e6cd2-169">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="e6cd2-170">Es el momento de agregar algunos controladores de eventos a la ventana principal.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-170">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="e6cd2-171">En la ventana de XAML, haga clic en el elemento **&lt;Ventana&gt;** , para seleccionar la ventana principal.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-171">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="e6cd2-172">En la ventana **Propiedades**, elija **Eventos** en la parte superior derecha y, a continuación, haga doble clic en el cuadro de texto que se encuentra a la derecha de la etiqueta **Cargado**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-172">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![Propiedades de la ventana principal](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="e6cd2-174">Esto lo lleva al código subyacente del formulario; ahora, se editará el código para usar el elemento `ProductContext` para ejecutar el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-174">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="e6cd2-175">Actualice el código como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-175">Update the code as shown below.</span></span>

<span data-ttu-id="e6cd2-176">El código declara una instancia de larga duración de `ProductContext`.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-176">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="e6cd2-177">El objeto `ProductContext` se utiliza para consultar y guardar datos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-177">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="e6cd2-178">A continuación, se llama al método `Dispose()` en la instancia de `ProductContext` desde el método `OnClosing` invalidado.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-178">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="e6cd2-179">Los comentarios del código explican qué hace cada paso.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-179"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="e6cd2-180">El código usa una llamada a `EnsureCreated()` para compilar la base de datos en la primera ejecución.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-180">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="e6cd2-181">Esto es aceptable para demostraciones, pero en las aplicaciones de producción debe examinar [migraciones](/ef/core/managing-schemas/migrations/) para administrar el esquema.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-181">This is acceptable for demos, but in production apps you should look at [migrations](/ef/core/managing-schemas/migrations/) to manage your schema.</span></span> <span data-ttu-id="e6cd2-182">El código también se ejecuta de forma sincrónica porque usa una base de datos SQLite local.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-182">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="e6cd2-183">En escenarios de producción que normalmente implican un servidor remoto, considere la posibilidad de utilizar las versiones asincrónicas de los métodos `Load` y `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-183">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="e6cd2-184">Prueba de una aplicación WPF</span><span class="sxs-lookup"><span data-stu-id="e6cd2-184">Test the WPF Application</span></span>

<span data-ttu-id="e6cd2-185">Para compilar y ejecutar la aplicación, presione **F5** o seleccione **Depurar &gt; Iniciar depuración**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-185">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="e6cd2-186">La base de datos debe crearse automáticamente con un archivo denominado `products.db`.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-186">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="e6cd2-187">Escriba un nombre de categoría y presione ENTRAR y, a continuación, agregue productos a la cuadrícula inferior.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-187">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="e6cd2-188">Haga clic en Guardar y observe la actualización de la cuadrícula con los identificadores proporcionados por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-188">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="e6cd2-189">Resalte una fila y presione **Eliminar** para quitarla.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-189">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="e6cd2-190">La entidad se eliminará al hacer clic en **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-190">The entity will be deleted when you click **Save**.</span></span>

![Aplicación en ejecución](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="e6cd2-192">Notificación de cambio de propiedad</span><span class="sxs-lookup"><span data-stu-id="e6cd2-192">Property Change Notification</span></span>

<span data-ttu-id="e6cd2-193">Este ejemplo se basa en cuatro pasos para sincronizar las entidades con la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-193">This example relies on four steps to synchronize the entities with the UI.</span></span> 

1. <span data-ttu-id="e6cd2-194">La llamada inicial a `_context.Categories.Load()` carga los datos de categorías.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-194">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="e6cd2-195">Los proxies de carga diferida cargan los datos de los productos dependientes.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-195">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="e6cd2-196">El seguimiento de cambios integrado de EF Core realiza las modificaciones necesarias en las entidades, incluidas las inserciones y eliminaciones, cuando se llama a `_context.SaveChanges()`.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-196">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="e6cd2-197">Las llamadas a `DataGridView.Items.Refresh()` fuerzan una recarga con los identificadores recién generados.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-197">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="e6cd2-198">Esto sirve para el ejemplo de introducción, pero puede que necesite código adicional para otros escenarios.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-198">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="e6cd2-199">Los controles de WPF representan la interfaz de usuario mediante la lectura de los campos y las propiedades de las entidades.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-199">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="e6cd2-200">Cuando se edita un valor en la interfaz de usuario (UI), ese valor se pasa a la entidad.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-200">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="e6cd2-201">Al cambiar el valor de una propiedad directamente en la entidad, como cargarla desde la base de datos, WPF no reflejará inmediatamente los cambios en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-201">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="e6cd2-202">El motor de representación debe recibir notificaciones de los cambios.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-202">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="e6cd2-203">El proyecto lo hizo llamando manualmente a `Refresh()`.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-203">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="e6cd2-204">Una manera sencilla de automatizar esta notificación es mediante la implementación de la interfaz [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-204">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="e6cd2-205">Los componentes de WPF detectarán automáticamente la interfaz y se registrarán para los eventos de cambio.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-205">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="e6cd2-206">La entidad es responsable de generar estos eventos.</span><span class="sxs-lookup"><span data-stu-id="e6cd2-206">The entity is responsible for raising these events.</span></span> 

> [!TIP]
> <span data-ttu-id="e6cd2-207">Para obtener más información sobre cómo administrar los cambios, lea: [Implementación de la notificación de cambio de propiedad](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-207">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e6cd2-208">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="e6cd2-208">Next Steps</span></span>

<span data-ttu-id="e6cd2-209">Más información sobre la [Configuración de un DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="e6cd2-209">Learn more about [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>