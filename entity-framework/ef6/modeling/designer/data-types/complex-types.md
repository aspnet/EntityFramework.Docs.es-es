---
title: 'Tipos complejos: EF Designer: EF6'
description: 'Tipos complejos: EF Designer en Entity Framework 6'
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: abacbdd375c2871e6c4205e0084764029b149a40
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065048"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="beb3f-103">Tipos complejos: EF Designer</span><span class="sxs-lookup"><span data-stu-id="beb3f-103">Complex Types - EF Designer</span></span>
<span data-ttu-id="beb3f-104">En este tema se muestra cómo asignar tipos complejos con el Entity Framework Designer (EF Designer) y cómo consultar entidades que contienen propiedades de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-104">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="beb3f-105">En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="beb3f-105">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="beb3f-107">Al crear el modelo conceptual, es posible que aparezcan advertencias sobre entidades y asociaciones no asignadas en la Lista de errores.</span><span class="sxs-lookup"><span data-stu-id="beb3f-107">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="beb3f-108">Puede omitir estas advertencias porque, después de elegir generar la base de datos a partir del modelo, los errores desaparecerán.</span><span class="sxs-lookup"><span data-stu-id="beb3f-108">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="beb3f-109">Qué es un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-109">What is a Complex Type</span></span>

<span data-ttu-id="beb3f-110">Los tipos complejos son propiedades no escalares de tipos de entidad que permiten organizar las propiedades escalares dentro de las entidades.</span><span class="sxs-lookup"><span data-stu-id="beb3f-110">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="beb3f-111">Al igual que las entidades, los tipos complejos están compuestos de propiedades escalares u otras propiedades de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-111">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="beb3f-112">Al trabajar con objetos que representan tipos complejos, tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb3f-112">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="beb3f-113">Los tipos complejos no tienen claves y, por lo tanto, no pueden existir de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="beb3f-113">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="beb3f-114">Los tipos complejos solo pueden existir como propiedades de tipos de entidad u otros tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="beb3f-114">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="beb3f-115">Los tipos complejos no pueden participar en asociaciones y no pueden contener propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="beb3f-115">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="beb3f-116">Las propiedades de tipo complejo no pueden ser **null**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-116">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="beb3f-117">Se produce una **excepción InvalidOperationException **cuando se llama a **DbContext. SaveChanges**   y se encuentra un objeto complejo null.</span><span class="sxs-lookup"><span data-stu-id="beb3f-117">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="beb3f-118">Las propiedades escalares de objetos complejos pueden ser **null**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-118">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="beb3f-119">Los tipos complejos no pueden heredar de otros tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="beb3f-119">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="beb3f-120">Debe definir el tipo complejo como una **clase**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-120">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="beb3f-121">EF detecta los cambios en los miembros de un objeto de tipo complejo cuando se llama a **DbContext. DetectChanges** .</span><span class="sxs-lookup"><span data-stu-id="beb3f-121">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="beb3f-122">Entity Framework llama automáticamente a **DetectChanges** cuando se llama a los siguientes miembros: **DbSet. Find**, **DbSet. local**, **DbSet. Remove**, **DbSet. Add**, **DbSet. Attach**, **dbcontext. SaveChanges**, **dbcontext. GetValidationErrors**, **dbcontext. entry**, **DbChangeTracker. Entries**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-122">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="beb3f-123">Refactorizar las propiedades de una entidad en un nuevo tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-123">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="beb3f-124">Si ya tiene una entidad en el modelo conceptual, puede que desee refactorizar algunas de las propiedades en una propiedad de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-124">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="beb3f-125">En la superficie del diseñador, seleccione una o varias propiedades (excepto las propiedades de navegación) de una entidad, haga clic con el botón derecho y seleccione **refactorizar- &gt; desplazar al nuevo tipo complejo**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-125">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![Refactorizar en nuevo tipo complejo](~/ef6/media/refactor.png)

<span data-ttu-id="beb3f-127">Un nuevo tipo complejo con las propiedades seleccionadas se agrega al **Explorador de modelos**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-127">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="beb3f-128">Se asigna un nombre predeterminado al tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-128">The complex type is given a default name.</span></span>

