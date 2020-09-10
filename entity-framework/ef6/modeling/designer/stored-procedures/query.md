---
title: 'Procedimientos almacenados de consulta del diseñador: EF6'
description: Procedimientos almacenados de consulta del diseñador en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: c647516e1c6ea010c791df6857782b2304ceed1c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620357"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="8d48c-103">Procedimientos almacenados de consulta del diseñador</span><span class="sxs-lookup"><span data-stu-id="8d48c-103">Designer Query Stored Procedures</span></span>
<span data-ttu-id="8d48c-104">En este tutorial paso a paso se muestra cómo usar el Entity Framework Designer (EF Designer) para importar procedimientos almacenados en un modelo y, a continuación, llamar a los procedimientos almacenados importados para recuperar los resultados.</span><span class="sxs-lookup"><span data-stu-id="8d48c-104">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="8d48c-105">Tenga en cuenta que Code First no admite la asignación a funciones o procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="8d48c-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="8d48c-106">Sin embargo, puede llamar a procedimientos almacenados o funciones mediante el método System. Data. Entity. DbSet. SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="8d48c-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="8d48c-107">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8d48c-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="8d48c-108">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="8d48c-108">Prerequisites</span></span>

<span data-ttu-id="8d48c-109">Para completar este tutorial, necesitará:</span><span class="sxs-lookup"><span data-stu-id="8d48c-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="8d48c-110">Una versión reciente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8d48c-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="8d48c-111">La [base de datos de ejemplo School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="8d48c-111">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="8d48c-112">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="8d48c-112">Set up the Project</span></span>

-   <span data-ttu-id="8d48c-113">Abra Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="8d48c-113">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="8d48c-114">Seleccionar **archivo- &gt; nuevo- &gt; proyecto**</span><span class="sxs-lookup"><span data-stu-id="8d48c-114">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="8d48c-115">En el panel izquierdo, haga clic en \*\*Visual C \# \*\*y, a continuación, seleccione la plantilla de **consola** .</span><span class="sxs-lookup"><span data-stu-id="8d48c-115">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="8d48c-116">Escriba **EFwithSProcsSample**   como nombre.</span><span class="sxs-lookup"><span data-stu-id="8d48c-116">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="8d48c-117">Seleccione  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-117">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="8d48c-118">Creación de un modelo</span><span class="sxs-lookup"><span data-stu-id="8d48c-118">Create a Model</span></span>

-   <span data-ttu-id="8d48c-119">Haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **agregar &gt; nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-119">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="8d48c-120">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.</span><span class="sxs-lookup"><span data-stu-id="8d48c-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="8d48c-121">Escriba **EFwithSProcsModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-121">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="8d48c-122">En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="8d48c-123">Haga clic en **nueva conexión**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-123">Click **New Connection**.</span></span>  
    <span data-ttu-id="8d48c-124">En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="8d48c-125">El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d48c-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="8d48c-126">En el cuadro de diálogo elija los objetos de base de datos, active la casilla **tablas**   para seleccionar todas las tablas.</span><span class="sxs-lookup"><span data-stu-id="8d48c-126">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="8d48c-127">Además, seleccione los siguientes procedimientos almacenados en el nodo **procedimientos almacenados y funciones** : **GetStudentGrades** y **GetDepartmentName**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-127">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![Importar procedimientos almacenados](~/ef6/media/import.jpg)

    <span data-ttu-id="8d48c-129">*A partir de Visual Studio 2012, el diseñador de EF admite la importación masiva de procedimientos almacenados. La **importación de funciones y procedimientos almacenados seleccionados en el modelo de entidad** está activada de forma predeterminada.*</span><span class="sxs-lookup"><span data-stu-id="8d48c-129">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="8d48c-130">Haga clic en **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-130">Click **Finish**.</span></span>

<span data-ttu-id="8d48c-131">De forma predeterminada, la forma de resultado de cada procedimiento almacenado importado o función que devuelve más de una columna se convierte automáticamente en un nuevo tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="8d48c-131">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="8d48c-132">En este ejemplo, queremos asignar los resultados de la función **GetStudentGrades** a la entidad **StudentGrade** y los resultados de **GetDepartmentName** a **None** (**ninguno** es el valor predeterminado).</span><span class="sxs-lookup"><span data-stu-id="8d48c-132">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="8d48c-133">Para que una importación de función devuelva un tipo de entidad, las columnas devueltas por el procedimiento almacenado correspondiente deben coincidir exactamente con las propiedades escalares del tipo de entidad devuelto.</span><span class="sxs-lookup"><span data-stu-id="8d48c-133">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="8d48c-134">Una importación de función también puede devolver colecciones de tipos simples, tipos complejos o ningún valor.</span><span class="sxs-lookup"><span data-stu-id="8d48c-134">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="8d48c-135">Haga clic con el botón secundario en la superficie de diseño y seleccione **Explorador de modelos**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-135">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="8d48c-136">En el **Explorador de modelos**, seleccione **importaciones de función**y, a continuación, haga doble clic en la función **GetStudentGrades** .</span><span class="sxs-lookup"><span data-stu-id="8d48c-136">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="8d48c-137">En el cuadro de diálogo Editar importación de función, seleccione **entidades**   y elija **StudentGrade**.</span><span class="sxs-lookup"><span data-stu-id="8d48c-137">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="8d48c-138">*La casilla **importación de función con composición** en la parte superior del cuadro de diálogo de **importaciones de funciones** le permitirá asignar funciones que admiten composición. Si activa esta casilla, solo aparecerán las funciones que admiten composición (funciones con valores de tabla) en la lista desplegable **procedimiento almacenado o nombre de función** . Si no activa esta casilla, solo se mostrarán en la lista las funciones que no admiten composición.*</span><span class="sxs-lookup"><span data-stu-id="8d48c-138">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="8d48c-139">Usar el modelo</span><span class="sxs-lookup"><span data-stu-id="8d48c-139">Use the Model</span></span>

<span data-ttu-id="8d48c-140">Abra el archivo **Program.CS** en el que se define el método **Main** .</span><span class="sxs-lookup"><span data-stu-id="8d48c-140">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="8d48c-141">Agregue el código siguiente a la función main.</span><span class="sxs-lookup"><span data-stu-id="8d48c-141">Add the following code into the Main function.</span></span>

<span data-ttu-id="8d48c-142">El código llama a dos procedimientos almacenados: **GetStudentGrades** (devuelve **StudentGrades** para el *StudentId*especificado) y **GetDepartmentName** (devuelve el nombre del Departamento en el parámetro de salida).</span><span class="sxs-lookup"><span data-stu-id="8d48c-142">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

<span data-ttu-id="8d48c-143">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d48c-143">Compile and run the application.</span></span> <span data-ttu-id="8d48c-144">El programa produce el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="8d48c-144">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="8d48c-145">Parámetros de salida</span><span class="sxs-lookup"><span data-stu-id="8d48c-145">Output Parameters</span></span>
-----------------

<span data-ttu-id="8d48c-146">Si se utilizan parámetros de salida, sus valores no estarán disponibles hasta que los resultados se hayan leído por completo.</span><span class="sxs-lookup"><span data-stu-id="8d48c-146">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="8d48c-147">Esto se debe al comportamiento subyacente de DbDataReader, consulte [recuperación de datos mediante un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="8d48c-147">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
