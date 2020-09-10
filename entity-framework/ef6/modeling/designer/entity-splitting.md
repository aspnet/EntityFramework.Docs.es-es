---
title: 'División de entidades del diseñador: EF6'
description: División de entidades del diseñador en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: d3be4e54d4bcd3ca253d8970c612acddf48dbaf4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620525"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="31e6f-103">División de entidades del diseñador</span><span class="sxs-lookup"><span data-stu-id="31e6f-103">Designer Entity Splitting</span></span>
<span data-ttu-id="31e6f-104">En este tutorial se muestra cómo asignar un tipo de entidad a dos tablas modificando un modelo con el Entity Framework Designer (EF Designer).</span><span class="sxs-lookup"><span data-stu-id="31e6f-104">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="31e6f-105">Puede asignar una entidad a varias tablas cuando estas comparten una clave común.</span><span class="sxs-lookup"><span data-stu-id="31e6f-105">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="31e6f-106">Los conceptos que se aplican en la asignación de un tipo de entidad a dos tablas se extienden con facilidad a la asignación de un tipo de entidad a más de dos tablas.</span><span class="sxs-lookup"><span data-stu-id="31e6f-106">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="31e6f-107">En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="31e6f-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="31e6f-109">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="31e6f-109">Prerequisites</span></span>

<span data-ttu-id="31e6f-110">Visual Studio 2012 o Visual Studio 2010, Ultimate, Premium, Professional o Web Express.</span><span class="sxs-lookup"><span data-stu-id="31e6f-110">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="31e6f-111">Crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="31e6f-111">Create the Database</span></span>

<span data-ttu-id="31e6f-112">El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:</span><span class="sxs-lookup"><span data-stu-id="31e6f-112">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="31e6f-113">Si usa Visual Studio 2012, va a crear una base de datos de LocalDB.</span><span class="sxs-lookup"><span data-stu-id="31e6f-113">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="31e6f-114">Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.</span><span class="sxs-lookup"><span data-stu-id="31e6f-114">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="31e6f-115">En primer lugar, vamos a crear una base de datos con dos tablas que se van a combinar en una sola entidad.</span><span class="sxs-lookup"><span data-stu-id="31e6f-115">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="31e6f-116">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31e6f-116">Open Visual Studio</span></span>
-   <span data-ttu-id="31e6f-117">**Vista &gt; Explorador de servidores**</span><span class="sxs-lookup"><span data-stu-id="31e6f-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="31e6f-118">Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**</span><span class="sxs-lookup"><span data-stu-id="31e6f-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="31e6f-119">Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar **Microsoft SQL Server** como origen de datos</span><span class="sxs-lookup"><span data-stu-id="31e6f-119">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="31e6f-120">Conéctese a LocalDB o a SQL Express, en función de la que haya instalado.</span><span class="sxs-lookup"><span data-stu-id="31e6f-120">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="31e6f-121">Escriba **EntitySplitting** como nombre de la base de datos</span><span class="sxs-lookup"><span data-stu-id="31e6f-121">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="31e6f-122">Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="31e6f-123">La nueva base de datos aparecerá ahora en Explorador de servidores</span><span class="sxs-lookup"><span data-stu-id="31e6f-123">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="31e6f-124">Si usa Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="31e6f-124">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="31e6f-125">Haga clic con el botón derecho en la base de datos en el Explorador de servidores y seleccione **Nueva consulta**</span><span class="sxs-lookup"><span data-stu-id="31e6f-125">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="31e6f-126">Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="31e6f-127">Si usa Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="31e6f-127">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="31e6f-128">Seleccionar **datos: &gt; Editor de Transact SQL: &gt; nueva conexión de consulta...**</span><span class="sxs-lookup"><span data-stu-id="31e6f-128">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="31e6f-129">Escriba **. \\ SQLEXPRESS** como nombre del servidor y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="31e6f-129">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="31e6f-130">Seleccione la base de datos **EntitySplitting** en la lista desplegable de la parte superior del editor de consultas.</span><span class="sxs-lookup"><span data-stu-id="31e6f-130">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="31e6f-131">Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **ejecutar SQL** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-131">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a><span data-ttu-id="31e6f-132">Crear el proyecto</span><span class="sxs-lookup"><span data-stu-id="31e6f-132">Create the Project</span></span>

-   <span data-ttu-id="31e6f-133">En el menú **Archivo** , seleccione **Nuevo**y haga clic en **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="31e6f-133">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="31e6f-134">En el panel izquierdo, haga clic en **Visual \# C**y, a continuación, seleccione la plantilla **aplicación de consola** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-134">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="31e6f-135">Escriba **MapEntityToTablesSample** como nombre del proyecto y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="31e6f-135">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="31e6f-136">Haga clic en **no** si se le pide que guarde la consulta SQL creada en la primera sección.</span><span class="sxs-lookup"><span data-stu-id="31e6f-136">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="31e6f-137">Crear un modelo basado en la base de datos</span><span class="sxs-lookup"><span data-stu-id="31e6f-137">Create a Model based on the Database</span></span>

