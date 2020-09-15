---
title: Diseñador espacial-EF-EF6
description: Diseñador espacial-EF en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 6524e3d2be6d5028ab12d6bfd7c65ce3a232a07a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073089"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="f1743-103">Diseñador espacial-EF</span><span class="sxs-lookup"><span data-stu-id="f1743-103">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="f1743-104">**EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="f1743-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="f1743-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="f1743-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="f1743-106">En el tutorial de vídeo y paso a paso se muestra cómo asignar tipos espaciales con el Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="f1743-106">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="f1743-107">También se muestra cómo usar una consulta LINQ para buscar una distancia entre dos ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="f1743-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="f1743-108">En este tutorial se utilizará Model First para crear una nueva base de datos, pero el diseñador de EF también puede usarse con el flujo de trabajo de [Database First](xref:ef6/modeling/designer/workflows/database-first) para asignar a una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="f1743-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="f1743-109">La compatibilidad con tipos espaciales se presentó en Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="f1743-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="f1743-110">Tenga en cuenta que para usar las nuevas características, como el tipo espacial, las enumeraciones y las funciones con valores de tabla, debe tener como destino .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="f1743-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="f1743-111">Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f1743-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="f1743-112">Para usar los tipos de datos espaciales, también debe usar un proveedor de Entity Framework que tenga compatibilidad espacial.</span><span class="sxs-lookup"><span data-stu-id="f1743-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="f1743-113">Consulte [compatibilidad con proveedores para tipos espaciales](xref:ef6/fundamentals/providers/spatial-support) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="f1743-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="f1743-114">Hay dos tipos de datos espaciales principales: Geography y Geometry.</span><span class="sxs-lookup"><span data-stu-id="f1743-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="f1743-115">El tipo de datos Geography almacena los datos de datos elipsoidales (por ejemplo, las coordenadas de latitud y longitud de GPS).</span><span class="sxs-lookup"><span data-stu-id="f1743-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="f1743-116">El tipo de datos Geometry representa el sistema de coordenadas euclidiana (plano).</span><span class="sxs-lookup"><span data-stu-id="f1743-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="f1743-117">Visualización del vídeo</span><span class="sxs-lookup"><span data-stu-id="f1743-117">Watch the video</span></span>
<span data-ttu-id="f1743-118">Este vídeo muestra cómo asignar tipos espaciales con el Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="f1743-118">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="f1743-119">También se muestra cómo usar una consulta LINQ para buscar una distancia entre dos ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="f1743-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="f1743-120">**Presentada por**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="f1743-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="f1743-121">**Vídeo**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="f1743-121">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="f1743-122">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="f1743-122">Pre-Requisites</span></span>

<span data-ttu-id="f1743-123">Deberá tener instalado Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition para completar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="f1743-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="f1743-124">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="f1743-124">Set up the Project</span></span>

1.  <span data-ttu-id="f1743-125">Abra Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="f1743-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="f1743-126">En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .</span><span class="sxs-lookup"><span data-stu-id="f1743-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="f1743-127">En el panel izquierdo, haga clic en \*\*Visual C \# \*\*y, a continuación, seleccione la plantilla de **consola** .</span><span class="sxs-lookup"><span data-stu-id="f1743-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="f1743-128">Escriba **SpatialEFDesigner** como nombre del proyecto y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="f1743-128">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="f1743-129">Crear un nuevo modelo con EF Designer</span><span class="sxs-lookup"><span data-stu-id="f1743-129">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="f1743-130">Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento** .</span><span class="sxs-lookup"><span data-stu-id="f1743-130">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="f1743-131">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.</span><span class="sxs-lookup"><span data-stu-id="f1743-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="f1743-132">Escriba **UniversityModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="f1743-132">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="f1743-133">En la página del Asistente para Entity Data Model, seleccione **modelo vacío** en el cuadro de diálogo elegir contenido del modelo.</span><span class="sxs-lookup"><span data-stu-id="f1743-133">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="f1743-134">Haga clic en **Finish** (Finalizar).</span><span class="sxs-lookup"><span data-stu-id="f1743-134">Click **Finish**</span></span>

