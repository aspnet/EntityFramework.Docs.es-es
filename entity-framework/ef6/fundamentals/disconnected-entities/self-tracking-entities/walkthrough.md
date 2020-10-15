---
title: 'Self-Tracking de entidades Tutorial: EF6'
description: Tutorial de entidades de seguimiento propio para Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 7c50bbc9fad10a474728b03e79b685c549cf675d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065607"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="7408e-103">Tutorial de Self-Tracking entidades</span><span class="sxs-lookup"><span data-stu-id="7408e-103">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="7408e-104">Ya no se recomienda usar la plantilla Entidades de autoseguimiento.</span><span class="sxs-lookup"><span data-stu-id="7408e-104">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="7408e-105">Solo sigue estando disponible para la compatibilidad con las aplicaciones existentes.</span><span class="sxs-lookup"><span data-stu-id="7408e-105">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="7408e-106">Si la aplicación necesita trabajar con gráficos desconectados de entidades, considere otras alternativas, como [Trackable Entities](https://trackableentities.github.io/), que es una tecnología similar a Entidades de autoseguimiento pero que la comunidad desarrolla de forma más activa, o escriba código personalizado mediante la API de seguimiento de cambios de bajo nivel.</span><span class="sxs-lookup"><span data-stu-id="7408e-106">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](https://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="7408e-107">En este tutorial se muestra el escenario en el que un servicio de Windows Communication Foundation (WCF) expone una operación que devuelve un gráfico de entidades.</span><span class="sxs-lookup"><span data-stu-id="7408e-107">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="7408e-108">A continuación, una aplicación cliente manipula dicho gráfico y envía las modificaciones a una operación de servicio que valida y guarda las actualizaciones en una base de datos mediante Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7408e-108">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="7408e-109">Antes de completar este tutorial, asegúrese de leer la página [entidades de seguimiento propio](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) .</span><span class="sxs-lookup"><span data-stu-id="7408e-109">Before completing this walkthrough make sure you read the [Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) page.</span></span>

<span data-ttu-id="7408e-110">Este tutorial realiza las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="7408e-110">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="7408e-111">Crea una base de datos a la que se tiene acceso.</span><span class="sxs-lookup"><span data-stu-id="7408e-111">Creates a database to access.</span></span>
-   <span data-ttu-id="7408e-112">Crea una biblioteca de clases que contiene el modelo.</span><span class="sxs-lookup"><span data-stu-id="7408e-112">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="7408e-113">Intercambia la plantilla Self-Tracking Entity generator.</span><span class="sxs-lookup"><span data-stu-id="7408e-113">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="7408e-114">Mueve las clases de entidad a un proyecto independiente.</span><span class="sxs-lookup"><span data-stu-id="7408e-114">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="7408e-115">Crea un servicio WCF que expone las operaciones para consultar y guardar entidades.</span><span class="sxs-lookup"><span data-stu-id="7408e-115">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="7408e-116">Crea aplicaciones cliente (consola y WPF) que consumen el servicio.</span><span class="sxs-lookup"><span data-stu-id="7408e-116">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="7408e-117">Usaremos Database First en este tutorial, pero las mismas técnicas se aplican igualmente a Model First.</span><span class="sxs-lookup"><span data-stu-id="7408e-117">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7408e-118">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7408e-118">Pre-Requisites</span></span>

<span data-ttu-id="7408e-119">Para completar este tutorial, necesitará una versión reciente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7408e-119">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="7408e-120">Crear una base de datos</span><span class="sxs-lookup"><span data-stu-id="7408e-120">Create a Database</span></span>