-   <span data-ttu-id="31e6f-138">Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="31e6f-138">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="31e6f-139">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.</span><span class="sxs-lookup"><span data-stu-id="31e6f-139">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="31e6f-140">Escriba **MapEntityToTablesModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="31e6f-140">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="31e6f-141">En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente.**</span><span class="sxs-lookup"><span data-stu-id="31e6f-141">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="31e6f-142">Seleccione la conexión **EntitySplitting** en el menú desplegable y haga clic en **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="31e6f-142">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="31e6f-143">En el cuadro de diálogo elija los objetos de base de datos, active la casilla situada junto al nodo **tablas**   .</span><span class="sxs-lookup"><span data-stu-id="31e6f-143">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="31e6f-144">Se agregarán todas las tablas de la base de datos **EntitySplitting** al modelo.</span><span class="sxs-lookup"><span data-stu-id="31e6f-144">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="31e6f-145">Haga clic en **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="31e6f-145">Click **Finish**.</span></span>

<span data-ttu-id="31e6f-146">Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.</span><span class="sxs-lookup"><span data-stu-id="31e6f-146">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="31e6f-147">Asignación de una entidad a dos tablas</span><span class="sxs-lookup"><span data-stu-id="31e6f-147">Map an Entity to Two Tables</span></span>

<span data-ttu-id="31e6f-148">En este paso, actualizaremos el tipo de entidad **Person** para combinar datos de las tablas **Person** y **PersonInfo** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-148">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="31e6f-149">Seleccione las propiedades de **correo electrónico**   y **teléfono** de la entidad **PersonInfo **y presione las teclas **Ctrl + X** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-149">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="31e6f-150">Seleccione la entidad **Person **y presione las teclas **Ctrl + V** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-150">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="31e6f-151">En la superficie de diseño, seleccione la entidad **PersonInfo**   y presione el botón **eliminar** en el teclado.</span><span class="sxs-lookup"><span data-stu-id="31e6f-151">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="31e6f-152">Haga clic en **no** cuando se le pregunte si desea quitar la tabla **PersonInfo** del modelo, vamos a asignarla a la entidad **Person** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-152">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![Eliminar tablas](~/ef6/media/deletetables.png)

<span data-ttu-id="31e6f-154">Los pasos siguientes requieren la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="31e6f-154">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="31e6f-155">Si no puede ver esta ventana, haga clic con el botón secundario en la superficie de diseño y seleccione detalles de la **asignación**.</span><span class="sxs-lookup"><span data-stu-id="31e6f-155">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="31e6f-156">Seleccione el **Person**   tipo de entidad person y haga clic en \*\* &lt; Agregar una &gt; tabla o vista\*\*   en la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="31e6f-156">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="31e6f-157">Seleccione **PersonInfo **   en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="31e6f-157">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="31e6f-158">La ventana detalles de la **asignación**   se actualiza con las asignaciones de columnas predeterminadas, que son correctas para nuestro escenario.</span><span class="sxs-lookup"><span data-stu-id="31e6f-158">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="31e6f-159">El **Person**   tipo de entidad Person ahora se asigna a las tablas **Person**   y **PersonInfo**   .</span><span class="sxs-lookup"><span data-stu-id="31e6f-159">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![Asignación 2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="31e6f-161">Usar el modelo</span><span class="sxs-lookup"><span data-stu-id="31e6f-161">Use the Model</span></span>

-   <span data-ttu-id="31e6f-162">Pegue el código siguiente en el método Main.</span><span class="sxs-lookup"><span data-stu-id="31e6f-162">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   <span data-ttu-id="31e6f-163">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31e6f-163">Compile and run the application.</span></span>

<span data-ttu-id="31e6f-164">Las siguientes instrucciones T-SQL se ejecutaron en la base de datos como resultado de la ejecución de esta aplicación.</span><span class="sxs-lookup"><span data-stu-id="31e6f-164">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="31e6f-165">Las dos instrucciones **Insert** siguientes se ejecutaron como resultado de la ejecución del contexto. SaveChanges ().</span><span class="sxs-lookup"><span data-stu-id="31e6f-165">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="31e6f-166">Toman los datos de la entidad **Person** y los dividen entre las tablas **Person** y **PersonInfo** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-166">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![Insertar 1](~/ef6/media/insert1.png)

    ![Insertar 2](~/ef6/media/insert2.png)
-   <span data-ttu-id="31e6f-169">La siguiente **selección** se ejecutó como resultado de enumerar las personas en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="31e6f-169">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="31e6f-170">Combina los datos de la tabla **Person** y **PersonInfo** .</span><span class="sxs-lookup"><span data-stu-id="31e6f-170">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![Selección de la combinación de datos person y PersonInfo](~/ef6/media/select.png)
