---
title: Compatibilidad con enum-Code First-EF6
description: 'Compatibilidad con enum: Code First en Entity Framework 6'
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: d12a7427f217eec21b1428dda78d0b7a9e88d07d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065321"
---
# <a name="enum-support---code-first"></a><span data-ttu-id="3bc78-103">Compatibilidad con enum-Code First</span><span class="sxs-lookup"><span data-stu-id="3bc78-103">Enum Support - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="3bc78-104">**EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="3bc78-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="3bc78-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="3bc78-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="3bc78-106">Este tutorial de vídeo y paso a paso muestra cómo utilizar tipos de enumeración con Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="3bc78-106">This video and step-by-step walkthrough shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="3bc78-107">También se muestra cómo usar las enumeraciones en una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="3bc78-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="3bc78-108">En este tutorial se utilizará Code First para crear una nueva base de datos, pero también puede usar [code First para asignarla a una base de datos existente](xref:ef6/modeling/code-first/workflows/existing-database).</span><span class="sxs-lookup"><span data-stu-id="3bc78-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to map to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="3bc78-109">La compatibilidad con la enumeración se presentó en Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="3bc78-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="3bc78-110">Para usar las nuevas características, como las enumeraciones, los tipos de datos espaciales y las funciones con valores de tabla, debe tener como destino .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="3bc78-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="3bc78-111">Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3bc78-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="3bc78-112">En Entity Framework, una enumeración puede tener los siguientes tipos subyacentes: **byte**, **Int16**, **Int32**, **Int64** o **SByte**.</span><span class="sxs-lookup"><span data-stu-id="3bc78-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="3bc78-113">Visualización del vídeo</span><span class="sxs-lookup"><span data-stu-id="3bc78-113">Watch the video</span></span>
<span data-ttu-id="3bc78-114">En este vídeo se muestra cómo utilizar tipos de enumeración con Code First de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3bc78-114">This video shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="3bc78-115">También se muestra cómo usar las enumeraciones en una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="3bc78-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="3bc78-116">**Presentada por**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="3bc78-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="3bc78-117">**Vídeo**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="3bc78-117">**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="3bc78-118">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="3bc78-118">Pre-Requisites</span></span>

<span data-ttu-id="3bc78-119">Deberá tener instalado Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition para completar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="3bc78-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

 

## <a name="set-up-the-project"></a><span data-ttu-id="3bc78-120">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="3bc78-120">Set up the Project</span></span>

1.  <span data-ttu-id="3bc78-121">Abra Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="3bc78-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="3bc78-122">En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .</span><span class="sxs-lookup"><span data-stu-id="3bc78-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="3bc78-123">En el panel izquierdo, haga clic en \*\*Visual C \# \*\*y, a continuación, seleccione la plantilla de **consola** .</span><span class="sxs-lookup"><span data-stu-id="3bc78-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="3bc78-124">Escriba **EnumCodeFirst** como nombre del proyecto y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="3bc78-124">Enter **EnumCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="3bc78-125">Definir un nuevo modelo mediante Code First</span><span class="sxs-lookup"><span data-stu-id="3bc78-125">Define a New Model using Code First</span></span>

<span data-ttu-id="3bc78-126">Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio).</span><span class="sxs-lookup"><span data-stu-id="3bc78-126">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="3bc78-127">El código siguiente define la clase Department.</span><span class="sxs-lookup"><span data-stu-id="3bc78-127">The code below defines the Department class.</span></span>

<span data-ttu-id="3bc78-128">El código también define la enumeración DepartmentNames.</span><span class="sxs-lookup"><span data-stu-id="3bc78-128">The code also defines the DepartmentNames enumeration.</span></span> <span data-ttu-id="3bc78-129">De forma predeterminada, la enumeración es de tipo **int** .</span><span class="sxs-lookup"><span data-stu-id="3bc78-129">By default, the enumeration is of **int** type.</span></span> <span data-ttu-id="3bc78-130">La propiedad Name de la clase Department es del tipo DepartmentNames.</span><span class="sxs-lookup"><span data-stu-id="3bc78-130">The Name property on the Department class is of the DepartmentNames type.</span></span>

<span data-ttu-id="3bc78-131">Abra el archivo Program.cs y pegue las siguientes definiciones de clase.</span><span class="sxs-lookup"><span data-stu-id="3bc78-131">Open the Program.cs file and paste the following class definitions.</span></span>

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="3bc78-132">Definir el tipo derivado de DbContext</span><span class="sxs-lookup"><span data-stu-id="3bc78-132">Define the DbContext Derived Type</span></span>

<span data-ttu-id="3bc78-133">Además de definir entidades, debe definir una clase que derive de DbContext y exponga las propiedades DbSet&lt;TEntity&gt;.</span><span class="sxs-lookup"><span data-stu-id="3bc78-133">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="3bc78-134">Las propiedades DbSet&lt;TEntity&gt; permiten que el contexto sepa qué tipos desea incluir en el modelo.</span><span class="sxs-lookup"><span data-stu-id="3bc78-134">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="3bc78-135">Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3bc78-135">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="3bc78-136">Los tipos DbContext y DbSet se definen en el ensamblado EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="3bc78-136">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="3bc78-137">Se agregará una referencia a este archivo DLL mediante el paquete NuGet EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="3bc78-137">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="3bc78-138">En Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="3bc78-138">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="3bc78-139">Seleccione **administrar paquetes NuGet..** .</span><span class="sxs-lookup"><span data-stu-id="3bc78-139">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="3bc78-140">En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="3bc78-140">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="3bc78-141">Haz clic en **Instalar**</span><span class="sxs-lookup"><span data-stu-id="3bc78-141">Click **Install**</span></span>

