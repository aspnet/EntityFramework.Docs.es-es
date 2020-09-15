---
title: 'Relaciones: EF Designer: EF6'
description: 'Relaciones: EF Designer en Entity Framework 6'
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: 65960dcfcafc239b5ecd8b396a52dababbb4ab08
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073297"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="a0933-103">Relaciones: EF Designer</span><span class="sxs-lookup"><span data-stu-id="a0933-103">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="a0933-104">En esta página se proporciona información sobre cómo configurar las relaciones en el modelo mediante el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="a0933-104">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="a0933-105">Para obtener información general sobre las relaciones en EF y cómo obtener acceso a los datos y manipularlos mediante relaciones, vea [relaciones & propiedades de navegación](xref:ef6/fundamentals/relationships).</span><span class="sxs-lookup"><span data-stu-id="a0933-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>

<span data-ttu-id="a0933-106">Las asociaciones definen las relaciones entre los tipos de entidad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="a0933-106">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="a0933-107">En este tema se muestra cómo asignar asociaciones con el Entity Framework Designer (EF Designer).</span><span class="sxs-lookup"><span data-stu-id="a0933-107">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="a0933-108">En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="a0933-108">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="a0933-110">Al crear el modelo conceptual, es posible que aparezcan advertencias sobre entidades y asociaciones no asignadas en la Lista de errores.</span><span class="sxs-lookup"><span data-stu-id="a0933-110">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="a0933-111">Puede omitir estas advertencias porque, después de elegir generar la base de datos a partir del modelo, los errores desaparecerán.</span><span class="sxs-lookup"><span data-stu-id="a0933-111">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="a0933-112">Información general sobre asociaciones</span><span class="sxs-lookup"><span data-stu-id="a0933-112">Associations Overview</span></span>