<span data-ttu-id="7408e-121">El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:</span><span class="sxs-lookup"><span data-stu-id="7408e-121">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="7408e-122">Si usa Visual Studio 2012, va a crear una base de datos de LocalDB.</span><span class="sxs-lookup"><span data-stu-id="7408e-122">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="7408e-123">Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.</span><span class="sxs-lookup"><span data-stu-id="7408e-123">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="7408e-124">Vamos a generar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7408e-124">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="7408e-125">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7408e-125">Open Visual Studio</span></span>
-   <span data-ttu-id="7408e-126">**Vista &gt; Explorador de servidores**</span><span class="sxs-lookup"><span data-stu-id="7408e-126">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="7408e-127">Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**</span><span class="sxs-lookup"><span data-stu-id="7408e-127">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="7408e-128">Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar **Microsoft SQL Server** como origen de datos</span><span class="sxs-lookup"><span data-stu-id="7408e-128">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="7408e-129">Conéctese a LocalDB o a SQL Express, en función de la que haya instalado.</span><span class="sxs-lookup"><span data-stu-id="7408e-129">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="7408e-130">Escriba **STESample** como nombre de la base de datos</span><span class="sxs-lookup"><span data-stu-id="7408e-130">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="7408e-131">Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .</span><span class="sxs-lookup"><span data-stu-id="7408e-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="7408e-132">La nueva base de datos aparecerá ahora en Explorador de servidores</span><span class="sxs-lookup"><span data-stu-id="7408e-132">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="7408e-133">Si usa Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="7408e-133">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="7408e-134">Haga clic con el botón derecho en la base de datos en el Explorador de servidores y seleccione **Nueva consulta**</span><span class="sxs-lookup"><span data-stu-id="7408e-134">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="7408e-135">Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="7408e-136">Si usa Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="7408e-136">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="7408e-137">Seleccionar **datos: &gt; Editor de Transact SQL: &gt; nueva conexión de consulta...**</span><span class="sxs-lookup"><span data-stu-id="7408e-137">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="7408e-138">Escriba **. \\ SQLEXPRESS** como nombre del servidor y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="7408e-138">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="7408e-139">Seleccione la base de datos **STESample** en la lista desplegable de la parte superior del editor de consultas.</span><span class="sxs-lookup"><span data-stu-id="7408e-139">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="7408e-140">Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **ejecutar SQL** .</span><span class="sxs-lookup"><span data-stu-id="7408e-140">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
    CREATE TABLE [dbo].[Blogs] (
        [BlogId] INT IDENTITY (1, 1) NOT NULL,
        [Name] NVARCHAR (200) NULL,
        [Url]  NVARCHAR (200) NULL,
        CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
    );

    CREATE TABLE [dbo].[Posts] (
        [PostId] INT IDENTITY (1, 1) NOT NULL,
        [Title] NVARCHAR (200) NULL,
        [Content] NTEXT NULL,
        [BlogId] INT NOT NULL,
        CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
        CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
    );

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a><span data-ttu-id="7408e-141">Crear el modelo</span><span class="sxs-lookup"><span data-stu-id="7408e-141">Create the Model</span></span>

<span data-ttu-id="7408e-142">En primer lugar, necesitamos un proyecto en el que colocar el modelo.</span><span class="sxs-lookup"><span data-stu-id="7408e-142">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="7408e-143">**Archivo- &gt; nuevo- &gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="7408e-143">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="7408e-144">Seleccione \*\*Visual C \# \*\* en el panel izquierdo y, a continuación, **biblioteca de clases** .</span><span class="sxs-lookup"><span data-stu-id="7408e-144">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="7408e-145">Escriba **STESample** como nombre y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-145">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="7408e-146">Ahora vamos a crear un modelo simple en EF Designer para tener acceso a la base de datos:</span><span class="sxs-lookup"><span data-stu-id="7408e-146">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="7408e-147">**Proyecto- &gt; Agregar nuevo elemento...**</span><span class="sxs-lookup"><span data-stu-id="7408e-147">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="7408e-148">Seleccione **datos** en el panel izquierdo y, a continuación, **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="7408e-148">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="7408e-149">Escriba **BloggingModel** como nombre y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-149">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="7408e-150">Seleccione **generar desde la base de datos** y haga clic en **siguiente** .</span><span class="sxs-lookup"><span data-stu-id="7408e-150">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="7408e-151">Escriba la información de conexión de la base de datos que creó en la sección anterior</span><span class="sxs-lookup"><span data-stu-id="7408e-151">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="7408e-152">Escriba **BloggingContext** como nombre de la cadena de conexión y haga clic en **siguiente** .</span><span class="sxs-lookup"><span data-stu-id="7408e-152">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="7408e-153">Active la casilla situada junto a **tablas** y haga clic en **Finalizar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-153">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="7408e-154">Intercambio a la generación de código de STE</span><span class="sxs-lookup"><span data-stu-id="7408e-154">Swap to STE Code Generation</span></span>

