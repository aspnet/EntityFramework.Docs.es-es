---
title: Funciones de Table-Valued (TVF)-EF6
description: Funciones de Table-Valued (TVF) en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 2c699cad3ace253842d17aca4eca4df2318c4795
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066205"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="33938-103">Funciones de Table-Valued (TVF)</span><span class="sxs-lookup"><span data-stu-id="33938-103">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="33938-104">**EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="33938-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="33938-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="33938-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="33938-106">En el tutorial de vídeo y paso a paso se muestra cómo asignar funciones con valores de tabla (TVF) mediante el Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="33938-106">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="33938-107">También se muestra cómo llamar a una TVF desde una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="33938-107">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="33938-108">TVF actualmente solo se admiten en el flujo de trabajo Database First.</span><span class="sxs-lookup"><span data-stu-id="33938-108">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="33938-109">La compatibilidad con TVF se presentó en Entity Framework versión 5.</span><span class="sxs-lookup"><span data-stu-id="33938-109">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="33938-110">Tenga en cuenta que para usar las nuevas características, como las funciones con valores de tabla, las enumeraciones y los tipos espaciales, debe tener como destino .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="33938-110">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="33938-111">Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="33938-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="33938-112">TVF son muy similares a los procedimientos almacenados con una diferencia clave: el resultado de una TVF es ajustable.</span><span class="sxs-lookup"><span data-stu-id="33938-112">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="33938-113">Esto significa que los resultados de una TVF se pueden usar en una consulta LINQ mientras que los resultados de un procedimiento almacenado no pueden.</span><span class="sxs-lookup"><span data-stu-id="33938-113">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="33938-114">Visualización del vídeo</span><span class="sxs-lookup"><span data-stu-id="33938-114">Watch the video</span></span>

