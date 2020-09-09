---
title: DataBinding con WinForms-EF6
description: DataBinding con WinForms en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
uid: ef6/fundamentals/databinding/winforms
ms.openlocfilehash: 934ea2ae488a7df128746a0607037d85e9b23771
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616336"
---
# <a name="databinding-with-winforms"></a><span data-ttu-id="8d7f7-103">Databinding with WinForms (Enlace de datos con WinForms)</span><span class="sxs-lookup"><span data-stu-id="8d7f7-103">Databinding with WinForms</span></span>
<span data-ttu-id="8d7f7-104">En este tutorial paso a paso se muestra cómo enlazar tipos POCO a controles de formularios Windows Forms (WinForms) en un formulario "principal-detalle".</span><span class="sxs-lookup"><span data-stu-id="8d7f7-104">This step-by-step walkthrough shows how to bind POCO types to Window Forms (WinForms) controls in a “master-detail" form.</span></span> <span data-ttu-id="8d7f7-105">La aplicación utiliza Entity Framework para rellenar los objetos con datos de la base de datos, realizar un seguimiento de los cambios y conservar los datos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-105">The application uses Entity Framework to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="8d7f7-106">El modelo define dos tipos que participan en una relación de uno a varios: categoría ( \\ maestro principal) y producto ( \\ detalle dependiente).</span><span class="sxs-lookup"><span data-stu-id="8d7f7-106">The model defines two types that participate in one-to-many relationship: Category (principal\\master) and Product (dependent\\detail).</span></span> <span data-ttu-id="8d7f7-107">A continuación, se usan las herramientas de Visual Studio para enlazar los tipos definidos en el modelo con los controles de WinForms.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-107">Then, the Visual Studio tools are used to bind the types defined in the model to the WinForms controls.</span></span> <span data-ttu-id="8d7f7-108">El marco de enlace de datos de WinForms permite la navegación entre objetos relacionados: la selección de filas en la vista maestra hace que la vista de detalles se actualice con los datos secundarios correspondientes.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-108">The WinForms data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="8d7f7-109">Las capturas de pantalla y las listas de código de este tutorial se han tomado de Visual Studio 2013 pero puede completar este tutorial con Visual Studio 2012 o Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-109">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="8d7f7-110">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="8d7f7-110">Pre-Requisites</span></span>