<span data-ttu-id="f1743-135">Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.</span><span class="sxs-lookup"><span data-stu-id="f1743-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="f1743-136">El asistente realiza las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="f1743-136">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="f1743-137">Genera el archivo EnumTestModel. edmx que define el modelo conceptual, el modelo de almacenamiento y la asignación entre ellos.</span><span class="sxs-lookup"><span data-stu-id="f1743-137">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="f1743-138">Establece la propiedad de procesamiento de artefactos de metadatos del archivo. edmx que se va a incrustar en el ensamblado de salida para incrustar los archivos de metadatos generados en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="f1743-138">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="f1743-139">Agrega una referencia a los siguientes ensamblados: EntityFramework, System. ComponentModel. DataAnnotations y System. Data. Entity.</span><span class="sxs-lookup"><span data-stu-id="f1743-139">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="f1743-140">Crea archivos UniversityModel.tt y UniversityModel.Context.tt y los agrega en el archivo. edmx.</span><span class="sxs-lookup"><span data-stu-id="f1743-140">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="f1743-141">Estos archivos de plantilla T4 generan el código que define el tipo derivado de DbContext y los tipos POCO que se asignan a las entidades en el modelo. edmx.</span><span class="sxs-lookup"><span data-stu-id="f1743-141">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="f1743-142">Agregar un nuevo tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="f1743-142">Add a New Entity Type</span></span>

1.  <span data-ttu-id="f1743-143">Haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **agregar- &gt; entidad**, aparecerá el cuadro de diálogo nueva entidad.</span><span class="sxs-lookup"><span data-stu-id="f1743-143">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="f1743-144">Especifique **Universidad** como nombre de tipo y especifique **UniversityID** para el nombre de la propiedad de clave y deje el tipo como **Int32** .</span><span class="sxs-lookup"><span data-stu-id="f1743-144">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="f1743-145">Haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="f1743-145">Click **OK**</span></span>
4.  <span data-ttu-id="f1743-146">Haga clic con el botón derecho en la entidad y seleccione **Agregar nueva &gt; propiedad-escalar** .</span><span class="sxs-lookup"><span data-stu-id="f1743-146">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="f1743-147">Cambiar el nombre de la nueva propiedad a **nombre**</span><span class="sxs-lookup"><span data-stu-id="f1743-147">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="f1743-148">Agregue otra propiedad escalar y cámbiela a **Ubicación** . abra el ventana Propiedades y cambie el tipo de la nueva propiedad a **geografía** .</span><span class="sxs-lookup"><span data-stu-id="f1743-148">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="f1743-149">Guardar el modelo y compilar el proyecto</span><span class="sxs-lookup"><span data-stu-id="f1743-149">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="f1743-150">Al compilar, las advertencias sobre entidades y asociaciones no asignadas pueden aparecer en el Lista de errores.</span><span class="sxs-lookup"><span data-stu-id="f1743-150">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="f1743-151">Puede omitir estas advertencias porque, después de elegir generar la base de datos a partir del modelo, los errores desaparecerán.</span><span class="sxs-lookup"><span data-stu-id="f1743-151">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="f1743-152">Generar base de datos a partir del modelo</span><span class="sxs-lookup"><span data-stu-id="f1743-152">Generate Database from Model</span></span>

