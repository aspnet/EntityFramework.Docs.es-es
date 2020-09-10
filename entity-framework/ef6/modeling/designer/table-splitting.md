---
title: 'División de la tabla del diseñador: EF6'
description: División de tabla del diseñador en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 55642d7c3b4efc7467e8a40e68b4144728df270f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620384"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="4d0c3-103">División de tablas del diseñador</span><span class="sxs-lookup"><span data-stu-id="4d0c3-103">Designer Table Splitting</span></span>
<span data-ttu-id="4d0c3-104">En este tutorial se muestra cómo asignar varios tipos de entidad a una sola tabla modificando un modelo con el Entity Framework Designer (EF Designer).</span><span class="sxs-lookup"><span data-stu-id="4d0c3-104">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="4d0c3-105">Uno de los motivos por los que puede querer usar la división de tablas es retrasar la carga de algunas propiedades al usar la carga diferida para cargar los objetos.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-105">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span><span data-ttu-id="4d0c3-106">Puede separar las propiedades que pueden contener una gran cantidad de datos en una entidad independiente y solo cargarlos cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-106"> You can separate the properties that might contain very large amount of data into a separate entity and only load it when required.</span></span>

<span data-ttu-id="4d0c3-107">En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="4d0c3-109">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4d0c3-109">Prerequisites</span></span>

<span data-ttu-id="4d0c3-110">Para completar este tutorial, necesitará:</span><span class="sxs-lookup"><span data-stu-id="4d0c3-110">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="4d0c3-111">Una versión reciente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-111">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="4d0c3-112">La [base de datos de ejemplo School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="4d0c3-112">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="4d0c3-113">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="4d0c3-113">Set up the Project</span></span>

<span data-ttu-id="4d0c3-114">En este tutorial se usa Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-114">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="4d0c3-115">Abra Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-115">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="4d0c3-116">En el menú **Archivo** , seleccione **Nuevo**y haga clic en **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-116">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="4d0c3-117">En el panel izquierdo, haga clic en Visual C \# y, a continuación, seleccione la plantilla aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-117">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="4d0c3-118">Escriba **TableSplittingSample** como nombre del proyecto y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-118">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="4d0c3-119">Creación de un modelo basado en la base de datos School</span><span class="sxs-lookup"><span data-stu-id="4d0c3-119">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="4d0c3-120">Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-120">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="4d0c3-121">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-121">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="4d0c3-122">Escriba **TableSplittingModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-122">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="4d0c3-123">En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente.**</span><span class="sxs-lookup"><span data-stu-id="4d0c3-123">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="4d0c3-124">Haga clic en nueva conexión.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-124">Click New Connection.</span></span> <span data-ttu-id="4d0c3-125">En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-125">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="4d0c3-126">El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-126">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="4d0c3-127">En el cuadro de diálogo elija los objetos de base de **Tables**datos,   desactive el nodo tablas y Compruebe la tabla **Person** .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-127">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="4d0c3-128">Esto agregará la tabla especificada al modelo **School** .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-128">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="4d0c3-129">Haga clic en **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-129">Click **Finish**.</span></span>

<span data-ttu-id="4d0c3-130">Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="4d0c3-131">Todos los objetos que seleccionó en el cuadro de diálogo **Elija los objetos de base de datos**   se agregan al modelo.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-131">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="4d0c3-132">Asignación de dos entidades a una sola tabla</span><span class="sxs-lookup"><span data-stu-id="4d0c3-132">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="4d0c3-133">En esta sección dividirá la entidad **Person** en dos entidades y, a continuación, las asignará a una sola tabla.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-133">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="4d0c3-134">La entidad **Person** no contiene propiedades que puedan contener grandes cantidades de datos. simplemente se usa como ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-134">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="4d0c3-135">Haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **Agregar nuevo**y haga clic en **entidad**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-135">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="4d0c3-136">Aparecerá el cuadro de diálogo **nueva entidad**   .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-136">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="4d0c3-137">Escriba **HireInfo**   para el **nombre de entidad** y **PersonID** para el nombre de la **propiedad de clave** .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-137">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="4d0c3-138">Haga clic en  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-138">Click **OK**.</span></span>
-   <span data-ttu-id="4d0c3-139">Se crea y se muestra un nuevo tipo de entidad en la superficie de diseño.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-139">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="4d0c3-140">Seleccione la **HireDate**   propiedad HireDate del tipo de entidad **Person**   y presione las teclas **Ctrl + X** .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-140">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="4d0c3-141">Seleccione la entidad **HireInfo**   y presione las teclas **Ctrl + V** .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-141">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="4d0c3-142">Cree una asociación entre **Person** y **HireInfo**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-142">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="4d0c3-143">Para ello, haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **Agregar nuevo**y haga clic en **Asociación**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-143">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="4d0c3-144">Aparece el cuadro de diálogo **Agregar Asociación**   .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-144">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="4d0c3-145">El nombre de **PersonHireInfo** se proporciona de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-145">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="4d0c3-146">Especifique Multiplicity **1 (uno)** en ambos extremos de la relación.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-146">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="4d0c3-147">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-147">Press **OK**.</span></span>