<span data-ttu-id="8d7f7-111">Debe tener Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 instalado para completar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-111">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="8d7f7-112">Si usa Visual Studio 2010, también tiene que instalar NuGet.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-112">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="8d7f7-113">Para obtener más información, consulte [instalación de NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).</span><span class="sxs-lookup"><span data-stu-id="8d7f7-113">For more information, see [Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="8d7f7-114">Crear la aplicación</span><span class="sxs-lookup"><span data-stu-id="8d7f7-114">Create the Application</span></span>

-   <span data-ttu-id="8d7f7-115">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d7f7-115">Open Visual Studio</span></span>
-   <span data-ttu-id="8d7f7-116">**Archivo- &gt; nuevo- &gt; proyecto....**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-116">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="8d7f7-117">Seleccione **Windows** en el panel izquierdo y **Windows FormsApplication** en el panel derecho.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-117">Select **Windows** in the left pane and **Windows FormsApplication** in the right pane</span></span>
-   <span data-ttu-id="8d7f7-118">Escriba **WinFormswithEFSample** como nombre</span><span class="sxs-lookup"><span data-stu-id="8d7f7-118">Enter **WinFormswithEFSample** as the name</span></span>
-   <span data-ttu-id="8d7f7-119">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-119">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="8d7f7-120">Instalar el paquete NuGet de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8d7f7-120">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="8d7f7-121">En Explorador de soluciones, haga clic con el botón derecho en el proyecto **WinFormswithEFSample**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-121">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="8d7f7-122">Seleccione **administrar paquetes NuGet..** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-122">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="8d7f7-123">En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-123">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="8d7f7-124">Haz clic en **Instalar**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-124">Click **Install**</span></span>  
    > [!NOTE]
    > <span data-ttu-id="8d7f7-125">Además del ensamblado EntityFramework, también se agrega una referencia a System. ComponentModel. DataAnnotations.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-125">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="8d7f7-126">Si el proyecto tiene una referencia a System. Data. Entity, se quitará cuando se instale el paquete EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-126">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="8d7f7-127">El ensamblado System. Data. Entity ya no se utiliza para las aplicaciones de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-127">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="implementing-ilistsource-for-collections"></a><span data-ttu-id="8d7f7-128">Implementación de IListSource para colecciones</span><span class="sxs-lookup"><span data-stu-id="8d7f7-128">Implementing IListSource for Collections</span></span>

<span data-ttu-id="8d7f7-129">Las propiedades de colección deben implementar la interfaz IListSource para habilitar el enlace de datos bidireccional con la ordenación al utilizar Windows Forms.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-129">Collection properties must implement the IListSource interface to enable two-way data binding with sorting when using Windows Forms.</span></span> <span data-ttu-id="8d7f7-130">Para ello, vamos a ampliar ObservableCollection para agregar la funcionalidad IListSource.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-130">To do this we are going to extend ObservableCollection to add IListSource functionality.</span></span>

-   <span data-ttu-id="8d7f7-131">Agregue una clase **ObservableListSource** al proyecto:</span><span class="sxs-lookup"><span data-stu-id="8d7f7-131">Add an **ObservableListSource** class to the project:</span></span>
    -   <span data-ttu-id="8d7f7-132">Haga clic con el botón derecho en el nombre del proyecto</span><span class="sxs-lookup"><span data-stu-id="8d7f7-132">Right-click on the project name</span></span>
    -   <span data-ttu-id="8d7f7-133">Seleccionar **agregar- &gt; nuevo elemento**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-133">Select **Add -&gt; New Item**</span></span>
    -   <span data-ttu-id="8d7f7-134">Seleccione **clase** y escriba **ObservableListSource** para el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-134">Select **Class** and enter **ObservableListSource** for the class name</span></span>
-   <span data-ttu-id="8d7f7-135">Reemplace el código generado de forma predeterminada con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8d7f7-135">Replace the code generated by default with the following code:</span></span>

<span data-ttu-id="8d7f7-136">*Esta clase permite el enlace de datos bidireccional y la ordenación. La clase se deriva de ObservableCollection &lt; T &gt; y agrega una implementación explícita de IListSource. El método GetList () de IListSource se implementa para devolver una implementación de IBindingList que permanece sincronizada con ObservableCollection. La implementación de IBindingList generada por ToBindingList admite la ordenación. El método de extensión ToBindingList se define en el ensamblado EntityFramework.*</span><span class="sxs-lookup"><span data-stu-id="8d7f7-136">*This class enables two-way data binding as well as sorting. The class derives from ObservableCollection&lt;T&gt; and adds an explicit implementation of IListSource. The GetList() method of IListSource is implemented to return an IBindingList implementation that stays in sync with the ObservableCollection. The IBindingList implementation generated by ToBindingList supports sorting. The ToBindingList extension method is defined in the EntityFramework assembly.*</span></span>

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a><span data-ttu-id="8d7f7-137">Definición de un modelo</span><span class="sxs-lookup"><span data-stu-id="8d7f7-137">Define a Model</span></span>

<span data-ttu-id="8d7f7-138">En este tutorial, puede elegir implementar un modelo mediante Code First o el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-138">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="8d7f7-139">Complete una de las dos secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-139">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="8d7f7-140">Opción 1: definir un modelo con Code First</span><span class="sxs-lookup"><span data-stu-id="8d7f7-140">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="8d7f7-141">En esta sección se muestra cómo crear un modelo y su base de datos asociada mediante Code First.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-141">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="8d7f7-142">Vaya a la sección siguiente (**opción 2: definir un modelo con Database First)** si prefiere usar Database First para aplicar ingeniería inversa al modelo desde una base de datos mediante el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-142">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="8d7f7-143">Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio).</span><span class="sxs-lookup"><span data-stu-id="8d7f7-143">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="8d7f7-144">Agregar una nueva clase de **producto** a Project</span><span class="sxs-lookup"><span data-stu-id="8d7f7-144">Add a new **Product** class to project</span></span>
-   <span data-ttu-id="8d7f7-145">Reemplace el código generado de forma predeterminada con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8d7f7-145">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   <span data-ttu-id="8d7f7-146">Agregue una clase de **categoría** al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-146">Add a **Category** class to the project.</span></span>
-   <span data-ttu-id="8d7f7-147">Reemplace el código generado de forma predeterminada con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8d7f7-147">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