<span data-ttu-id="a0933-113">Al diseñar el modelo mediante el diseñador de EF, un archivo. edmx representa el modelo.</span><span class="sxs-lookup"><span data-stu-id="a0933-113">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="a0933-114">En el archivo. edmx, un elemento **Association** define una relación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="a0933-114">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="a0933-115">Una asociación debe especificar los tipos de entidad que están implicados en la relación y el posible número de tipos de entidad en cada extremo de la relación, que se conoce como multiplicidad.</span><span class="sxs-lookup"><span data-stu-id="a0933-115">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="a0933-116">La multiplicidad de un extremo de asociación puede tener un valor de uno (1), cero o uno (0.. 1) o varios ( \* ).</span><span class="sxs-lookup"><span data-stu-id="a0933-116">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="a0933-117">Esta información se especifica en dos elementos **End** secundarios.</span><span class="sxs-lookup"><span data-stu-id="a0933-117">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="a0933-118">En tiempo de ejecución, se puede tener acceso a las instancias de tipo de entidad en un extremo de una asociación a través de las propiedades de navegación o las claves externas (si elige exponer las claves externas en las entidades).</span><span class="sxs-lookup"><span data-stu-id="a0933-118">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="a0933-119">Con las claves externas expuestas, la relación entre las entidades se administra con un elemento **ReferentialConstraint** (un elemento secundario del elemento **Association** ).</span><span class="sxs-lookup"><span data-stu-id="a0933-119">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="a0933-120">Se recomienda exponer siempre las claves externas para las relaciones de las entidades.</span><span class="sxs-lookup"><span data-stu-id="a0933-120">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="a0933-121">En varios a varios ( \* : \* ) no se pueden agregar claves externas a las entidades.</span><span class="sxs-lookup"><span data-stu-id="a0933-121">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="a0933-122">En una \* relación de: \* , la información de asociación se administra con un objeto independiente.</span><span class="sxs-lookup"><span data-stu-id="a0933-122">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="a0933-123">Para obtener información sobre los elementos CSDL (**ReferentialConstraint**, **Association**, etc.), vea la [especificación de CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="a0933-123">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="a0933-124">Crear y eliminar asociaciones</span><span class="sxs-lookup"><span data-stu-id="a0933-124">Create and Delete Associations</span></span>

<span data-ttu-id="a0933-125">La creación de una asociación con el diseñador de EF actualiza el contenido del modelo del archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="a0933-125">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="a0933-126">Después de crear una asociación, debe crear las asignaciones para la Asociación (que se describen más adelante en este tema).</span><span class="sxs-lookup"><span data-stu-id="a0933-126">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="a0933-127">En esta sección se supone que ya ha agregado las entidades con las que desea crear una asociación entre el modelo.</span><span class="sxs-lookup"><span data-stu-id="a0933-127">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="a0933-128">Para crear una asociación</span><span class="sxs-lookup"><span data-stu-id="a0933-128">To create an association</span></span>

1.  <span data-ttu-id="a0933-129">Haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **Agregar nuevo**y seleccione **asociación..**..</span><span class="sxs-lookup"><span data-stu-id="a0933-129">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="a0933-130">Rellene la configuración de la asociación en el cuadro de diálogo **Agregar Asociación** .</span><span class="sxs-lookup"><span data-stu-id="a0933-130">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![Agregar Asociación](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="a0933-132">Puede optar por no agregar propiedades de navegación ni propiedades de clave externa a las entidades en los extremos de la asociación si desactiva las casillas **propiedad de navegación **y **Agregar propiedades de clave externa a la &lt; &gt; entidad nombre de tipo de entidad **.</span><span class="sxs-lookup"><span data-stu-id="a0933-132">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="a0933-133">Si agrega solo una propiedad de navegación, la asociación se podrá recorrer en una única dirección.</span><span class="sxs-lookup"><span data-stu-id="a0933-133">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="a0933-134">Si no agrega ninguna propiedad de navegación, deberá agregar propiedades de clave externa para poder tener acceso a las entidades situadas en los extremos de la asociación.</span><span class="sxs-lookup"><span data-stu-id="a0933-134">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="a0933-135">Haga clic en  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="a0933-135">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="a0933-136">Para eliminar una asociación</span><span class="sxs-lookup"><span data-stu-id="a0933-136">To delete an association</span></span>

<span data-ttu-id="a0933-137">Para eliminar una asociación, realice una de las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="a0933-137">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="a0933-138">Haga clic con el botón derecho en la asociación en la superficie del diseñador EF y seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="a0933-138">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="a0933-139">O BIEN</span><span class="sxs-lookup"><span data-stu-id="a0933-139">OR -</span></span>

-   <span data-ttu-id="a0933-140">Seleccione una o más asociaciones y presione la tecla Supr.</span><span class="sxs-lookup"><span data-stu-id="a0933-140">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="a0933-141">Incluir propiedades de clave externa en las entidades (restricciones referenciales)</span><span class="sxs-lookup"><span data-stu-id="a0933-141">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="a0933-142">Se recomienda exponer siempre las claves externas para las relaciones de las entidades.</span><span class="sxs-lookup"><span data-stu-id="a0933-142">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="a0933-143">Entity Framework usa una restricción referencial para identificar que una propiedad actúa como clave externa de una relación.</span><span class="sxs-lookup"><span data-stu-id="a0933-143">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="a0933-144">Si ha activado la casilla de verificación \*\*\*Agregar propiedades de clave externa al &lt; nombre de tipo de entidad &gt; \*\*\* al crear una relación, se le ha agregado esta restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="a0933-144">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="a0933-145">Cuando se usa EF Designer para agregar o editar una restricción referencial, el diseñador EF agrega o modifica un elemento **ReferentialConstraint**   en el contenido CSDL del archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="a0933-145">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="a0933-146">Haga doble clic en la asociación que desee editar.</span><span class="sxs-lookup"><span data-stu-id="a0933-146">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="a0933-147">Aparecerá el cuadro de diálogo **restricción referencial**   .</span><span class="sxs-lookup"><span data-stu-id="a0933-147">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="a0933-148">En la **Principal**   lista desplegable principal, seleccione la entidad de entidad de seguridad en la restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="a0933-148">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="a0933-149">Las propiedades de clave de la entidad se agregan a la lista de **claves principales**   en el cuadro de diálogo.</span><span class="sxs-lookup"><span data-stu-id="a0933-149">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="a0933-150">En la **Dependent**   lista desplegable dependiente, seleccione la entidad dependiente en la restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="a0933-150">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="a0933-151">Para cada clave principal que tenga una clave dependiente, seleccione una clave dependiente correspondiente en las listas desplegables de la columna de **clave dependiente**   .</span><span class="sxs-lookup"><span data-stu-id="a0933-151">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Restricción de referencia](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="a0933-153">Haga clic en  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="a0933-153">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="a0933-154">Crear y editar asignaciones de asociación</span><span class="sxs-lookup"><span data-stu-id="a0933-154">Create and Edit Association Mappings</span></span>

<span data-ttu-id="a0933-155">Puede especificar cómo se asigna una asociación a la base de datos en la ventana detalles de la **asignación**   del diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="a0933-155">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="a0933-156">Solo puede asignar los detalles de las asociaciones que no tienen una restricción referencial especificada.</span><span class="sxs-lookup"><span data-stu-id="a0933-156">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="a0933-157">Si se especifica una restricción referencial, se incluye una propiedad de clave externa en la entidad y se pueden usar los detalles de asignación de la entidad para controlar a qué columna se asigna la clave externa.</span><span class="sxs-lookup"><span data-stu-id="a0933-157">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="a0933-158">Crear una asignación de asociación</span><span class="sxs-lookup"><span data-stu-id="a0933-158">Create an association mapping</span></span>

-   <span data-ttu-id="a0933-159">Haga clic con el botón secundario en una asociación en la superficie de diseño y seleccione **asignación de tabla**.</span><span class="sxs-lookup"><span data-stu-id="a0933-159">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="a0933-160">Esto muestra la asignación de asociación en la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="a0933-160">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="a0933-161">Haga clic en **Agregar una tabla o vista**.</span><span class="sxs-lookup"><span data-stu-id="a0933-161">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="a0933-162">Aparece una lista desplegable que incluye todas las tablas del modelo de almacenamiento. </span><span class="sxs-lookup"><span data-stu-id="a0933-162">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="a0933-163">Seleccione la tabla a la que se asignará la asociación. </span><span class="sxs-lookup"><span data-stu-id="a0933-163">Select the table to which the association will map.</span></span>
    <span data-ttu-id="a0933-164">La ventana detalles de la **asignación**   muestra los extremos de la asociación y las propiedades clave del tipo de entidad en cada **extremo**.</span><span class="sxs-lookup"><span data-stu-id="a0933-164">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="a0933-165">Para cada propiedad de clave, haga clic en el campo **columna**   y seleccione la columna a la que se asignará la propiedad.</span><span class="sxs-lookup"><span data-stu-id="a0933-165">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![Detalles de la asignación 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="a0933-167">Editar una asignación de asociación</span><span class="sxs-lookup"><span data-stu-id="a0933-167">Edit an association mapping</span></span>

-   <span data-ttu-id="a0933-168">Haga clic con el botón secundario en una asociación en la superficie de diseño y seleccione **asignación de tabla**.</span><span class="sxs-lookup"><span data-stu-id="a0933-168">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="a0933-169">Esto muestra la asignación de asociación en la ventana detalles de la **asignación**   .</span><span class="sxs-lookup"><span data-stu-id="a0933-169">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="a0933-170">Haga clic en \*\*maps to &lt; TABLE name &gt; \*\*.</span><span class="sxs-lookup"><span data-stu-id="a0933-170">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="a0933-171">Aparece una lista desplegable que incluye todas las tablas del modelo de almacenamiento. </span><span class="sxs-lookup"><span data-stu-id="a0933-171">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="a0933-172">Seleccione la tabla a la que se asignará la asociación. </span><span class="sxs-lookup"><span data-stu-id="a0933-172">Select the table to which the association will map.</span></span>
    <span data-ttu-id="a0933-173">La ventana detalles de la **asignación**   muestra los extremos de la asociación y las propiedades clave del tipo de entidad en cada extremo.</span><span class="sxs-lookup"><span data-stu-id="a0933-173">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="a0933-174">Para cada propiedad de clave, haga clic en el campo **columna**   y seleccione la columna a la que se asignará la propiedad.</span><span class="sxs-lookup"><span data-stu-id="a0933-174">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="a0933-175">Editar y eliminar propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="a0933-175">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="a0933-176">Las propiedades de navegación son propiedades de acceso directo que se usan para buscar las entidades en los extremos de una asociación en un modelo.</span><span class="sxs-lookup"><span data-stu-id="a0933-176">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="a0933-177">Las propiedades de navegación se pueden crear al definir una asociación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="a0933-177">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="a0933-178">Para editar las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="a0933-178">To edit navigation properties</span></span>

-   <span data-ttu-id="a0933-179">Seleccione una propiedad de navegación en la superficie del diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="a0933-179">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="a0933-180">En la ventana **propiedades**de Visual Studio se muestra información sobre la propiedad de navegación   .</span><span class="sxs-lookup"><span data-stu-id="a0933-180">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="a0933-181">Cambie la configuración de las propiedades en la ventana **propiedades**   .</span><span class="sxs-lookup"><span data-stu-id="a0933-181">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="a0933-182">Para eliminar propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="a0933-182">To delete navigation properties</span></span>

-   <span data-ttu-id="a0933-183">Si las claves externas no se exponen en los tipos de entidad del modelo conceptual, la eliminación de una propiedad de navegación puede provocar que la asociación correspondiente solo se pueda recorrer en una dirección o incluso en ninguna.</span><span class="sxs-lookup"><span data-stu-id="a0933-183">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="a0933-184">Haga clic con el botón derecho en una propiedad de navegación en la superficie del diseñador EF y seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="a0933-184">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