<span data-ttu-id="4d0c3-148">El paso siguiente requiere la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-148">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="4d0c3-149">Si no puede ver esta ventana, haga clic con el botón secundario en la superficie de diseño y seleccione detalles de la **asignación**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-149">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="4d0c3-150">Seleccione el **HireInfo**   tipo de entidad HireInfo y haga clic en \*\* &lt; Agregar una &gt; tabla o vista\*\*   en la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-150">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="4d0c3-151">Seleccione **persona** en la lista desplegable \*\* &lt; Agregar una &gt; tabla o vista\*\*   .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-151">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="4d0c3-152">La lista contiene tablas o vistas a las que se puede asignar la entidad seleccionada.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-152">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="4d0c3-153">Las propiedades adecuadas deben estar asignadas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-153">The appropriate properties should be mapped by default.</span></span>

    ![Propiedades de asignación](~/ef6/media/mapping.png)

-   <span data-ttu-id="4d0c3-155">Seleccione la Asociación **PersonHireInfo** en la superficie de diseño.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-155">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="4d0c3-156">Haga clic con el botón secundario en la asociación en la superficie de diseño y seleccione **propiedades**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-156">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="4d0c3-157">En la ventana **propiedades** , seleccione la propiedad **restricciones referenciales** y haga clic en el botón de puntos suspensivos.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-157">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="4d0c3-158">Seleccione **persona** en la lista desplegable **principal** .</span><span class="sxs-lookup"><span data-stu-id="4d0c3-158">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="4d0c3-159">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-159">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="4d0c3-160">Usar el modelo</span><span class="sxs-lookup"><span data-stu-id="4d0c3-160">Use the Model</span></span>

-   <span data-ttu-id="4d0c3-161">Pegue el código siguiente en el método Main.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-161">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   <span data-ttu-id="4d0c3-162">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-162">Compile and run the application.</span></span>

<span data-ttu-id="4d0c3-163">Las siguientes instrucciones T-SQL se ejecutaron en la base de datos **School** como resultado de la ejecución de esta aplicación.</span><span class="sxs-lookup"><span data-stu-id="4d0c3-163">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="4d0c3-164">La siguiente **inserción** se ejecutó como resultado de la ejecución del contexto. SaveChanges () y combina datos de las entidades **Person** y **HireInfo**</span><span class="sxs-lookup"><span data-stu-id="4d0c3-164">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![Inserción de datos de HireInfo y de combinación de personas](~/ef6/media/insert.png)

-   <span data-ttu-id="4d0c3-166">La siguiente **selección** se ejecutó como resultado de la ejecución del contexto. People. FirstOrDefault () y selecciona solo las columnas asignadas a **Person**</span><span class="sxs-lookup"><span data-stu-id="4d0c3-166">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![Seleccione 1.](~/ef6/media/select1.png)

-   <span data-ttu-id="4d0c3-168">La siguiente **selección** se ejecutó como resultado del acceso a la propiedad de navegación ExistingPerson. instructor y selecciona solo las columnas asignadas a **HireInfo**</span><span class="sxs-lookup"><span data-stu-id="4d0c3-168">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![Seleccione 2](~/ef6/media/select2.png)
