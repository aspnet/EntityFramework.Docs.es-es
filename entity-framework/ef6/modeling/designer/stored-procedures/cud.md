---
title: 'Procedimientos almacenados del diseñador CUD: EF6'
description: Procedimientos almacenados CUD del diseñador en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: fae795922980452cf062a62e3cbb47de49b49c74
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073219"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="d226c-103">Procedimientos almacenados CUD del diseñador</span><span class="sxs-lookup"><span data-stu-id="d226c-103">Designer CUD Stored Procedures</span></span>

<span data-ttu-id="d226c-104">En este tutorial paso a paso se muestra cómo asignar las \\ operaciones de creación de inserción, actualización y eliminación (CUD) de un tipo de entidad a procedimientos almacenados mediante el Entity Framework Designer (EF Designer).</span><span class="sxs-lookup"><span data-stu-id="d226c-104">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="d226c-105">De forma predeterminada, el Entity Framework genera automáticamente las instrucciones SQL para las operaciones CUD, pero también puede asignar procedimientos almacenados a estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="d226c-105"> By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="d226c-106">Tenga en cuenta que Code First no admite la asignación a funciones o procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="d226c-106">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="d226c-107">Sin embargo, puede llamar a procedimientos almacenados o funciones mediante el método System. Data. Entity. DbSet. SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="d226c-107">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="d226c-108">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d226c-108">For example:</span></span>

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="d226c-109">Consideraciones al asignar las operaciones CUD a procedimientos almacenados</span><span class="sxs-lookup"><span data-stu-id="d226c-109">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="d226c-110">Al asignar las operaciones CUD a los procedimientos almacenados, se aplican las consideraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d226c-110">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span>

- <span data-ttu-id="d226c-111">Si va a asignar una de las operaciones de CUD a un procedimiento almacenado, asígnela todas.</span><span class="sxs-lookup"><span data-stu-id="d226c-111">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="d226c-112">Si no asigna los tres, se producirá un error en las operaciones no asignadas si se ejecuta y se producirá una **UpdateException**   .</span><span class="sxs-lookup"><span data-stu-id="d226c-112">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
- <span data-ttu-id="d226c-113">Debe asignar todos los parámetros del procedimiento almacenado a las propiedades de la entidad.</span><span class="sxs-lookup"><span data-stu-id="d226c-113">You must map every parameter of the stored procedure to entity properties.</span></span>
- <span data-ttu-id="d226c-114">Si el servidor genera el valor de clave principal para la fila insertada, debe volver a asignar este valor a la propiedad clave de la entidad.</span><span class="sxs-lookup"><span data-stu-id="d226c-114">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="d226c-115">En el ejemplo siguiente, el **InsertPerson**   procedimiento almacenado InsertPerson devuelve la clave principal recién creada como parte del conjunto de resultados del procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="d226c-115">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="d226c-116">La clave principal se asigna a la clave de entidad (**PersonID**) mediante la característica \*\* &lt; Agregar enlaces &gt; de resultados\*\*   del diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="d226c-116">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
- <span data-ttu-id="d226c-117">Las llamadas a procedimientos almacenados se asignan 1:1 con las entidades del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="d226c-117">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="d226c-118">Por ejemplo, si implementa una jerarquía de herencia en el modelo conceptual y, a continuación, asigna los procedimientos almacenados CUD para las entidades **primaria** (base) y **secundaria** (derivada), al guardar los cambios **secundarios** solo se llamará a los procedimientos almacenados del **elemento secundario**, no se desencadenarán las llamadas a procedimientos almacenados del **elemento primario**.</span><span class="sxs-lookup"><span data-stu-id="d226c-118">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d226c-119">Prerrequisitos</span><span class="sxs-lookup"><span data-stu-id="d226c-119">Prerequisites</span></span>

<span data-ttu-id="d226c-120">Para completar este tutorial, necesitará:</span><span class="sxs-lookup"><span data-stu-id="d226c-120">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="d226c-121">Una versión reciente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d226c-121">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="d226c-122">La [base de datos de ejemplo School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="d226c-122">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="d226c-123">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="d226c-123">Set up the Project</span></span>

- <span data-ttu-id="d226c-124">Abra Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="d226c-124">Open Visual Studio 2012.</span></span>
- <span data-ttu-id="d226c-125">Seleccionar **archivo- &gt; nuevo- &gt; proyecto**</span><span class="sxs-lookup"><span data-stu-id="d226c-125">Select **File-&gt; New -&gt; Project**</span></span>
- <span data-ttu-id="d226c-126">En el panel izquierdo, haga clic en \*\*Visual C \# \*\*y, a continuación, seleccione la plantilla de **consola** .</span><span class="sxs-lookup"><span data-stu-id="d226c-126">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
- <span data-ttu-id="d226c-127">Escriba **CUDSProcsSample**   como nombre.</span><span class="sxs-lookup"><span data-stu-id="d226c-127">Enter **CUDSProcsSample** as the name.</span></span>
- <span data-ttu-id="d226c-128">Seleccione  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="d226c-128">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="d226c-129">Creación de un modelo</span><span class="sxs-lookup"><span data-stu-id="d226c-129">Create a Model</span></span>