<span data-ttu-id="8d7f7-148">Además de definir entidades, debe definir una clase que derive de **DbContext** y exponga propiedades de \*\*DbSet &lt; &gt; \*\* .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-148">In addition to defining entities, you need to define a class that derives from **DbContext** and exposes **DbSet&lt;TEntity&gt;** properties.</span></span> <span data-ttu-id="8d7f7-149">Las propiedades **DbSet** permiten que el contexto sepa qué tipos desea incluir en el modelo.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-149">The **DbSet** properties let the context know which types you want to include in the model.</span></span> <span data-ttu-id="8d7f7-150">Los tipos **DbContext** y **DbSet** se definen en el ensamblado EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-150">The **DbContext** and **DbSet** types are defined in the EntityFramework assembly.</span></span>

<span data-ttu-id="8d7f7-151">Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-151">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="8d7f7-152">Agregue una nueva clase **ProductContext** al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-152">Add a new **ProductContext** class to the project.</span></span>
-   <span data-ttu-id="8d7f7-153">Reemplace el código generado de forma predeterminada con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8d7f7-153">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="8d7f7-154">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-154">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="8d7f7-155">Opción 2: definir un modelo con Database First</span><span class="sxs-lookup"><span data-stu-id="8d7f7-155">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="8d7f7-156">En esta sección se muestra cómo usar Database First para aplicar ingeniería inversa al modelo desde una base de datos mediante el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-156">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="8d7f7-157">Si ha completado la sección anterior (**opción 1: definir un modelo con Code First)**, omita esta sección y vaya directamente a la sección de **carga diferida** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-157">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="8d7f7-158">Crear una base de datos existente</span><span class="sxs-lookup"><span data-stu-id="8d7f7-158">Create an Existing Database</span></span>

