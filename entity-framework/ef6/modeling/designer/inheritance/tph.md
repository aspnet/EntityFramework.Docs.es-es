---
title: 'Herencia TPH del diseñador: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
ms.openlocfilehash: 43ba34a98c3960a7a3052a00e2ed2751c2f2b121
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415236"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="bdbcc-102">Herencia TPH del diseñador</span><span class="sxs-lookup"><span data-stu-id="bdbcc-102">Designer TPH Inheritance</span></span>
<span data-ttu-id="bdbcc-103">En este tutorial paso a paso se muestra cómo implementar la herencia de tabla por jerarquía (TPH) en el modelo conceptual con el Entity Framework Designer (EF Designer).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-103">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="bdbcc-104">La herencia TPH utiliza una tabla de base de datos para mantener los datos de todos los tipos de entidad en una jerarquía de herencia.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-104">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="bdbcc-105">En este tutorial asignaremos la tabla Person a tres tipos de entidad: person (el tipo base), Student (derive de person) y instructor (derive de person).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-105">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="bdbcc-106">Vamos a crear un modelo conceptual a partir de la base de datos (Database First) y, a continuación, modificar el modelo para implementar la herencia TPH mediante el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-106">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="bdbcc-107">Es posible asignar una herencia TPH mediante Model First pero tendría que escribir su propio flujo de trabajo de generación de base de datos, que es complejo.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-107">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="bdbcc-108">A continuación, asignaría este flujo de trabajo a la propiedad **flujo de trabajo de generación de base de datos** en el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-108">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="bdbcc-109">Una alternativa más sencilla es usar Code First.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-109">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="bdbcc-110">Otras opciones de herencia</span><span class="sxs-lookup"><span data-stu-id="bdbcc-110">Other Inheritance Options</span></span>

<span data-ttu-id="bdbcc-111">Tabla por tipo (TPT) es otro tipo de herencia en la que las tablas independientes de la base de datos se asignan a las entidades que participan en la herencia.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-111">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="bdbcc-112"> Para obtener información sobre cómo asignar la herencia de tabla por tipo con el diseñador de EF, consulte [herencia del diseñador de EF](~/ef6/modeling/designer/inheritance/tpt.md).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-112"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](~/ef6/modeling/designer/inheritance/tpt.md).</span></span>

<span data-ttu-id="bdbcc-113">El tiempo de ejecución de Entity Framework admite la herencia de tipo de tabla por hormigón (TPC) y los modelos de herencia mixto, pero no son compatibles con el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-113">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="bdbcc-114">Si desea utilizar la herencia de TPC o mixta, tiene dos opciones: usar Code First o editar manualmente el archivo EDMX.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="bdbcc-115">Si decide trabajar con el archivo EDMX, la ventana detalles de la asignación se colocará en "modo seguro" y no podrá usar el diseñador para cambiar las asignaciones.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bdbcc-116">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="bdbcc-116">Prerequisites</span></span>

<span data-ttu-id="bdbcc-117">Para completar este tutorial, necesitará:</span><span class="sxs-lookup"><span data-stu-id="bdbcc-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="bdbcc-118">Una versión reciente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="bdbcc-119">La [base de datos de ejemplo School](~/ef6/resources/school-database.md).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-119">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="bdbcc-120">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="bdbcc-120">Set up the Project</span></span>

-   <span data-ttu-id="bdbcc-121">Abra Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="bdbcc-122">Seleccionar **archivo-&gt; nuevo-&gt; proyecto**</span><span class="sxs-lookup"><span data-stu-id="bdbcc-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="bdbcc-123">En el panel izquierdo, haga clic en **Visual C\#** y, a continuación, seleccione la plantilla de **consola** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="bdbcc-124">Escriba **TPHDBFirstSample** como nombre.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-124">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="bdbcc-125">Seleccione  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="bdbcc-126">Creación de un modelo</span><span class="sxs-lookup"><span data-stu-id="bdbcc-126">Create a Model</span></span>