<span data-ttu-id="7408e-155">Ahora es necesario deshabilitar la generación de código y el intercambio predeterminados para Self-Tracking entidades.</span><span class="sxs-lookup"><span data-stu-id="7408e-155">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="7408e-156">Si usa Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="7408e-156">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="7408e-157">Expanda **BloggingModel. edmx** en **Explorador de soluciones** y elimine **BloggingModel.TT** y **BloggingModel.Context.TT**; de 
     *esta forma, se deshabilitará la generación de código predeterminada*</span><span class="sxs-lookup"><span data-stu-id="7408e-157">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="7408e-158">Haga clic con el botón secundario en un área vacía de la superficie de EF Designer y seleccione **Agregar elemento de generación de código..** .</span><span class="sxs-lookup"><span data-stu-id="7408e-158">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="7408e-159">Seleccione **en línea** en el panel izquierdo y busque el **generador Ste** .</span><span class="sxs-lookup"><span data-stu-id="7408e-159">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="7408e-160">Seleccione el **generador Ste para la \# plantilla de C** , escriba **STETemplate** como nombre y haga clic en **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-160">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="7408e-161">Los archivos **STETemplate.TT** y **STETemplate.Context.TT** se agregan anidados en el archivo BloggingModel. edmx.</span><span class="sxs-lookup"><span data-stu-id="7408e-161">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="7408e-162">Si usa Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="7408e-162">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="7408e-163">Haga clic con el botón secundario en un área vacía de la superficie de EF Designer y seleccione **Agregar elemento de generación de código..** .</span><span class="sxs-lookup"><span data-stu-id="7408e-163">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="7408e-164">Seleccione **código** en el panel izquierdo y, a continuación, **ADO.net Self-Tracking Entity generator**</span><span class="sxs-lookup"><span data-stu-id="7408e-164">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="7408e-165">Escriba **STETemplate** como nombre y haga clic en **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-165">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="7408e-166">Los archivos **STETemplate.TT** y **STETemplate.Context.TT** se agregan directamente al proyecto</span><span class="sxs-lookup"><span data-stu-id="7408e-166">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="7408e-167">Traslado de tipos de entidad a un proyecto independiente</span><span class="sxs-lookup"><span data-stu-id="7408e-167">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="7408e-168">Para usar Self-Tracking entidades, nuestra aplicación cliente necesita acceso a las clases de entidad generadas a partir de nuestro modelo.</span><span class="sxs-lookup"><span data-stu-id="7408e-168">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="7408e-169">Dado que no queremos exponer todo el modelo a la aplicación cliente, vamos a trasladar las clases de entidad a un proyecto independiente.</span><span class="sxs-lookup"><span data-stu-id="7408e-169">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="7408e-170">El primer paso es dejar de generar las clases de entidad en el proyecto existente:</span><span class="sxs-lookup"><span data-stu-id="7408e-170">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="7408e-171">Haga clic con el botón derecho en **STETemplate.TT** en **Explorador de soluciones** y seleccione **propiedades** .</span><span class="sxs-lookup"><span data-stu-id="7408e-171">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="7408e-172">En la ventana **propiedades** , desactive **TextTemplatingFileGenerator** de la propiedad **CustomTool** .</span><span class="sxs-lookup"><span data-stu-id="7408e-172">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="7408e-173">Expandir **STETemplate.TT** en **Explorador de soluciones** y eliminar todos los archivos anidados en él</span><span class="sxs-lookup"><span data-stu-id="7408e-173">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="7408e-174">A continuación, vamos a agregar un nuevo proyecto y a generar las clases de entidad en él.</span><span class="sxs-lookup"><span data-stu-id="7408e-174">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="7408e-175">**Archivo- &gt; agregar- &gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="7408e-175">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="7408e-176">Seleccione \*\*Visual C \# \*\* en el panel izquierdo y, a continuación, **biblioteca de clases** .</span><span class="sxs-lookup"><span data-stu-id="7408e-176">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="7408e-177">Escriba **STESample. Entities** como nombre y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-177">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="7408e-178">**Proyecto: &gt; Agregar elemento existente...**</span><span class="sxs-lookup"><span data-stu-id="7408e-178">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="7408e-179">Navegue hasta la carpeta del proyecto **STESample**</span><span class="sxs-lookup"><span data-stu-id="7408e-179">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="7408e-180">Seleccione esta información para ver **todos los archivos ( \* . \* )**</span><span class="sxs-lookup"><span data-stu-id="7408e-180">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="7408e-181">Seleccione el archivo **STETemplate.TT**</span><span class="sxs-lookup"><span data-stu-id="7408e-181">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="7408e-182">Haga clic en la flecha desplegable situada junto al botón **Agregar** y seleccione **Agregar como vínculo** .</span><span class="sxs-lookup"><span data-stu-id="7408e-182">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![Agregar plantilla vinculada](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="7408e-184">También vamos a asegurarnos de que las clases de entidad se generan en el mismo espacio de nombres que el contexto.</span><span class="sxs-lookup"><span data-stu-id="7408e-184">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="7408e-185">Esto solo reduce el número de instrucciones Using que necesitamos agregar en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7408e-185">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="7408e-186">Haga clic con el botón derecho en el **STETemplate.TT** vinculado en **Explorador de soluciones** y seleccione **propiedades** .</span><span class="sxs-lookup"><span data-stu-id="7408e-186">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="7408e-187">En la ventana **propiedades** , establezca el espacio de nombres de la **herramienta personalizada** en **STESample**</span><span class="sxs-lookup"><span data-stu-id="7408e-187">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="7408e-188">El código generado por la plantilla STE necesitará una referencia a **System. Runtime. Serialization** para compilar.</span><span class="sxs-lookup"><span data-stu-id="7408e-188">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="7408e-189">Esta biblioteca es necesaria para los atributos **DataContract** y **DataMember** de WCF que se utilizan en los tipos de entidad serializables.</span><span class="sxs-lookup"><span data-stu-id="7408e-189">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="7408e-190">Haga clic con el botón derecho en el proyecto **STESample. Entities** en **Explorador de soluciones** y seleccione **Agregar referencia..** .</span><span class="sxs-lookup"><span data-stu-id="7408e-190">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="7408e-191">En Visual Studio 2012: Active la casilla situada junto a **System. Runtime. Serialization** y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-191">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="7408e-192">En Visual Studio 2010: seleccione **System. Runtime. Serialization** y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-192">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="7408e-193">Por último, el proyecto con nuestro contexto en él necesitará una referencia a los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="7408e-193">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="7408e-194">Haga clic con el botón derecho en el proyecto **STESample** en **Explorador de soluciones** y seleccione **Agregar referencia..** .</span><span class="sxs-lookup"><span data-stu-id="7408e-194">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="7408e-195">En Visual Studio 2012: seleccione **solución** en el panel izquierdo, active la casilla situada junto a **STESample. entidades** y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-195">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="7408e-196">En Visual Studio 2010: seleccione la pestaña **proyectos** , seleccione **STESample. Entities** y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-196">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="7408e-197">Otra opción para mover los tipos de entidad a un proyecto independiente consiste en mover el archivo de plantilla, en lugar de vincularlo desde su ubicación predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7408e-197">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="7408e-198">Si lo hace, tendrá que actualizar la variable **ArchivoDeEntrada** en la plantilla para proporcionar la ruta de acceso relativa al archivo edmx (en este ejemplo sería **.. \\ BloggingModel. edmx**).</span><span class="sxs-lookup"><span data-stu-id="7408e-198">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="7408e-199">Crear un servicio WCF</span><span class="sxs-lookup"><span data-stu-id="7408e-199">Create a WCF Service</span></span>