- <span data-ttu-id="d226c-130">Haga clic con el botón derecho en el nombre del proyecto en Explorador de soluciones y seleccione **agregar &gt; nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="d226c-130">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
- <span data-ttu-id="d226c-131">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.</span><span class="sxs-lookup"><span data-stu-id="d226c-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
- <span data-ttu-id="d226c-132">Escriba **CUDSProcs. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="d226c-132">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
- <span data-ttu-id="d226c-133">En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d226c-133">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
- <span data-ttu-id="d226c-134">Haga clic en **nueva conexión**.</span><span class="sxs-lookup"><span data-stu-id="d226c-134">Click **New Connection**.</span></span> <span data-ttu-id="d226c-135">En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="d226c-135">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="d226c-136">El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d226c-136">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
- <span data-ttu-id="d226c-137">En el cuadro de diálogo elija los objetos de base de datos, en el nodo **tablas**   , seleccione la tabla **Person** .</span><span class="sxs-lookup"><span data-stu-id="d226c-137">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
- <span data-ttu-id="d226c-138">Además, seleccione los siguientes procedimientos almacenados en el nodo **procedimientos almacenados y funciones** : **DeletePerson**, **InsertPerson**y **UpdatePerson**.</span><span class="sxs-lookup"><span data-stu-id="d226c-138">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span>
- <span data-ttu-id="d226c-139">A partir de Visual Studio 2012, el diseñador de EF admite la importación masiva de procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="d226c-139">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="d226c-140">La **importación de funciones y procedimientos almacenados seleccionados en el modelo de entidad** está activada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d226c-140">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="d226c-141">Como en este ejemplo tenemos procedimientos almacenados que insertan, actualizan y eliminan tipos de entidad, no queremos importarlos y desactivarán esta casilla.</span><span class="sxs-lookup"><span data-stu-id="d226c-141">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span>

    ![Importar procedimientos de S](~/ef6/media/importsprocs.jpg)

- <span data-ttu-id="d226c-143">Haga clic en **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="d226c-143">Click **Finish**.</span></span>
    <span data-ttu-id="d226c-144">Se muestra el diseñador de EF, que proporciona una superficie de diseño para editar el modelo.</span><span class="sxs-lookup"><span data-stu-id="d226c-144">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="d226c-145">Asignar la entidad Person a procedimientos almacenados</span><span class="sxs-lookup"><span data-stu-id="d226c-145">Map the Person Entity to Stored Procedures</span></span>

- <span data-ttu-id="d226c-146">Haga clic con el botón secundario en el tipo de entidad **Person**   y seleccione **asignación de procedimiento almacenado**.</span><span class="sxs-lookup"><span data-stu-id="d226c-146">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
- <span data-ttu-id="d226c-147">Las asignaciones de procedimientos almacenados aparecen en la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="d226c-147">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
- <span data-ttu-id="d226c-148">Haga clic en \*\* &lt; seleccionar &gt; Insertar función\*\*.</span><span class="sxs-lookup"><span data-stu-id="d226c-148">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="d226c-149">El campo se convierte en una lista desplegable de los procedimientos almacenados del modelo de almacenamiento que se puede asignar a tipos de entidad del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="d226c-149">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="d226c-150">Seleccione **InsertPerson**   en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="d226c-150">Select **InsertPerson** from the drop-down list.</span></span>
- <span data-ttu-id="d226c-151">Aparecen las asignaciones predeterminadas entre los parámetros de procedimiento almacenado y las propiedades de entidad.</span><span class="sxs-lookup"><span data-stu-id="d226c-151">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="d226c-152">Observe que las flechas indican la dirección de la asignación: se proporcionan valores de propiedad como parámetros de procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="d226c-152">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
- <span data-ttu-id="d226c-153">Haga clic en \*\* &lt; agregar &gt; enlace de resultados\*\*.</span><span class="sxs-lookup"><span data-stu-id="d226c-153">Click **&lt;Add Result Binding&gt;**.</span></span>
- <span data-ttu-id="d226c-154">Escriba **NewPersonID**, el nombre del parámetro devuelto por el **InsertPerson**   procedimiento almacenado InsertPerson.</span><span class="sxs-lookup"><span data-stu-id="d226c-154">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="d226c-155">Asegúrese de no escribir espacios iniciales ni finales.</span><span class="sxs-lookup"><span data-stu-id="d226c-155">Make sure not to type leading or trailing spaces.</span></span>
- <span data-ttu-id="d226c-156">Presione **entrar**.</span><span class="sxs-lookup"><span data-stu-id="d226c-156">Press **Enter**.</span></span>
- <span data-ttu-id="d226c-157">De forma predeterminada, **NewPersonID**   se asigna a la clave de entidad **PersonID**.</span><span class="sxs-lookup"><span data-stu-id="d226c-157">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="d226c-158">Observe que una flecha indica la dirección de la asignación: el valor de la columna de resultado se proporciona para la propiedad.</span><span class="sxs-lookup"><span data-stu-id="d226c-158">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![Detalles de la asignación](~/ef6/media/mappingdetails.png)

