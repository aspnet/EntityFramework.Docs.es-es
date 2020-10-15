---
title: Definición de Query-EF Designer-EF6
description: Definir el diseñador de Query-EF en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: 3640156a2ce97dccab563a18d3236aad3dc72ee0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066387"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="40133-103">Definir el diseñador de consultas-EF</span><span class="sxs-lookup"><span data-stu-id="40133-103">Defining Query - EF Designer</span></span>
<span data-ttu-id="40133-104">En este tutorial se muestra cómo agregar una consulta de definición y un tipo de entidad correspondiente a un modelo mediante el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="40133-104">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="40133-105">Una consulta de definición se usa normalmente para proporcionar una funcionalidad similar a la que proporciona una vista de base de datos, pero la vista se define en el modelo, no en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="40133-105">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="40133-106">Una consulta de definición permite ejecutar una instrucción SQL que se especifica en el elemento **DefiningQuery**   de un archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="40133-106">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="40133-107">Para obtener más información, vea **DefiningQuery** en la [especificación de SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="40133-107">For more information, see **DefiningQuery** in the [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="40133-108">Al utilizar las consultas de definición, también debe definir un tipo de entidad en el modelo.</span><span class="sxs-lookup"><span data-stu-id="40133-108">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="40133-109">El tipo de entidad se utiliza para exponer los datos expuestos por la consulta de definición.</span><span class="sxs-lookup"><span data-stu-id="40133-109">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="40133-110">Tenga en cuenta que los datos que se exponen a través de este tipo de entidad son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="40133-110">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="40133-111">Las consultas con parámetros no se pueden ejecutar como consultas de definición.</span><span class="sxs-lookup"><span data-stu-id="40133-111">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="40133-112">Sin embargo, los datos se pueden actualizar asignando las funciones de inserción, actualización y eliminación del tipo de entidad que los muestra a los procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="40133-112">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="40133-113">Para obtener más información, vea [Insert, Update y DELETE con procedimientos almacenados](xref:ef6/modeling/designer/stored-procedures/cud).</span><span class="sxs-lookup"><span data-stu-id="40133-113">For more information, see [Insert, Update, and Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).</span></span>

<span data-ttu-id="40133-114">En este tema se muestra cómo realizar las siguientes tareas.</span><span class="sxs-lookup"><span data-stu-id="40133-114">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="40133-115">Agregar una consulta de definición</span><span class="sxs-lookup"><span data-stu-id="40133-115">Add a Defining Query</span></span>
-   <span data-ttu-id="40133-116">Agregar un tipo de entidad al modelo</span><span class="sxs-lookup"><span data-stu-id="40133-116">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="40133-117">Asignar la consulta de definición al tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="40133-117">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40133-118">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="40133-118">Prerequisites</span></span>

<span data-ttu-id="40133-119">Para completar este tutorial, necesitará:</span><span class="sxs-lookup"><span data-stu-id="40133-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="40133-120">Una versión reciente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="40133-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="40133-121">La [base de datos de ejemplo School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="40133-121">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="40133-122">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="40133-122">Set up the Project</span></span>

<span data-ttu-id="40133-123">En este tutorial se usa Visual Studio 2012 o una versión más reciente.</span><span class="sxs-lookup"><span data-stu-id="40133-123">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="40133-124">Abra Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="40133-124">Open Visual Studio.</span></span>
-   <span data-ttu-id="40133-125">En el menú **Archivo** , seleccione **Nuevo**y haga clic en **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="40133-125">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="40133-126">En el panel izquierdo, haga clic en **Visual \# C**y, a continuación, seleccione la plantilla **aplicación de consola** .</span><span class="sxs-lookup"><span data-stu-id="40133-126">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="40133-127">Escriba **DefiningQuerySample** como nombre del proyecto y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="40133-127">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="40133-128">Creación de un modelo basado en la base de datos School</span><span class="sxs-lookup"><span data-stu-id="40133-128">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="40133-129">Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="40133-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="40133-130">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.</span><span class="sxs-lookup"><span data-stu-id="40133-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="40133-131">Escriba **DefiningQueryModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="40133-131">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="40133-132">En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="40133-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="40133-133">Haga clic en nueva conexión.</span><span class="sxs-lookup"><span data-stu-id="40133-133">Click New Connection.</span></span> <span data-ttu-id="40133-134">En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="40133-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="40133-135">El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="40133-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="40133-136">En el cuadro de diálogo elija los objetos de base de datos, compruebe el nodo **tablas**   .</span><span class="sxs-lookup"><span data-stu-id="40133-136">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="40133-137">Se agregarán todas las tablas al modelo **School** .</span><span class="sxs-lookup"><span data-stu-id="40133-137">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="40133-138">Haga clic en **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="40133-138">Click **Finish**.</span></span>
-   <span data-ttu-id="40133-139">En Explorador de soluciones, haga clic con el botón secundario en el archivo **DefiningQueryModel. edmx** y seleccione **abrir con.**...</span><span class="sxs-lookup"><span data-stu-id="40133-139">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="40133-140">Seleccione **Editor XML (texto)**.</span><span class="sxs-lookup"><span data-stu-id="40133-140">Select **XML (Text) Editor**.</span></span>

    ![Editor XML](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="40133-142">Haga clic en **sí** si se le solicita el mensaje siguiente:</span><span class="sxs-lookup"><span data-stu-id="40133-142">Click **Yes** if prompted with the following message:</span></span>

    ![ADVERTENCIA 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="40133-144">Agregar una consulta de definición</span><span class="sxs-lookup"><span data-stu-id="40133-144">Add a Defining Query</span></span>

<span data-ttu-id="40133-145">En este paso, usaremos el editor XML para agregar una consulta de definición y un tipo de entidad a la sección SSDL del archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="40133-145">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="40133-146">Agregue un elemento **EntitySet**   a la sección SSDL del archivo. edmx (línea 5 a 13).</span><span class="sxs-lookup"><span data-stu-id="40133-146">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="40133-147">Especifique lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="40133-147">Specify the following:</span></span>
    -   <span data-ttu-id="40133-148">Solo **Name**   se especifican los atributos name y **EntityType**   del elemento **EntitySet**   .</span><span class="sxs-lookup"><span data-stu-id="40133-148">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="40133-149">El nombre completo del tipo de entidad se usa en el atributo **EntityType**   .</span><span class="sxs-lookup"><span data-stu-id="40133-149">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="40133-150">La instrucción SQL que se va a ejecutar se especifica en el elemento **DefiningQuery**   .</span><span class="sxs-lookup"><span data-stu-id="40133-150">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   <span data-ttu-id="40133-151">Agregue el elemento **EntityType** a la sección SSDL del archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="40133-151">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="40133-152">como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="40133-152">file as shown below.</span></span> <span data-ttu-id="40133-153">Tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="40133-153">Note the following:</span></span>
    -   <span data-ttu-id="40133-154">El valor del atributo **Name** corresponde al valor del atributo **EntityType** en el elemento **EntitySet** anterior, aunque el nombre completo del tipo de entidad se usa en el atributo **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="40133-154">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="40133-155">Los nombres de propiedad corresponden a los nombres de columna devueltos por la instrucción SQL en el elemento **DefiningQuery** (arriba).</span><span class="sxs-lookup"><span data-stu-id="40133-155">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="40133-156">En este ejemplo, la clave de entidad consta de tres propiedades para garantizar un valor de clave único.</span><span class="sxs-lookup"><span data-stu-id="40133-156">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> <span data-ttu-id="40133-157">Si posteriormente ejecuta el cuadro de diálogo **Asistente para actualizar modelo** , se sobrescribirán los cambios realizados en el modelo de almacenamiento, incluida la definición de las consultas.</span><span class="sxs-lookup"><span data-stu-id="40133-157">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="40133-158">Agregar un tipo de entidad al modelo</span><span class="sxs-lookup"><span data-stu-id="40133-158">Add an Entity Type to the Model</span></span>

<span data-ttu-id="40133-159">En este paso se agregará el tipo de entidad al modelo conceptual mediante el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="40133-159">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="40133-160">Tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="40133-160"> Note the following:</span></span>

-   <span data-ttu-id="40133-161">El **nombre** de la entidad corresponde al valor del atributo **EntityType** en el elemento **EntitySet** anterior.</span><span class="sxs-lookup"><span data-stu-id="40133-161">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="40133-162">Los nombres de propiedad corresponden a los nombres de columna devueltos por la instrucción SQL en el elemento **DefiningQuery** anterior.</span><span class="sxs-lookup"><span data-stu-id="40133-162">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="40133-163">En este ejemplo, la clave de entidad consta de tres propiedades para garantizar un valor de clave único.</span><span class="sxs-lookup"><span data-stu-id="40133-163">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="40133-164">Abra el modelo en el diseñador de EF.</span><span class="sxs-lookup"><span data-stu-id="40133-164">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="40133-165">Haga doble clic en DefiningQueryModel. edmx.</span><span class="sxs-lookup"><span data-stu-id="40133-165">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="40133-166">Por **ejemplo** , en el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="40133-166">Say **Yes** to the following message:</span></span>

    ![ADVERTENCIA 2](~/ef6/media/warning2.png)

 

<span data-ttu-id="40133-168">Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.</span><span class="sxs-lookup"><span data-stu-id="40133-168">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="40133-169">Haga clic con el botón secundario en la superficie del diseñador y seleccione **Agregar nueva** - &gt; **entidad.**...</span><span class="sxs-lookup"><span data-stu-id="40133-169">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="40133-170">Especifique **GradeReport** para el nombre de entidad y el **CourseID** para la **propiedad de clave**.</span><span class="sxs-lookup"><span data-stu-id="40133-170">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="40133-171">Haga clic con el botón secundario en la entidad **GradeReport** y seleccione **Agregar nueva** - &gt; **propiedad escalar**.</span><span class="sxs-lookup"><span data-stu-id="40133-171">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="40133-172">Cambie el nombre predeterminado de la propiedad a **FirstName**.</span><span class="sxs-lookup"><span data-stu-id="40133-172">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="40133-173">Agregue otra propiedad escalar y especifique **LastName** como nombre.</span><span class="sxs-lookup"><span data-stu-id="40133-173">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="40133-174">Agregue otra propiedad escalar y especifique **grade** como el nombre.</span><span class="sxs-lookup"><span data-stu-id="40133-174">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="40133-175">En la ventana **propiedades** , cambie la propiedad **tipo** de **grado**a **decimal**.</span><span class="sxs-lookup"><span data-stu-id="40133-175">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="40133-176">Seleccione las propiedades **FirstName** y **LastName** .</span><span class="sxs-lookup"><span data-stu-id="40133-176">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="40133-177">En la ventana **propiedades** , cambie el valor de la propiedad **EntityKey** a **true**.</span><span class="sxs-lookup"><span data-stu-id="40133-177">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="40133-178">Como resultado, se han agregado los siguientes elementos a la sección **CSDL** del archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="40133-178">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="40133-179">Asignar la consulta de definición al tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="40133-179">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="40133-180">En este paso, usaremos la ventana detalles de la asignación para asignar los tipos de entidad conceptual y de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="40133-180">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="40133-181">Haga clic con el botón secundario en la entidad **GradeReport** en la superficie de diseño y seleccione **asignación de tabla**.</span><span class="sxs-lookup"><span data-stu-id="40133-181">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="40133-182">Se muestra la ventana detalles de la **asignación** .</span><span class="sxs-lookup"><span data-stu-id="40133-182">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="40133-183">Seleccione **GradeReport** en la lista desplegable \*\* &lt; Agregar una &gt; tabla o vista\*\* (que se encuentra en la **tabla**s).</span><span class="sxs-lookup"><span data-stu-id="40133-183">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="40133-184">Aparecen las asignaciones predeterminadas entre el tipo de entidad conceptual y Storage **GradeReport** .</span><span class="sxs-lookup"><span data-stu-id="40133-184">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="40133-185">![Asignación de Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="40133-185">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="40133-186">Como resultado, el elemento **EntitySetMapping**   se agrega a la sección de asignación del archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="40133-186">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   <span data-ttu-id="40133-187">Realice la compilación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="40133-187">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="40133-188">Llamar a la consulta de definición en el código</span><span class="sxs-lookup"><span data-stu-id="40133-188">Call the Defining Query in your Code</span></span>

<span data-ttu-id="40133-189">Ahora puede ejecutar la consulta de definición mediante el tipo de entidad **GradeReport** .</span><span class="sxs-lookup"><span data-stu-id="40133-189">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