<span data-ttu-id="7408e-200">Ahora es el momento de agregar un servicio WCF para exponer nuestros datos, comenzaremos por crear el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7408e-200">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="7408e-201">**Archivo- &gt; agregar- &gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="7408e-201">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="7408e-202">Seleccione \*\*Visual C \# \*\* en el panel izquierdo y, a continuación, **aplicación de servicio WCF** .</span><span class="sxs-lookup"><span data-stu-id="7408e-202">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="7408e-203">Escriba **STESample. Service** como nombre y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-203">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="7408e-204">Agregar una referencia al ensamblado **System. Data. Entity**</span><span class="sxs-lookup"><span data-stu-id="7408e-204">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="7408e-205">Agregar una referencia a los proyectos **STESample** y **STESample. Entities**</span><span class="sxs-lookup"><span data-stu-id="7408e-205">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="7408e-206">Necesitamos copiar la cadena de conexión de EF en este proyecto para que se encuentre en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="7408e-206">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="7408e-207">Abra el archivo **App.Config** para el proyecto **STESample **y copie el elemento **connectionStrings** .</span><span class="sxs-lookup"><span data-stu-id="7408e-207">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="7408e-208">Pegue el elemento **connectionStrings** como un elemento secundario del elemento de **configuración** del archivo de **Web.Config** en el proyecto **STESample. Service.**</span><span class="sxs-lookup"><span data-stu-id="7408e-208">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="7408e-209">Ahora es el momento de implementar el servicio real.</span><span class="sxs-lookup"><span data-stu-id="7408e-209">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="7408e-210">Abra **IService1.CS** y reemplace el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="7408e-210">Open **IService1.cs** and replace the contents with the following code</span></span>

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   <span data-ttu-id="7408e-211">Abra **Service1. SVC** y reemplace el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="7408e-211">Open **Service1.svc** and replace the contents with the following code</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="7408e-212">Consumo del servicio desde una aplicación de consola</span><span class="sxs-lookup"><span data-stu-id="7408e-212">Consume the Service from a Console Application</span></span>