-   <span data-ttu-id="bdbcc-127">Haga clic con el botón derecho en el nombre del proyecto en Explorador de soluciones y seleccione **agregar&gt; nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-127">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="bdbcc-128">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="bdbcc-129">Escriba **TPHModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-129">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="bdbcc-130">En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-130">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="bdbcc-131">Haga clic en **nueva conexión**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-131">Click **New Connection**.</span></span>
    <span data-ttu-id="bdbcc-132">En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **LocalDB)\\mssqllocaldb**), seleccione el método de autenticación, escriba **School** para el nombre de la base de datos y, a continuación, haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="bdbcc-133">El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="bdbcc-134">En el cuadro de diálogo elija los objetos de base de datos, en el nodo tablas, seleccione la tabla **Person** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="bdbcc-135">Haga clic en **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-135">Click **Finish**.</span></span>

<span data-ttu-id="bdbcc-136">Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="bdbcc-137">Todos los objetos que seleccionó en el cuadro de diálogo elija los objetos de base de datos se agregan al modelo.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="bdbcc-138">Este es el aspecto de la tabla **Person** en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-138">That is how the **Person** table looks in the database.</span></span>

![Tabla Person](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="bdbcc-140">Implementar la herencia de tabla por jerarquía</span><span class="sxs-lookup"><span data-stu-id="bdbcc-140">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="bdbcc-141">La tabla **Person** tiene la columna **Discriminator** , que puede tener uno de dos valores: "Student" y "instructor".</span><span class="sxs-lookup"><span data-stu-id="bdbcc-141">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="bdbcc-142">En función del valor, la tabla **Person** se asignará a la entidad **Student** o a la entidad **instructor** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-142">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="bdbcc-143">La tabla **Person** también tiene dos columnas, **HireDate** y **EnrollmentDate**, que deben **admitir valores NULL** porque una persona no puede ser Student ni un instructor al mismo tiempo (al menos no en este tutorial).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-143">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="bdbcc-144">Agregar nuevas entidades</span><span class="sxs-lookup"><span data-stu-id="bdbcc-144">Add new Entities</span></span>

-   <span data-ttu-id="bdbcc-145">Agregue una nueva entidad.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-145">Add a new entity.</span></span>
    <span data-ttu-id="bdbcc-146">Para ello, haga clic con el botón secundario en un espacio vacío de la superficie de diseño del Entity Framework Designer y seleccione **agregar&gt;entidad**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-146">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="bdbcc-147">Escriba **Instructor** para el **nombre de entidad** y seleccione **Person** en la lista desplegable del **tipo base**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-147">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="bdbcc-148">Haga clic en  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-148">Click **OK**.</span></span>
-   <span data-ttu-id="bdbcc-149">Agregue otra entidad nueva.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-149">Add another new entity.</span></span> <span data-ttu-id="bdbcc-150">Escriba **Student** para el **nombre de entidad** y seleccione **Person** en la lista desplegable del **tipo base**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-150">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="bdbcc-151">Se han agregado dos nuevos tipos de entidad a la superficie de diseño.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-151">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="bdbcc-152">Una flecha señala desde los nuevos tipos de entidad hasta la **persona** tipo de entidad; Esto indica que **Person** es el tipo base para los nuevos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-152">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="bdbcc-153">Haga clic con el botón secundario en la propiedad **HireDate** de la entidad  **Person** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-153">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="bdbcc-154">Seleccione **cortar** (o use la tecla Ctrl-X).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-154">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="bdbcc-155">Haga clic con el botón secundario en la entidad del **instructor** y seleccione **pegar** (o use la tecla Ctrl-V).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-155">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="bdbcc-156">Haga clic con el botón secundario en la propiedad **HireDate** y seleccione **propiedades**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-156">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="bdbcc-157">En la ventana **propiedades** , establezca la propiedad **Nullable** en **false**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-157">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="bdbcc-158">Haga clic con el botón secundario en la propiedad **EnrollmentDate** de la entidad **Person** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-158">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="bdbcc-159">Seleccione **cortar** (o use la tecla Ctrl-X).</span><span class="sxs-lookup"><span data-stu-id="bdbcc-159">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="bdbcc-160">Haga clic con el botón secundario en la entidad **Student** y seleccione **pegar (o use la tecla Ctrl-V).**</span><span class="sxs-lookup"><span data-stu-id="bdbcc-160">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="bdbcc-161">Seleccione la propiedad **EnrollmentDate** y establezca la propiedad **Nullable** en **false**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-161">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="bdbcc-162">Seleccione la **persona** tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-162">Select the **Person** entity type.</span></span> <span data-ttu-id="bdbcc-163">En la ventana **propiedades** , establezca su propiedad **abstract** en **true**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-163">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="bdbcc-164">Elimine la propiedad **Discriminator** de **Person**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-164">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="bdbcc-165">La razón por la que se debe eliminar se explica en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-165">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="bdbcc-166">Asignación de las entidades</span><span class="sxs-lookup"><span data-stu-id="bdbcc-166">Map the entities</span></span>

-   <span data-ttu-id="bdbcc-167">Haga clic con el botón secundario en el **instructor** y seleccione **asignación de tabla.**</span><span class="sxs-lookup"><span data-stu-id="bdbcc-167">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="bdbcc-168">La entidad instructor está seleccionada en la ventana detalles de la asignación.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-168">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="bdbcc-169">Haga clic en **&lt;agregar una tabla o vista&gt;**  en la ventana detalles de la **asignación** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-169">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="bdbcc-170">El **&lt;agregar una tabla o vista&gt;** campo se convierte en una lista desplegable de tablas o vistas a las que se puede asignar la entidad seleccionada.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-170">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="bdbcc-171">Seleccione **Person** en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-171">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="bdbcc-172">La ventana detalles de la **asignación** se actualiza con las asignaciones de columnas predeterminadas y una opción para agregar una condición.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-172">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="bdbcc-173">Haga clic en **&lt;agregar una condición&gt;** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-173">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="bdbcc-174">El **&lt;agregar una condición&gt;** campo se convierte en una lista desplegable de columnas para las que se pueden establecer condiciones.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-174">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="bdbcc-175">Seleccione **discriminator** en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-175">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="bdbcc-176">En la columna **operador** de la ventana detalles de la **asignación** , seleccione = en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-176">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="bdbcc-177">En la columna **valor/propiedad** , escriba **instructor**.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-177">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="bdbcc-178">El resultado final debería ser similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="bdbcc-178">The end result should look like this:</span></span>

    ![Detalles de la asignación](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="bdbcc-180">Repita estos pasos para el tipo de entidad **student** , pero haga que la condición sea igual que el valor **Student** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-180">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="bdbcc-181">*La razón por la que queríamos quitar la propiedad **Discriminator** , es que no se puede asignar más de una vez una columna de tabla. Esta columna se utilizará para la asignación condicional, por lo que no se puede usar también para la asignación de propiedades. La única manera en que se puede usar para ambos, si una condición usa **es null** o **no es null** comparación.*</span><span class="sxs-lookup"><span data-stu-id="bdbcc-181">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="bdbcc-182">Ahora se implementa la herencia de tabla por jerarquía.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-182">Table-per-hierarchy inheritance is now implemented.</span></span>

![TPH final](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="bdbcc-184">Usar el modelo</span><span class="sxs-lookup"><span data-stu-id="bdbcc-184">Use the Model</span></span>

<span data-ttu-id="bdbcc-185">Abra el archivo **Program.CS** en el que se define el método **Main** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-185">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="bdbcc-186">Pegue el código siguiente en la función **Main** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-186">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="bdbcc-187">El código ejecuta tres consultas.</span><span class="sxs-lookup"><span data-stu-id="bdbcc-187">The code executes three queries.</span></span> <span data-ttu-id="bdbcc-188">La primera consulta devuelve todos los objetos **Person** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-188">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="bdbcc-189">La segunda consulta utiliza el método **Intype** para devolver los objetos **instructor** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-189">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="bdbcc-190">La tercera consulta utiliza el método **Intype** para devolver objetos **Student** .</span><span class="sxs-lookup"><span data-stu-id="bdbcc-190">The third query uses the **OfType** method to return **Student** objects.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