<span data-ttu-id="beb3f-129">Una propiedad compleja del tipo que se acaba de crear reemplaza las propiedades seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="beb3f-129">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="beb3f-130">Se conservan todas las asignaciones de propiedades.</span><span class="sxs-lookup"><span data-stu-id="beb3f-130">All property mappings are preserved.</span></span>

![Refactorizar en nuevo tipo complejo 2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="beb3f-132">Crear un nuevo tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-132">Create a New Complex Type</span></span>

<span data-ttu-id="beb3f-133">También puede crear un nuevo tipo complejo que no contenga las propiedades de una entidad existente.</span><span class="sxs-lookup"><span data-stu-id="beb3f-133">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="beb3f-134">Haga clic con el botón secundario en la carpeta **tipos complejos** en el explorador de modelos, seleccione **AgregarNuevo tipo complejo..**..</span><span class="sxs-lookup"><span data-stu-id="beb3f-134">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="beb3f-135">Como alternativa, puede seleccionar la carpeta **tipos complejos** y presionar la tecla **Insertar**del   teclado.</span><span class="sxs-lookup"><span data-stu-id="beb3f-135">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![Agregar nuevo tipo complejo](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="beb3f-137">Un nuevo tipo complejo se agrega a la carpeta con un nombre predeterminado.</span><span class="sxs-lookup"><span data-stu-id="beb3f-137">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="beb3f-138">Ahora puede Agregar propiedades al tipo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-138">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="beb3f-139">Agregar propiedades a un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-139">Add Properties to a Complex Type</span></span>

<span data-ttu-id="beb3f-140">Las propiedades de un tipo complejo pueden ser tipos escalares o los tipos complejos existentes.</span><span class="sxs-lookup"><span data-stu-id="beb3f-140">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="beb3f-141">Sin embargo, las propiedades de tipo complejo no pueden tener las referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="beb3f-141">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="beb3f-142">Por ejemplo, un tipo complejo **OnsiteCourseDetails**   no puede tener una propiedad de tipo complejo **OnsiteCourseDetails**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-142">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="beb3f-143">Puede agregar una propiedad a un tipo complejo de cualquiera de las formas enumeradas a continuación.</span><span class="sxs-lookup"><span data-stu-id="beb3f-143">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="beb3f-144">Haga clic con el botón secundario en un tipo complejo en el explorador de modelos, seleccione **Agregar**, seleccione **propiedad escalar**   o **propiedad compleja**y, a continuación, seleccione el tipo de propiedad que desee.</span><span class="sxs-lookup"><span data-stu-id="beb3f-144">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="beb3f-145">Como alternativa, puede seleccionar un tipo complejo y, a continuación, presionar la tecla **Insertar**del   teclado.</span><span class="sxs-lookup"><span data-stu-id="beb3f-145">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![Agregar propiedades a un tipo complejo](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="beb3f-147">Se agrega una nueva propiedad al tipo complejo con un nombre predeterminado.</span><span class="sxs-lookup"><span data-stu-id="beb3f-147">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="beb3f-148">O BIEN</span><span class="sxs-lookup"><span data-stu-id="beb3f-148">OR -</span></span>

-   <span data-ttu-id="beb3f-149">Haga clic con el botón derecho en una propiedad de entidad en la superficie del **Diseñador de EF** y seleccione **copiar**, a continuación, haga clic con el botón secundario en el tipo complejo en el **Explorador de modelos** y seleccione **pegar**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-149">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="beb3f-150">Cambiar el nombre de un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-150">Rename a Complex Type</span></span>

<span data-ttu-id="beb3f-151">Al cambiar el nombre de un tipo complejo, todas las referencias al tipo se actualizan en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb3f-151">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="beb3f-152">Haga doble clic lentamente en un tipo complejo en el **Explorador de modelos**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-152">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="beb3f-153">El nombre se seleccionará y se abrirá en modo de edición.</span><span class="sxs-lookup"><span data-stu-id="beb3f-153">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="beb3f-154">O BIEN</span><span class="sxs-lookup"><span data-stu-id="beb3f-154">OR -</span></span>

-   <span data-ttu-id="beb3f-155">Haga clic con el botón secundario en un tipo complejo en el **Explorador de modelos** y seleccione **cambiar nombre**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-155">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="beb3f-156">O BIEN</span><span class="sxs-lookup"><span data-stu-id="beb3f-156">OR -</span></span>

-   <span data-ttu-id="beb3f-157">Seleccione un tipo complejo en el Explorador de modelos y presione la tecla F2.</span><span class="sxs-lookup"><span data-stu-id="beb3f-157">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="beb3f-158">O BIEN</span><span class="sxs-lookup"><span data-stu-id="beb3f-158">OR -</span></span>

-   <span data-ttu-id="beb3f-159">Haga clic con el botón secundario en un tipo complejo en el **Explorador de modelos** y seleccione **propiedades**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-159">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="beb3f-160">Edite el nombre en la ventana **propiedades**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-160">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="beb3f-161">Agregar un tipo complejo existente a una entidad y asignar sus propiedades a las columnas de la tabla</span><span class="sxs-lookup"><span data-stu-id="beb3f-161">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="beb3f-162">Haga clic con el botón secundario en una entidad, seleccione **Agregar nuevo**y seleccione **propiedad compleja**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-162">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="beb3f-163">Se agrega a la entidad una propiedad de tipo complejo con un nombre predeterminado.</span><span class="sxs-lookup"><span data-stu-id="beb3f-163">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="beb3f-164">Se asigna un tipo predeterminado (que se elige entre los tipos complejos existentes) a la propiedad.</span><span class="sxs-lookup"><span data-stu-id="beb3f-164">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="beb3f-165">Asigne el tipo deseado a la propiedad en la ventana **propiedades**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-165">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="beb3f-166">Después de agregar una propiedad de tipo complejo a una entidad, debe asignar sus propiedades a las columnas de una tabla.</span><span class="sxs-lookup"><span data-stu-id="beb3f-166">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="beb3f-167">Haga clic con el botón secundario en un tipo de entidad en la superficie de diseño o en el **Explorador de modelos**   y seleccione **asignaciones de tablas**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-167">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="beb3f-168">Las asignaciones de tabla se muestran en la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-168">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="beb3f-169">Expanda el nodo **asigna a &lt; nombre &gt; de tabla**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-169">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="beb3f-170">Aparece un nodo **asignaciones de columnas**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-170">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="beb3f-171">Expanda el nodo **asignaciones de columnas**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-171">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="beb3f-172">Aparece una lista de todas las columnas de la tabla.</span><span class="sxs-lookup"><span data-stu-id="beb3f-172">A list of all the columns in the table appears.</span></span> <span data-ttu-id="beb3f-173">Las propiedades predeterminadas (si existen) a las que se asignan las columnas aparecen bajo el encabezado **valor/propiedad**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-173">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="beb3f-174">Seleccione la columna que desea asignar y, a continuación, haga clic con el botón secundario en el campo de **valor o propiedad**correspondiente   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-174">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="beb3f-175">Se muestra una lista desplegable de todas las propiedades escalares.</span><span class="sxs-lookup"><span data-stu-id="beb3f-175">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="beb3f-176">Seleccione la propiedad apropiada.</span><span class="sxs-lookup"><span data-stu-id="beb3f-176">Select the appropriate property.</span></span>

    ![Asignar tipo complejo](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="beb3f-178">Repita los pasos 6 y 7 para cada columna de tabla.</span><span class="sxs-lookup"><span data-stu-id="beb3f-178">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="beb3f-179">Para eliminar una asignación de columna, seleccione la columna que desea asignar y, a continuación, haga clic en el campo **valor/propiedad**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-179">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="beb3f-180">A continuación, seleccione **eliminar** en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="beb3f-180">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="beb3f-181">Asignación de una importación de función a un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-181">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="beb3f-182">Las importaciones de función se basan en procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="beb3f-182">Function imports are based on stored procedures.</span></span> <span data-ttu-id="beb3f-183">Para asignar una importación de función a un tipo complejo, las columnas devueltas por el procedimiento almacenado correspondiente deben coincidir con el número de propiedades del tipo complejo y deben tener tipos de almacenamiento compatibles con los tipos de propiedad.</span><span class="sxs-lookup"><span data-stu-id="beb3f-183">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="beb3f-184">Haga doble clic en una función importada que desee asignar a un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-184">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![Importaciones de función](~/ef6/media/functionimports.png)

-   <span data-ttu-id="beb3f-186">Rellene los valores para la nueva importación de función de la siguiente forma:</span><span class="sxs-lookup"><span data-stu-id="beb3f-186">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="beb3f-187">Especifique el procedimiento almacenado para el que va a crear una importación de función en el campo **nombre del procedimiento almacenado**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-187">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="beb3f-188">El campo es una lista desplegable que muestra todos los procedimientos almacenados del modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="beb3f-188">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="beb3f-189">Especifique el nombre de la importación de función en el campo Nombre de la **importación de función**   .</span><span class="sxs-lookup"><span data-stu-id="beb3f-189">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="beb3f-190">Seleccione **complejo**   como el tipo de valor devuelto y, a continuación, especifique el tipo de valor devuelto complejo específico eligiendo el tipo adecuado en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="beb3f-190">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![Editar importación de función](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="beb3f-192">Haga clic en  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-192">Click **OK**.</span></span>
    <span data-ttu-id="beb3f-193">Se crea la entrada de importación de función en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="beb3f-193">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="beb3f-194">Personalizar la asignación de columnas para la importación de función</span><span class="sxs-lookup"><span data-stu-id="beb3f-194">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="beb3f-195">Haga clic con el botón derecho en la importación de función en el explorador de modelos y seleccione **función importar asignación**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-195">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="beb3f-196">Aparece la ventana detalles de la **asignación**   y muestra la asignación predeterminada para la importación de función.</span><span class="sxs-lookup"><span data-stu-id="beb3f-196">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="beb3f-197">Las flechas indican las asignaciones entre los valores de columna y de propiedad.</span><span class="sxs-lookup"><span data-stu-id="beb3f-197">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="beb3f-198">De forma predeterminada, se presupone que los nombres de columna son los mismos que los nombres de propiedad del tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-198">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="beb3f-199">Los nombres de columna predeterminados aparecen en texto gris.</span><span class="sxs-lookup"><span data-stu-id="beb3f-199">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="beb3f-200">Si es necesario, cambie los nombres de columna para que coincidan con los nombres de columna devueltos por el procedimiento almacenado que corresponden a la importación de función.</span><span class="sxs-lookup"><span data-stu-id="beb3f-200">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="beb3f-201">Eliminar un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-201">Delete a Complex Type</span></span>

<span data-ttu-id="beb3f-202">Al eliminar un tipo complejo, se elimina el tipo del modelo conceptual, así como las asignaciones para todas las instancias del tipo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-202">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="beb3f-203">Sin embargo, no se actualizan las referencias al tipo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-203">However, references to the type are not updated.</span></span> <span data-ttu-id="beb3f-204">Por ejemplo, si una entidad tiene una propiedad de tipo complejo de tipo ComplexType1 y ComplexType1 se elimina en el **Explorador de modelos**, no se actualiza la propiedad de entidad correspondiente.</span><span class="sxs-lookup"><span data-stu-id="beb3f-204">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="beb3f-205">El modelo no se validará porque contiene una entidad que hace referencia a un tipo complejo eliminado.</span><span class="sxs-lookup"><span data-stu-id="beb3f-205">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="beb3f-206">Puede actualizar o eliminar referencias a los tipos complejos eliminados utilizando Entity Designer.</span><span class="sxs-lookup"><span data-stu-id="beb3f-206">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="beb3f-207">Haga clic con el botón secundario en un tipo complejo en el explorador de modelos y seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="beb3f-207">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="beb3f-208">O BIEN</span><span class="sxs-lookup"><span data-stu-id="beb3f-208">OR -</span></span>

-   <span data-ttu-id="beb3f-209">Seleccione un tipo complejo en el Explorador de modelos y presione la tecla Suprimir del teclado.</span><span class="sxs-lookup"><span data-stu-id="beb3f-209">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="beb3f-210">Consultar las entidades que contienen propiedades de tipo complejo</span><span class="sxs-lookup"><span data-stu-id="beb3f-210">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="beb3f-211">En el código siguiente se muestra cómo ejecutar una consulta que devuelve una colección de objetos de tipo entidad que contienen una propiedad de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="beb3f-211">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