<span data-ttu-id="7408e-213">Vamos a crear una aplicación de consola que use nuestro servicio.</span><span class="sxs-lookup"><span data-stu-id="7408e-213">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="7408e-214">**Archivo- &gt; nuevo- &gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="7408e-214">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="7408e-215">Seleccione \*\*Visual C \# \*\* en el panel izquierdo y, a continuación, **aplicación de consola**</span><span class="sxs-lookup"><span data-stu-id="7408e-215">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="7408e-216">Escriba **STESample. ConsoleTest** como nombre y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-216">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="7408e-217">Agregar una referencia al proyecto **STESample. Entities**</span><span class="sxs-lookup"><span data-stu-id="7408e-217">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="7408e-218">Necesitamos una referencia de servicio a nuestro servicio WCF</span><span class="sxs-lookup"><span data-stu-id="7408e-218">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="7408e-219">Haga clic con el botón derecho en el proyecto **STESample. ConsoleTest** en **Explorador de soluciones** y seleccione **Agregar referencia de servicio...**</span><span class="sxs-lookup"><span data-stu-id="7408e-219">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="7408e-220">Haga clic en **detectar**</span><span class="sxs-lookup"><span data-stu-id="7408e-220">Click **Discover**</span></span>
-   <span data-ttu-id="7408e-221">Escriba **BloggingService** como espacio de nombres y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-221">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="7408e-222">Ahora podemos escribir código para consumir el servicio.</span><span class="sxs-lookup"><span data-stu-id="7408e-222">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="7408e-223">Abra **Program.CS** y reemplace el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="7408e-223">Open **Program.cs** and replace the contents with the following code.</span></span>

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

<span data-ttu-id="7408e-224">Ahora puede ejecutar la aplicación para verla en acción.</span><span class="sxs-lookup"><span data-stu-id="7408e-224">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="7408e-225">Haga clic con el botón derecho en el proyecto **STESample. ConsoleTest** en **Explorador de soluciones** y seleccione **depurar- &gt; Iniciar nueva instancia** .</span><span class="sxs-lookup"><span data-stu-id="7408e-225">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="7408e-226">Verá el siguiente resultado cuando se ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7408e-226">You'll see the following output when the application executes.</span></span>

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="7408e-227">Consumo del servicio desde una aplicación de WPF</span><span class="sxs-lookup"><span data-stu-id="7408e-227">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="7408e-228">Vamos a crear una aplicación WPF que use nuestro servicio.</span><span class="sxs-lookup"><span data-stu-id="7408e-228">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="7408e-229">**Archivo- &gt; nuevo- &gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="7408e-229">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="7408e-230">Seleccione \*\*Visual C \# \*\* en el panel izquierdo y, a continuación, **aplicación WPF** .</span><span class="sxs-lookup"><span data-stu-id="7408e-230">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="7408e-231">Escriba **STESample. WPFTest** como nombre y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-231">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="7408e-232">Agregar una referencia al proyecto **STESample. Entities**</span><span class="sxs-lookup"><span data-stu-id="7408e-232">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="7408e-233">Necesitamos una referencia de servicio a nuestro servicio WCF</span><span class="sxs-lookup"><span data-stu-id="7408e-233">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="7408e-234">Haga clic con el botón derecho en el proyecto **STESample. WPFTest** en **Explorador de soluciones** y seleccione **Agregar referencia de servicio...**</span><span class="sxs-lookup"><span data-stu-id="7408e-234">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="7408e-235">Haga clic en **detectar**</span><span class="sxs-lookup"><span data-stu-id="7408e-235">Click **Discover**</span></span>
-   <span data-ttu-id="7408e-236">Escriba **BloggingService** como espacio de nombres y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="7408e-236">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="7408e-237">Ahora podemos escribir código para consumir el servicio.</span><span class="sxs-lookup"><span data-stu-id="7408e-237">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="7408e-238">Abra **MainWindow. Xaml** y reemplace el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="7408e-238">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   <span data-ttu-id="7408e-239">Abra el código subyacente de MainWindow (**MainWindow.Xaml.CS**) y reemplace el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="7408e-239">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

<span data-ttu-id="7408e-240">Ahora puede ejecutar la aplicación para verla en acción.</span><span class="sxs-lookup"><span data-stu-id="7408e-240">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="7408e-241">Haga clic con el botón derecho en el proyecto **STESample. WPFTest** en **Explorador de soluciones** y seleccione **depurar- &gt; Iniciar nueva instancia** .</span><span class="sxs-lookup"><span data-stu-id="7408e-241">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="7408e-242">Puede manipular los datos mediante la pantalla y guardarlos a través del servicio mediante el botón **Guardar**</span><span class="sxs-lookup"><span data-stu-id="7408e-242">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![Ventana principal de WPF](~/ef6/media/wpf.png)