<span data-ttu-id="3bc78-142">Tenga en cuenta que, además del ensamblado EntityFramework, también se agregan las referencias a los ensamblados System. ComponentModel. DataAnnotations y System. Data. Entity.</span><span class="sxs-lookup"><span data-stu-id="3bc78-142">Note, that in addition to the EntityFramework  assembly, references to System.ComponentModel.DataAnnotations and System.Data.Entity assemblies are added as well.</span></span>

<span data-ttu-id="3bc78-143">En la parte superior del archivo Program.cs, agregue la siguiente instrucción using:</span><span class="sxs-lookup"><span data-stu-id="3bc78-143">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="3bc78-144">En Program.cs, agregue la definición de contexto.</span><span class="sxs-lookup"><span data-stu-id="3bc78-144">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="3bc78-145">Conservar y recuperar datos</span><span class="sxs-lookup"><span data-stu-id="3bc78-145">Persist and Retrieve Data</span></span>

<span data-ttu-id="3bc78-146">Abra el archivo Program.cs en el que se define el método Main.</span><span class="sxs-lookup"><span data-stu-id="3bc78-146">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="3bc78-147">Agregue el código siguiente a la función main.</span><span class="sxs-lookup"><span data-stu-id="3bc78-147">Add the following code into the Main function.</span></span> <span data-ttu-id="3bc78-148">El código agrega un nuevo objeto Department al contexto.</span><span class="sxs-lookup"><span data-stu-id="3bc78-148">The code adds a new Department object to the context.</span></span> <span data-ttu-id="3bc78-149">Después guarda los datos.</span><span class="sxs-lookup"><span data-stu-id="3bc78-149">It then saves the data.</span></span> <span data-ttu-id="3bc78-150">El código también ejecuta una consulta LINQ que devuelve un departamento en el que el nombre es DepartmentNames. English.</span><span class="sxs-lookup"><span data-stu-id="3bc78-150">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="3bc78-151">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3bc78-151">Compile and run the application.</span></span> <span data-ttu-id="3bc78-152">El programa produce el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="3bc78-152">The program produces the following output:</span></span>

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a><span data-ttu-id="3bc78-153">Ver la base de datos generada</span><span class="sxs-lookup"><span data-stu-id="3bc78-153">View the Generated Database</span></span>

<span data-ttu-id="3bc78-154">Al ejecutar la aplicación por primera vez, el Entity Framework crea una base de datos automáticamente.</span><span class="sxs-lookup"><span data-stu-id="3bc78-154">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="3bc78-155">Dado que tenemos instalado Visual Studio 2012, la base de datos se creará en la instancia de LocalDB.</span><span class="sxs-lookup"><span data-stu-id="3bc78-155">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="3bc78-156">De forma predeterminada, el Entity Framework nombra la base de datos después del nombre completo del contexto derivado (para este ejemplo, que es **EnumCodeFirst. EnumTestContext**).</span><span class="sxs-lookup"><span data-stu-id="3bc78-156">By default, the Entity Framework names the database after the fully qualified name of the derived context (for this example that is **EnumCodeFirst.EnumTestContext**).</span></span> <span data-ttu-id="3bc78-157">Las veces posteriores en las que se utilizará la base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="3bc78-157">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="3bc78-158">Tenga en cuenta que si realiza cambios en el modelo una vez creada la base de datos, debe utilizar Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3bc78-158">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="3bc78-159">Vea [code First en una nueva base de datos](xref:ef6/modeling/code-first/workflows/new-database) para obtener un ejemplo del uso de las migraciones.</span><span class="sxs-lookup"><span data-stu-id="3bc78-159">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="3bc78-160">Para ver la base de datos y los datos, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3bc78-160">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="3bc78-161">En el menú principal de Visual Studio 2012, seleccione **Ver**  - &gt; **Explorador de objetos de SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="3bc78-161">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="3bc78-162">Si LocalDB no está en la lista de servidores, haga clic con el botón secundario del mouse en **SQL Server** y seleccione **Agregar SQL Server** usar la **autenticación de Windows** predeterminada para conectarse a la instancia de LocalDB.</span><span class="sxs-lookup"><span data-stu-id="3bc78-162">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB instance</span></span>
3.  <span data-ttu-id="3bc78-163">Expandir el nodo LocalDB</span><span class="sxs-lookup"><span data-stu-id="3bc78-163">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="3bc78-164">Desabra la carpeta **bases** de datos para ver la nueva base de datos y vaya a la tabla **Department** . tenga en cuenta que Code First no crea una tabla que se asigne al tipo de enumeración</span><span class="sxs-lookup"><span data-stu-id="3bc78-164">Unfold the **Databases** folder to see the new database and browse to the **Department** table Note, that Code First does not create a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="3bc78-165">Para ver los datos, haga clic con el botón derecho en la tabla y seleccione **ver datos** .</span><span class="sxs-lookup"><span data-stu-id="3bc78-165">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="3bc78-166">Resumen</span><span class="sxs-lookup"><span data-stu-id="3bc78-166">Summary</span></span>

<span data-ttu-id="3bc78-167">En este tutorial, hemos visto cómo usar los tipos de enumeración con Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="3bc78-167">In this walkthrough we looked at how to use enum types with Entity Framework Code First.</span></span> 