<span data-ttu-id="33938-115">**Presentada por**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="33938-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="33938-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  |  [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="33938-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="33938-117">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="33938-117">Pre-Requisites</span></span>

<span data-ttu-id="33938-118">Para completar este tutorial, necesitará:</span><span class="sxs-lookup"><span data-stu-id="33938-118">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="33938-119">Instale la [base de datos School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="33938-119">Install the [School database](xref:ef6/resources/school-database).</span></span>

- <span data-ttu-id="33938-120">Tener una versión reciente de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33938-120">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="33938-121">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="33938-121">Set up the Project</span></span>

1.  <span data-ttu-id="33938-122">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33938-122">Open Visual Studio</span></span>
2.  <span data-ttu-id="33938-123">En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .</span><span class="sxs-lookup"><span data-stu-id="33938-123">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="33938-124">En el panel izquierdo, haga clic en \*\*Visual C \# \*\*y, a continuación, seleccione la plantilla de **consola** .</span><span class="sxs-lookup"><span data-stu-id="33938-124">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="33938-125">Escriba **TVF** como el nombre del proyecto y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="33938-125">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="33938-126">Agregar una función TVF a la base de datos</span><span class="sxs-lookup"><span data-stu-id="33938-126">Add a TVF to the Database</span></span>

-   <span data-ttu-id="33938-127">Seleccionar **vista- &gt; Explorador de objetos de SQL Server**</span><span class="sxs-lookup"><span data-stu-id="33938-127">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="33938-128">Si LocalDB no está en la lista de servidores: haga clic con el botón derecho en **SQL Server** y seleccione **Agregar SQL Server** usar la **autenticación de Windows** predeterminada para conectarse al servidor de LocalDB.</span><span class="sxs-lookup"><span data-stu-id="33938-128">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="33938-129">Expandir el nodo LocalDB</span><span class="sxs-lookup"><span data-stu-id="33938-129">Expand the LocalDB node</span></span>
-   <span data-ttu-id="33938-130">En el nodo bases de datos, haga clic con el botón secundario en el nodo de base de datos School y seleccione **nueva consulta.**</span><span class="sxs-lookup"><span data-stu-id="33938-130">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="33938-131">En el editor de T-SQL, pegue la siguiente definición de TVF</span><span class="sxs-lookup"><span data-stu-id="33938-131">In T-SQL Editor, paste the following TVF definition</span></span>

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   <span data-ttu-id="33938-132">Haga clic con el botón secundario del mouse en el editor de T-SQL y seleccione **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="33938-132">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="33938-133">La función GetStudentGradesForCourse se agrega a la base de datos School</span><span class="sxs-lookup"><span data-stu-id="33938-133">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="33938-134">Creación de un modelo</span><span class="sxs-lookup"><span data-stu-id="33938-134">Create a Model</span></span>

1.  <span data-ttu-id="33938-135">Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento** .</span><span class="sxs-lookup"><span data-stu-id="33938-135">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="33938-136">Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel **plantillas** .</span><span class="sxs-lookup"><span data-stu-id="33938-136">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="33938-137">Escriba **TVFModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="33938-137">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="33938-138">En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente** .</span><span class="sxs-lookup"><span data-stu-id="33938-138">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="33938-139">Haga clic en **nueva conexión** entrar **(LocalDB) \\ mssqllocaldb** en el cuadro de texto nombre de servidor, escriba **School**   como nombre de la base de datos haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="33938-139">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="33938-140">En el cuadro de diálogo elija los objetos de base de datos, en el nodo **tablas**   , seleccione las tablas **Person**, **StudentGrade**y **Course**   .</span><span class="sxs-lookup"><span data-stu-id="33938-140">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="33938-141">Seleccione la función **GetStudentGradesForCourse** que se encuentra en el nodo **procedimientos almacenados y funciones**.   tenga en cuenta que, a partir de Visual Studio 2012, Entity Designer le permite importar por lotes los procedimientos almacenados y las funciones.</span><span class="sxs-lookup"><span data-stu-id="33938-141">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="33938-142">Haga clic en **Finalizar**</span><span class="sxs-lookup"><span data-stu-id="33938-142">Click **Finish**</span></span>
9.  <span data-ttu-id="33938-143">Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.</span><span class="sxs-lookup"><span data-stu-id="33938-143">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="33938-144">Todos los objetos que seleccionó en el cuadro de diálogo **Elija los objetos de base de datos**   se agregan al modelo.</span><span class="sxs-lookup"><span data-stu-id="33938-144">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="33938-145">De forma predeterminada, la forma de resultado de cada procedimiento almacenado importado o función se convertirá automáticamente en un nuevo tipo complejo en el modelo de entidad.</span><span class="sxs-lookup"><span data-stu-id="33938-145">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="33938-146">Pero queremos asignar los resultados de la función GetStudentGradesForCourse a la entidad StudentGrade: haga clic con el botón derecho en la superficie de diseño y seleccione **Explorador de modelos** en el explorador de modelos, seleccione **importaciones de función**y, a continuación, haga doble clic en la función **GetStudentGradesForCourse** en el cuadro de diálogo Editar importación de función, seleccione **entidades**   y elija **StudentGrade**</span><span class="sxs-lookup"><span data-stu-id="33938-146">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="33938-147">Conservar y recuperar datos</span><span class="sxs-lookup"><span data-stu-id="33938-147">Persist and Retrieve Data</span></span>

<span data-ttu-id="33938-148">Abra el archivo donde se define el método Main.</span><span class="sxs-lookup"><span data-stu-id="33938-148">Open the file where the Main method is defined.</span></span> <span data-ttu-id="33938-149">Agregue el código siguiente a la función main.</span><span class="sxs-lookup"><span data-stu-id="33938-149">Add the following code into the Main function.</span></span>

<span data-ttu-id="33938-150">En el código siguiente se muestra cómo crear una consulta que utiliza una función con valores de tabla.</span><span class="sxs-lookup"><span data-stu-id="33938-150">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="33938-151">La consulta proyecta los resultados en un tipo anónimo que contiene el título del curso relacionado y los estudiantes relacionados con un grado mayor o igual que 3,5.</span><span class="sxs-lookup"><span data-stu-id="33938-151">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

<span data-ttu-id="33938-152">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="33938-152">Compile and run the application.</span></span> <span data-ttu-id="33938-153">El programa produce el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="33938-153">The program produces the following output:</span></span>

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="33938-154">Resumen</span><span class="sxs-lookup"><span data-stu-id="33938-154">Summary</span></span>

<span data-ttu-id="33938-155">En este tutorial, hemos visto cómo asignar funciones con valores de tabla (TVF) mediante el Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="33938-155">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="33938-156">También se muestra cómo llamar a una función TVF desde una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="33938-156">It also demonstrated how to call a TVF from a LINQ query.</span></span>