<span data-ttu-id="f1743-153">Ahora se puede generar una base de datos basada en el modelo.</span><span class="sxs-lookup"><span data-stu-id="f1743-153">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="f1743-154">Haga clic con el botón secundario en un espacio vacío en la superficie de Entity Designer y seleccione **generar base de datos a partir del modelo** .</span><span class="sxs-lookup"><span data-stu-id="f1743-154">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="f1743-155">Se muestra el cuadro de diálogo elegir la conexión de datos del Asistente para generar base de datos, haga clic en el botón **nueva conexión** especifique **(LocalDB) \\ mssqllocaldb** para el nombre del servidor y la **Universidad** para la base de datos y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="f1743-155">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="f1743-156">Aparecerá un cuadro de diálogo en el que se le pregunta si desea crear una nueva base de datos, haga clic en **sí**.</span><span class="sxs-lookup"><span data-stu-id="f1743-156">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="f1743-157">Haga clic en **siguiente** y el Asistente para crear bases de datos genera el lenguaje de definición de datos (DDL) para crear una base de datos. la DDL generada se muestra en el cuadro de diálogo Resumen y configuración. tenga en cuenta que el DDL no contiene una definición para una tabla que se asigna al tipo de enumeración</span><span class="sxs-lookup"><span data-stu-id="f1743-157">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="f1743-158">Haga clic en **Finalizar** al hacer clic en finalizar no se ejecuta el script DDL.</span><span class="sxs-lookup"><span data-stu-id="f1743-158">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="f1743-159">El Asistente para crear bases de datos hace lo siguiente: abre **UniversityModel. edmx. SQL** en el editor de T-SQL genera el esquema de almacenamiento y las secciones de asignación del archivo edmx agrega información de la cadena de conexión al archivo App.config</span><span class="sxs-lookup"><span data-stu-id="f1743-159">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="f1743-160">Haga clic con el botón secundario del mouse en el editor de T-SQL y seleccione **Ejecutar** el cuadro de diálogo conectar con el servidor, escriba la información de conexión del paso 2 y haga clic en **conectar** .</span><span class="sxs-lookup"><span data-stu-id="f1743-160">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="f1743-161">Para ver el esquema generado, haga clic con el botón derecho en el nombre de la base de datos en Explorador de objetos de SQL Server y seleccione **Actualizar** .</span><span class="sxs-lookup"><span data-stu-id="f1743-161">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="f1743-162">Conservar y recuperar datos</span><span class="sxs-lookup"><span data-stu-id="f1743-162">Persist and Retrieve Data</span></span>

<span data-ttu-id="f1743-163">Abra el archivo Program.cs en el que se define el método Main.</span><span class="sxs-lookup"><span data-stu-id="f1743-163">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="f1743-164">Agregue el código siguiente a la función main.</span><span class="sxs-lookup"><span data-stu-id="f1743-164">Add the following code into the Main function.</span></span>

<span data-ttu-id="f1743-165">El código agrega dos nuevos objetos universitarios al contexto.</span><span class="sxs-lookup"><span data-stu-id="f1743-165">The code adds two new University objects to the context.</span></span> <span data-ttu-id="f1743-166">Las propiedades espaciales se inicializan mediante el método DbGeography. FromText.</span><span class="sxs-lookup"><span data-stu-id="f1743-166">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="f1743-167">El punto de geografía representado como WellKnownText se pasa al método.</span><span class="sxs-lookup"><span data-stu-id="f1743-167">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="f1743-168">Después, el código guarda los datos.</span><span class="sxs-lookup"><span data-stu-id="f1743-168">The code then saves the data.</span></span> <span data-ttu-id="f1743-169">A continuación, se crea y se ejecuta la consulta LINQ que devuelve un objeto University donde su ubicación es más cercana a la ubicación especificada.</span><span class="sxs-lookup"><span data-stu-id="f1743-169">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

<span data-ttu-id="f1743-170">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f1743-170">Compile and run the application.</span></span> <span data-ttu-id="f1743-171">El programa produce el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="f1743-171">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="f1743-172">Para ver los datos de la base de datos, haga clic con el botón derecho en el nombre de la base de datos en Explorador de objetos de SQL Server y seleccione **Actualizar**.</span><span class="sxs-lookup"><span data-stu-id="f1743-172">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="f1743-173">A continuación, haga clic con el botón secundario del mouse en la tabla y seleccione **ver datos**.</span><span class="sxs-lookup"><span data-stu-id="f1743-173">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="f1743-174">Resumen</span><span class="sxs-lookup"><span data-stu-id="f1743-174">Summary</span></span>

<span data-ttu-id="f1743-175">En este tutorial, hemos visto cómo asignar tipos espaciales mediante el Entity Framework Designer y cómo usar tipos espaciales en el código.</span><span class="sxs-lookup"><span data-stu-id="f1743-175">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