<span data-ttu-id="8d7f7-159">Normalmente, cuando el destino es una base de datos existente, ya se creará, pero para este tutorial es necesario crear una base de datos para tener acceso a.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-159">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="8d7f7-160">El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:</span><span class="sxs-lookup"><span data-stu-id="8d7f7-160">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="8d7f7-161">Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-161">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="8d7f7-162">Si usa Visual Studio 2012, va a crear una base de datos de [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-162">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="8d7f7-163">Vamos a generar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-163">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="8d7f7-164">**Vista &gt; Explorador de servidores**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-164">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="8d7f7-165">Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-165">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="8d7f7-166">Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar Microsoft SQL Server como origen de datos</span><span class="sxs-lookup"><span data-stu-id="8d7f7-166">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Cambiar origen de datos](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="8d7f7-168">Conéctese a LocalDB o a SQL Express, en función de la que haya instalado y escriba **Products** como nombre de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-168">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![Agregar conexión LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![Agregar conexión Express](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="8d7f7-171">Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-171">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Creación de una base de datos](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="8d7f7-173">La nueva base de datos aparecerá ahora en Explorador de servidores, haga clic con el botón derecho en ella y seleccione **nueva consulta** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-173">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="8d7f7-174">Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-174">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a><span data-ttu-id="8d7f7-175">Modelo de ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="8d7f7-175">Reverse Engineer Model</span></span>

<span data-ttu-id="8d7f7-176">Vamos a hacer uso de Entity Framework Designer, que se incluye como parte de Visual Studio, para crear nuestro modelo.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-176">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="8d7f7-177">**Proyecto- &gt; Agregar nuevo elemento...**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-177">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="8d7f7-178">Seleccione **datos** en el menú de la izquierda y, a continuación, **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-178">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="8d7f7-179">Escriba **ProductModel** como nombre y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-179">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="8d7f7-180">Se iniciará el **Asistente para Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-180">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="8d7f7-181">Seleccione **generar desde la base de datos** y haga clic en **siguiente** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-181">Select **Generate from Database** and click **Next**</span></span>

    ![Elegir contenido de Model](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="8d7f7-183">Seleccione la conexión a la base de datos creada en la primera sección, escriba **ProductContext** como nombre de la cadena de conexión y haga clic en **siguiente** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-183">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![Elegir la conexión](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="8d7f7-185">Haga clic en la casilla situada junto a "tablas" para importar todas las tablas y haga clic en "finalizar".</span><span class="sxs-lookup"><span data-stu-id="8d7f7-185">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Elección de los objetos](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="8d7f7-187">Una vez que se completa el proceso de ingeniería inversa, el nuevo modelo se agrega al proyecto y se abre para que pueda verlo en el Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-187">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="8d7f7-188">También se ha agregado un archivo App.config al proyecto con los detalles de conexión de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-188">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="8d7f7-189">Pasos adicionales en Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="8d7f7-189">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="8d7f7-190">Si está trabajando en Visual Studio 2010, tendrá que actualizar el diseñador de EF para usar la generación de código EF6.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-190">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="8d7f7-191">Haga clic con el botón derecho en una zona vacía del modelo en el diseñador de EF y seleccione **Agregar elemento de generación de código..** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-191">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="8d7f7-192">Seleccione **plantillas en línea** en el menú de la izquierda y busque **DbContext**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-192">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="8d7f7-193">Seleccione el **generador de DbContext de EF 6. x para C \# ,** escriba **ProductsModel** como nombre y haga clic en Agregar.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-193">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="8d7f7-194">Actualizar la generación de código para el enlace de datos</span><span class="sxs-lookup"><span data-stu-id="8d7f7-194">Updating code generation for data binding</span></span>

<span data-ttu-id="8d7f7-195">EF genera código a partir del modelo mediante plantillas T4.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-195">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="8d7f7-196">Las plantillas que se incluyen con Visual Studio o que se descargan de la galería de Visual Studio están pensadas para uso general.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-196">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="8d7f7-197">Esto significa que las entidades generadas a partir de estas plantillas tienen &lt; propiedades ICollection T simples &gt; .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-197">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="8d7f7-198">Sin embargo, cuando se realiza el enlace de datos, es aconsejable tener propiedades de colección que implementen IListSource.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-198">However, when doing data binding it is desirable to have collection properties that implement IListSource.</span></span> <span data-ttu-id="8d7f7-199">Este es el motivo por el que hemos creado la clase ObservableListSource anterior y ahora vamos a modificar las plantillas para hacer uso de esta clase.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-199">This is why we created the ObservableListSource class above and we are now going to modify the templates to make use of this class.</span></span>

-   <span data-ttu-id="8d7f7-200">Abra el **Explorador de soluciones** y busque el archivo **ProductModel. edmx.**</span><span class="sxs-lookup"><span data-stu-id="8d7f7-200">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="8d7f7-201">Busque el archivo **ProductModel.TT** que se anidará en el archivo ProductModel. edmx.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-201">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![Plantilla de modelo de producto](~/ef6/media/productmodeltemplate.png)

-   <span data-ttu-id="8d7f7-203">Haga doble clic en el archivo ProductModel.tt para abrirlo en el editor de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d7f7-203">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="8d7f7-204">Busque y reemplace las dos apariciones de "**ICollection**" por "**ObservableListSource**".</span><span class="sxs-lookup"><span data-stu-id="8d7f7-204">Find and replace the two occurrences of “**ICollection**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="8d7f7-205">Se encuentran en aproximadamente las líneas 296 y 484.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-205">These are located at approximately lines 296 and 484.</span></span>
-   <span data-ttu-id="8d7f7-206">Busque y reemplace la primera aparición de "**HashSet**" por "**ObservableListSource**".</span><span class="sxs-lookup"><span data-stu-id="8d7f7-206">Find and replace the first occurrence of “**HashSet**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="8d7f7-207">Esta repetición se encuentra en aproximadamente la línea 50.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-207">This occurrence is located at approximately line 50.</span></span> <span data-ttu-id="8d7f7-208">**No** Reemplace la segunda aparición de HashSet que se encuentra más adelante en el código.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-208">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="8d7f7-209">Guarde el archivo ProductModel.tt.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-209">Save the ProductModel.tt file.</span></span> <span data-ttu-id="8d7f7-210">Esto debería hacer que se vuelva a generar el código para las entidades.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-210">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="8d7f7-211">Si el código no se vuelve a generar automáticamente, haga clic con el botón derecho en ProductModel.tt y elija "ejecutar herramienta personalizada".</span><span class="sxs-lookup"><span data-stu-id="8d7f7-211">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="8d7f7-212">Si ahora abre el archivo Category.cs (que está anidado en ProductModel.tt), debería ver que la colección Products tiene el tipo \*\*ObservableListSource &lt; Product &gt; \*\*.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-212">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableListSource&lt;Product&gt;**.</span></span>

<span data-ttu-id="8d7f7-213">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-213">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="8d7f7-214">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="8d7f7-214">Lazy Loading</span></span>

<span data-ttu-id="8d7f7-215">La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-215">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="8d7f7-216">En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-216">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="8d7f7-217">EF ofrece una opción para cargar automáticamente entidades relacionadas desde la base de datos la primera vez que se tiene acceso a la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-217">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="8d7f7-218">Con este tipo de carga (denominado carga diferida), tenga en cuenta que la primera vez que se accede a cada propiedad de navegación se ejecutará una consulta independiente en la base de datos si el contenido no está ya en el contexto.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-218">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="8d7f7-219">Cuando se usan tipos de entidad POCO, EF consigue la carga diferida mediante la creación de instancias de tipos de proxy derivados durante el tiempo de ejecución y, a continuación, la invalidación de las propiedades virtuales en las clases para agregar el enlace de carga.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-219">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="8d7f7-220">Para obtener la carga diferida de los objetos relacionados, debe declarar captadores de propiedades de navegación como **Public** y **virtual** (**reemplazable** en Visual Basic) y la clase no debe estar **sellada** (**NotOverridable** en Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="8d7f7-220">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="8d7f7-221">Cuando se usa Database First propiedades de navegación se convierten automáticamente en virtual para habilitar la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-221">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="8d7f7-222">En la sección Code First hemos optado por que las propiedades de navegación sean virtuales por la misma razón</span><span class="sxs-lookup"><span data-stu-id="8d7f7-222">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="8d7f7-223">Enlace de objetos a controles</span><span class="sxs-lookup"><span data-stu-id="8d7f7-223">Bind Object to Controls</span></span>

<span data-ttu-id="8d7f7-224">Agregue las clases que se definen en el modelo como orígenes de datos para esta aplicación de WinForms.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-224">Add the classes that are defined in the model as data sources for this WinForms application.</span></span>

-   <span data-ttu-id="8d7f7-225">En el menú principal, seleccione **proyecto- &gt; Agregar nuevo origen de datos.** ..</span><span class="sxs-lookup"><span data-stu-id="8d7f7-225">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="8d7f7-226">(en Visual Studio 2010, debe seleccionar **datos- &gt; Agregar nuevo origen de datos.**..)</span><span class="sxs-lookup"><span data-stu-id="8d7f7-226">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="8d7f7-227">En la ventana elegir un tipo de origen de datos, seleccione **objeto** y haga clic en **siguiente** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-227">In the Choose a Data Source Type window, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="8d7f7-228">En el cuadro de diálogo Seleccionar los objetos de datos, **WinFormswithEFSample** dos veces y seleccione **categoría** no es necesario seleccionar el origen de datos del producto, porque lo haremos a través de la propiedad del producto en el origen de datos de la categoría.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-228">In the Select the Data Objects dialog, unfold the **WinFormswithEFSample** two times and select **Category** There is no need to select the Product data source, because we will get to it through the Product’s property on the Category data source.</span></span>

    ![Origen de datos](~/ef6/media/datasource.png)

-   <span data-ttu-id="8d7f7-230">Haga clic en **Finish** (Finalizar).</span><span class="sxs-lookup"><span data-stu-id="8d7f7-230">Click **Finish.**</span></span>
    <span data-ttu-id="8d7f7-231">Si la ventana orígenes de datos no aparece, seleccione **Ver- &gt; otras ventanas-otros &gt; orígenes de datos** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-231">If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources**</span></span>
-   <span data-ttu-id="8d7f7-232">Presione el icono de anclaje para que la ventana orígenes de datos no se oculte automáticamente.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-232">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="8d7f7-233">Es posible que tenga que presionar el botón actualizar si la ventana ya estaba visible.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-233">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Origen de datos 2](~/ef6/media/datasource2.png)

-   <span data-ttu-id="8d7f7-235">En Explorador de soluciones, haga doble clic en el archivo **Form1.CS** para abrir el formulario principal en el diseñador.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-235">In Solution Explorer, double-click the **Form1.cs** file to open the main form in designer.</span></span>
-   <span data-ttu-id="8d7f7-236">Seleccione el origen de datos de la **categoría** y arrástrelo en el formulario.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-236">Select the **Category** data source and drag it on the form.</span></span> <span data-ttu-id="8d7f7-237">De forma predeterminada, se agregan al diseñador un nuevo control DataGridView (**categoryDataGridView**) y controles de la barra de herramientas de navegación.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-237">By default, a new DataGridView (**categoryDataGridView**) and Navigation toolbar controls are added to the designer.</span></span> <span data-ttu-id="8d7f7-238">Estos controles están enlazados a los componentes BindingSource (**categoryBindingSource**) y navegador de enlace (**categoryBindingNavigator**) que también se crean.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-238">These controls are bound to the BindingSource (**categoryBindingSource**) and Binding Navigator (**categoryBindingNavigator**) components that are created as well.</span></span>
-   <span data-ttu-id="8d7f7-239">Edite las columnas en el **categoryDataGridView**.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-239">Edit the columns on the **categoryDataGridView**.</span></span> <span data-ttu-id="8d7f7-240">Queremos establecer la columna **CategoryID** en solo lectura.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-240">We want to set the **CategoryId** column to read-only.</span></span> <span data-ttu-id="8d7f7-241">La base de datos genera el valor de la propiedad **CategoryID** después de guardar los datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-241">The value for the **CategoryId** property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="8d7f7-242">Haga clic con el botón secundario en el control DataGridView y seleccione Editar columnas...</span><span class="sxs-lookup"><span data-stu-id="8d7f7-242">Right-click the DataGridView control and select Edit Columns…</span></span>
    -   <span data-ttu-id="8d7f7-243">Seleccione la columna CategoryId y establezca ReadOnly en true.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-243">Select the CategoryId column and set ReadOnly to True</span></span>
    -   <span data-ttu-id="8d7f7-244">Presione Aceptar</span><span class="sxs-lookup"><span data-stu-id="8d7f7-244">Press OK</span></span>
-   <span data-ttu-id="8d7f7-245">Seleccione productos en el origen de datos de la categoría y arrástrelos en el formulario.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-245">Select Products from under the Category data source and drag it on the form.</span></span> <span data-ttu-id="8d7f7-246">ProductDataGridView y productBindingSource se agregan al formulario.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-246">The productDataGridView and productBindingSource are added to the form.</span></span>
-   <span data-ttu-id="8d7f7-247">Edite las columnas en el productDataGridView.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-247">Edit the columns on the productDataGridView.</span></span> <span data-ttu-id="8d7f7-248">Queremos ocultar las columnas CategoryId y categoría y establecer ProductId en solo lectura.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-248">We want to hide the CategoryId and Category columns and set ProductId to read-only.</span></span> <span data-ttu-id="8d7f7-249">La base de datos genera el valor de la propiedad ProductId después de guardar los datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-249">The value for the ProductId property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="8d7f7-250">Haga clic con el botón secundario en el control DataGridView y seleccione **Editar columnas.**...</span><span class="sxs-lookup"><span data-stu-id="8d7f7-250">Right-click the DataGridView control and select **Edit Columns...**.</span></span>
    -   <span data-ttu-id="8d7f7-251">Seleccione la columna **ProductID** y establezca **ReadOnly** en **true**.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-251">Select the **ProductId** column and set **ReadOnly** to **True**.</span></span>
    -   <span data-ttu-id="8d7f7-252">Seleccione la columna **CategoryID** y presione el botón **quitar** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-252">Select the **CategoryId** column and press the **Remove** button.</span></span> <span data-ttu-id="8d7f7-253">Haga lo mismo con la columna **categoría** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-253">Do the same with the **Category** column.</span></span>
    -   <span data-ttu-id="8d7f7-254">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-254">Press **OK**.</span></span>

    <span data-ttu-id="8d7f7-255">Hasta ahora, asociamos nuestros controles DataGridView a componentes BindingSource en el diseñador.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-255">So far, we associated our DataGridView controls with BindingSource components in the designer.</span></span> <span data-ttu-id="8d7f7-256">En la siguiente sección, agregaremos código al código subyacente para establecer categoryBindingSource. DataSource en la colección de entidades cuyo seguimiento realiza actualmente DbContext.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-256">In the next section we will add code to the code behind to set categoryBindingSource.DataSource to the collection of entities that are currently tracked by DbContext.</span></span> <span data-ttu-id="8d7f7-257">Cuando hemos arrastrado y colocado productos desde la categoría, el WinForms se encarga de configurar la propiedad productsBindingSource. DataSource en categoryBindingSource y la propiedad productsBindingSource. DataMember en Products.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-257">When we dragged-and-dropped Products from under the Category, the WinForms took care of setting up the productsBindingSource.DataSource property to categoryBindingSource and productsBindingSource.DataMember property to Products.</span></span> <span data-ttu-id="8d7f7-258">Debido a este enlace, solo se mostrarán en el productDataGridView los productos que pertenecen a la categoría seleccionada actualmente.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-258">Because of this binding, only the products that belong to the currently selected Category will be displayed in the productDataGridView.</span></span>
-   <span data-ttu-id="8d7f7-259">Habilite el botón **Guardar** en la barra de herramientas de navegación; para ello, haga clic con el botón secundario del mouse y seleccione **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-259">Enable the **Save** button on the Navigation toolbar by clicking the right mouse button and selecting **Enabled**.</span></span>

    ![Diseñador de formulario 1](~/ef6/media/form1-designer.png)

-   <span data-ttu-id="8d7f7-261">Agregue el controlador de eventos para el botón Guardar haciendo doble clic en el botón.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-261">Add the event handler for the save button by double-clicking on the button.</span></span> <span data-ttu-id="8d7f7-262">Esto agregará el controlador de eventos y le llevará al código subyacente para el formulario.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-262">This will add the event handler and bring you to the code behind for the form.</span></span> <span data-ttu-id="8d7f7-263">El código del controlador de eventos \*\* \_ click de categoryBindingNavigatorSaveItem\*\* se agregará en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-263">The code for the **categoryBindingNavigatorSaveItem\_Click** event handler will be added in the next section.</span></span>

## <a name="add-the-code-that-handles-data-interaction"></a><span data-ttu-id="8d7f7-264">Agregar el código que controla la interacción de datos</span><span class="sxs-lookup"><span data-stu-id="8d7f7-264">Add the Code that Handles Data Interaction</span></span>

<span data-ttu-id="8d7f7-265">Ahora agregaremos el código para usar el ProductContext para realizar el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-265">We'll now add the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="8d7f7-266">Actualice el código para la ventana de formulario principal, como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-266">Update the code for the main form window as shown below.</span></span>

<span data-ttu-id="8d7f7-267">El código declara una instancia de ejecución prolongada de ProductContext.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-267">The code declares a long-running instance of ProductContext.</span></span> <span data-ttu-id="8d7f7-268">El objeto ProductContext se usa para consultar y guardar datos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-268">The ProductContext object is used to query and save data to the database.</span></span> <span data-ttu-id="8d7f7-269">A continuación, se llama al método Dispose () en la instancia de ProductContext desde el método de cierre de sesión reemplazado.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-269">The Dispose() method on the ProductContext instance is then called from the overridden OnClosing method.</span></span> <span data-ttu-id="8d7f7-270">Los comentarios de código proporcionan detalles sobre lo que hace el código.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-270">The code comments provide details about what the code does.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a><span data-ttu-id="8d7f7-271">Prueba de la aplicación Windows Forms</span><span class="sxs-lookup"><span data-stu-id="8d7f7-271">Test the Windows Forms Application</span></span>

-   <span data-ttu-id="8d7f7-272">Compile y ejecute la aplicación y puede probar la funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-272">Compile and run the application and you can test out the functionality.</span></span>

    ![Formulario 1 antes de guardar](~/ef6/media/form1beforesave.png)

-   <span data-ttu-id="8d7f7-274">Después de guardar las claves generadas por el almacén, se muestran en la pantalla.</span><span class="sxs-lookup"><span data-stu-id="8d7f7-274">After saving the store generated keys are shown on the screen.</span></span>

    ![Formulario 1 después de guardar](~/ef6/media/form1aftersave.png)

-   <span data-ttu-id="8d7f7-276">Si ha usado Code First, también verá que se ha creado una base de datos **WinFormswithEFSample. ProductContext** .</span><span class="sxs-lookup"><span data-stu-id="8d7f7-276">If you used Code First, then you will also see that a **WinFormswithEFSample.ProductContext** database is created for you.</span></span>

    ![Explorador de objetos de servidor](~/ef6/media/serverobjexplorer.png)