- <span data-ttu-id="d226c-160">Haga clic en \*\* &lt; seleccionar &gt; función de actualización\*\*   y seleccione **UpdatePerson**   en la lista desplegable resultante.</span><span class="sxs-lookup"><span data-stu-id="d226c-160">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="d226c-161">Aparecen las asignaciones predeterminadas entre los parámetros de procedimiento almacenado y las propiedades de entidad.</span><span class="sxs-lookup"><span data-stu-id="d226c-161">Default mappings between stored procedure parameters and entity properties appear.</span></span>
- <span data-ttu-id="d226c-162">Haga clic en \*\* &lt; seleccionar &gt; eliminar función\*\*   y seleccione **DeletePerson**   en la lista desplegable resultante.</span><span class="sxs-lookup"><span data-stu-id="d226c-162">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="d226c-163">Aparecen las asignaciones predeterminadas entre los parámetros de procedimiento almacenado y las propiedades de entidad.</span><span class="sxs-lookup"><span data-stu-id="d226c-163">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="d226c-164">Las operaciones de inserción, actualización y eliminación del tipo de entidad **Person**   ahora están asignadas a procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="d226c-164">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="d226c-165">Si desea habilitar la comprobación de simultaneidad al actualizar o eliminar una entidad con procedimientos almacenados, use una de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="d226c-165">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

- <span data-ttu-id="d226c-166">Use un parámetro de **salida** para devolver el número de filas afectadas desde el procedimiento almacenado y active la casilla del **parámetro filas afectadas**   junto al nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="d226c-166">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="d226c-167">Si el valor devuelto es cero cuando se llama a la operación, se producirá una  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)   .</span><span class="sxs-lookup"><span data-stu-id="d226c-167">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
- <span data-ttu-id="d226c-168">Active la casilla **usar valor original** junto a una propiedad que quiera usar para la comprobación de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="d226c-168">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="d226c-169">Cuando se intenta realizar una actualización, se usará el valor de la propiedad que se leyó originalmente de la base de datos al escribir datos de nuevo en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d226c-169">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="d226c-170">Si el valor no coincide con el valor de la base de datos, se producirá una **OptimisticConcurrencyException**   .</span><span class="sxs-lookup"><span data-stu-id="d226c-170">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="d226c-171">Usar el modelo</span><span class="sxs-lookup"><span data-stu-id="d226c-171">Use the Model</span></span>

<span data-ttu-id="d226c-172">Abra el archivo **Program.CS** en el que se define el método **Main** .</span><span class="sxs-lookup"><span data-stu-id="d226c-172">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="d226c-173">Agregue el código siguiente a la función main.</span><span class="sxs-lookup"><span data-stu-id="d226c-173">Add the following code into the Main function.</span></span>

<span data-ttu-id="d226c-174">El código crea un nuevo objeto **Person** y, a continuación, actualiza el objeto y, por último, elimina el objeto.</span><span class="sxs-lookup"><span data-stu-id="d226c-174">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- <span data-ttu-id="d226c-175">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d226c-175">Compile and run the application.</span></span> <span data-ttu-id="d226c-176">El programa genera el siguiente resultado \*</span><span class="sxs-lookup"><span data-stu-id="d226c-176">The program produces the following output \*</span></span>

> [!NOTE]
> <span data-ttu-id="d226c-177">El servidor genera automáticamente PersonID, por lo que probablemente verá un número diferente \*</span><span class="sxs-lookup"><span data-stu-id="d226c-177">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="d226c-178">Si está trabajando con la versión Ultimate de Visual Studio, puede usar IntelliTrace con el depurador para ver las instrucciones SQL que se ejecutan.</span><span class="sxs-lookup"><span data-stu-id="d226c-178">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Depurar con IntelliTrace](~/ef6/media/intellitrace.png)
