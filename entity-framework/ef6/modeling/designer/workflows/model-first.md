---
title: Model First EF6
description: Model First en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: 13c5e173182ad0781a674ce3d10f7374c4443bf9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073167"
---
# <a name="model-first"></a><span data-ttu-id="9068a-103">Model First</span><span class="sxs-lookup"><span data-stu-id="9068a-103">Model First</span></span>
<span data-ttu-id="9068a-104">Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Model First mediante Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9068a-104">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="9068a-105">Model First permite crear un nuevo modelo mediante el Entity Framework Designer y, a continuación, generar un esquema de base de datos a partir del modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-105">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="9068a-106">El modelo se almacena en un archivo EDMX (extensión. EDMX) y se puede ver y editar en el Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="9068a-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="9068a-107">Las clases con las que interactúa en la aplicación se generan automáticamente a partir del archivo EDMX.</span><span class="sxs-lookup"><span data-stu-id="9068a-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="9068a-108">Visualización del vídeo</span><span class="sxs-lookup"><span data-stu-id="9068a-108">Watch the video</span></span>
<span data-ttu-id="9068a-109">Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Model First mediante Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9068a-109">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="9068a-110">Model First permite crear un nuevo modelo mediante el Entity Framework Designer y, a continuación, generar un esquema de base de datos a partir del modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-110">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="9068a-111">El modelo se almacena en un archivo EDMX (extensión. EDMX) y se puede ver y editar en el Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="9068a-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="9068a-112">Las clases con las que interactúa en la aplicación se generan automáticamente a partir del archivo EDMX.</span><span class="sxs-lookup"><span data-stu-id="9068a-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="9068a-113">**Presentado por**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="9068a-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="9068a-114">**Vídeo**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="9068a-114">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="9068a-115">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="9068a-115">Pre-Requisites</span></span>

<span data-ttu-id="9068a-116">Para completar este tutorial, deberá tener instalados Visual Studio 2010 o Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="9068a-116">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="9068a-117">Si usa Visual Studio 2010, también debe tener instalado [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) .</span><span class="sxs-lookup"><span data-stu-id="9068a-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="9068a-118">1. crear la aplicación</span><span class="sxs-lookup"><span data-stu-id="9068a-118">1. Create the Application</span></span>

<span data-ttu-id="9068a-119">Para simplificar las cosas, vamos a crear una aplicación de consola básica que use el Model First para realizar el acceso a los datos:</span><span class="sxs-lookup"><span data-stu-id="9068a-119">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="9068a-120">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9068a-120">Open Visual Studio</span></span>
-   <span data-ttu-id="9068a-121">**Archivo- &gt; nuevo- &gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="9068a-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="9068a-122">Seleccionar **ventanas** en el menú izquierdo y en la **aplicación de consola**</span><span class="sxs-lookup"><span data-stu-id="9068a-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="9068a-123">Escriba **ModelFirstSample** como nombre</span><span class="sxs-lookup"><span data-stu-id="9068a-123">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="9068a-124">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="9068a-124">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="9068a-125">2. crear modelo</span><span class="sxs-lookup"><span data-stu-id="9068a-125">2. Create Model</span></span>

<span data-ttu-id="9068a-126">Vamos a hacer uso de Entity Framework Designer, que se incluye como parte de Visual Studio, para crear nuestro modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-126">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="9068a-127">**Proyecto- &gt; Agregar nuevo elemento...**</span><span class="sxs-lookup"><span data-stu-id="9068a-127">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="9068a-128">Seleccione **datos** en el menú de la izquierda y, a continuación, **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="9068a-128">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="9068a-129">Escriba **BloggingModel** como nombre y haga clic en **Aceptar**. Esto iniciará el Asistente para Entity Data Model</span><span class="sxs-lookup"><span data-stu-id="9068a-129">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="9068a-130">Seleccione **modelo vacío** y haga clic en **Finalizar** .</span><span class="sxs-lookup"><span data-stu-id="9068a-130">Select **Empty Model** and click **Finish**</span></span>

    ![Crear modelo vacío](~/ef6/media/createemptymodel.png)

<span data-ttu-id="9068a-132">El Entity Framework Designer se abre con un modelo en blanco.</span><span class="sxs-lookup"><span data-stu-id="9068a-132">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="9068a-133">Ahora podemos empezar a agregar entidades, propiedades y asociaciones al modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-133">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="9068a-134">Haga clic con el botón derecho en la superficie de diseño y seleccione **propiedades** .</span><span class="sxs-lookup"><span data-stu-id="9068a-134">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="9068a-135">En el ventana Propiedades cambiar el **nombre del contenedor de entidades** a **BloggingContext** 
     *este es el nombre del contexto derivado que se generará automáticamente, el contexto representa una sesión con la base de datos, lo que nos permite consultar y guardar datos* .</span><span class="sxs-lookup"><span data-stu-id="9068a-135">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="9068a-136">Haga clic con el botón derecho en la superficie de diseño y seleccione **Agregar nueva &gt; entidad..** .</span><span class="sxs-lookup"><span data-stu-id="9068a-136">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="9068a-137">Escriba **blog** como el nombre de la entidad y **BlogId** como nombre de la clave y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="9068a-137">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![Agregar entidad de blog](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="9068a-139">Haga clic con el botón derecho en la nueva entidad en la superficie de diseño y seleccione **Agregar nueva &gt; propiedad-escalar** **, escriba el nombre de** la propiedad.</span><span class="sxs-lookup"><span data-stu-id="9068a-139">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="9068a-140">Repita este proceso para agregar una propiedad de **dirección URL** .</span><span class="sxs-lookup"><span data-stu-id="9068a-140">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="9068a-141">Haga clic con el botón secundario en la propiedad **dirección URL** en la superficie de diseño y seleccione **propiedades**, en el ventana Propiedades cambie la configuración que **acepta valores NULL** a **true** 
     *esto nos permite guardar un blog en la base de datos sin asignarle una dirección URL* .</span><span class="sxs-lookup"><span data-stu-id="9068a-141">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="9068a-142">Con las técnicas que acaba de aprender, agregue una entidad **post** con una propiedad de clave **PostId**</span><span class="sxs-lookup"><span data-stu-id="9068a-142">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="9068a-143">Agregar propiedades escalares de **título** y de **contenido** a la entidad **post**</span><span class="sxs-lookup"><span data-stu-id="9068a-143">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="9068a-144">Ahora que tenemos un par de entidades, es el momento de agregar una asociación (o relación) entre ellas.</span><span class="sxs-lookup"><span data-stu-id="9068a-144">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="9068a-145">Haga clic con el botón derecho en la superficie de diseño y seleccione **Agregar nueva- &gt; asociación..** .</span><span class="sxs-lookup"><span data-stu-id="9068a-145">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="9068a-146">Haga que un extremo del punto de relación se envíe a un **blog** con una multiplicidad de **uno** y el otro extremo para **publicar** con una multiplicidad de **muchos** 
     *significa que un blog tiene muchas publicaciones y una publicación pertenece a un blog* .</span><span class="sxs-lookup"><span data-stu-id="9068a-146">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="9068a-147">Asegúrese de que el cuadro de la **entidad agregar propiedades de clave externa al "post"** está activado y haga clic en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="9068a-147">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![Agregar Asociación MF](~/ef6/media/addassociationmf.png)

<span data-ttu-id="9068a-149">Ahora tenemos un modelo simple en el que se puede generar una base de datos y se usa para leer y escribir datos.</span><span class="sxs-lookup"><span data-stu-id="9068a-149">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![Modelo inicial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="9068a-151">Pasos adicionales en Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="9068a-151">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="9068a-152">Si está trabajando en Visual Studio 2010, hay algunos pasos adicionales que debe seguir para actualizar a la versión más reciente de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9068a-152">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="9068a-153">La actualización es importante porque le proporciona acceso a una superficie de API mejorada, que es mucho más fácil de usar, así como las correcciones de errores más recientes.</span><span class="sxs-lookup"><span data-stu-id="9068a-153">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="9068a-154">En primer lugar, necesitamos obtener la versión más reciente de Entity Framework de NuGet.</span><span class="sxs-lookup"><span data-stu-id="9068a-154">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="9068a-155">**Proyecto: &gt; Administrar paquetes de NuGet...** 
     *Si no tiene la opción **administrar paquetes Nuget...** , debe instalar la [versión más reciente de Nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) .*</span><span class="sxs-lookup"><span data-stu-id="9068a-155">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="9068a-156">Seleccione la pestaña **en línea**</span><span class="sxs-lookup"><span data-stu-id="9068a-156">Select the **Online** tab</span></span>
-   <span data-ttu-id="9068a-157">Seleccione el paquete **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="9068a-157">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="9068a-158">Haz clic en **Instalar**</span><span class="sxs-lookup"><span data-stu-id="9068a-158">Click **Install**</span></span>

<span data-ttu-id="9068a-159">A continuación, necesitamos cambiar nuestro modelo para generar código que haga uso de la API de DbContext, que se presentó en versiones posteriores de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9068a-159">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="9068a-160">Haga clic con el botón derecho en una zona vacía del modelo en el diseñador de EF y seleccione **Agregar elemento de generación de código..** .</span><span class="sxs-lookup"><span data-stu-id="9068a-160">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="9068a-161">Seleccione **plantillas en línea** en el menú de la izquierda y busque **DbContext**</span><span class="sxs-lookup"><span data-stu-id="9068a-161">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="9068a-162">Seleccione el **generador de DbContext de EF 5. x \# para C**, escriba **BloggingModel** como nombre y haga clic en **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="9068a-162">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![Plantilla DbContext](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="9068a-164">3. generar la base de datos</span><span class="sxs-lookup"><span data-stu-id="9068a-164">3. Generating the Database</span></span>

<span data-ttu-id="9068a-165">Dado nuestro modelo, Entity Framework puede calcular un esquema de base de datos que nos permitirá almacenar y recuperar datos mediante el modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-165">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="9068a-166">El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:</span><span class="sxs-lookup"><span data-stu-id="9068a-166">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="9068a-167">Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.</span><span class="sxs-lookup"><span data-stu-id="9068a-167">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="9068a-168">Si usa Visual Studio 2012, va a crear una base de datos de [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) .</span><span class="sxs-lookup"><span data-stu-id="9068a-168">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="9068a-169">Vamos a generar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9068a-169">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="9068a-170">Haga clic con el botón secundario en la superficie de diseño y seleccione **generar base de datos del modelo..** .</span><span class="sxs-lookup"><span data-stu-id="9068a-170">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="9068a-171">Haga clic en **nueva conexión..** .</span><span class="sxs-lookup"><span data-stu-id="9068a-171">Click **New Connection…**</span></span> <span data-ttu-id="9068a-172">y especifique LocalDB o SQL Express, en función de la versión de Visual Studio que use, escriba **ModelFirst. blog** como nombre de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9068a-172">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![Conexión de LocalDB MF](~/ef6/media/localdbconnectionmf.png)

    ![Conexión de SQL Express MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="9068a-175">Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .</span><span class="sxs-lookup"><span data-stu-id="9068a-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="9068a-176">Seleccione **siguiente** y el Entity Framework Designer calculará un script para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9068a-176">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="9068a-177">Una vez que se muestre el script, haga clic en **Finalizar** y el script se agregará al proyecto y se abrirá.</span><span class="sxs-lookup"><span data-stu-id="9068a-177">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="9068a-178">Haga clic con el botón derecho en el script y seleccione **Ejecutar**; se le pedirá que especifique la base de datos a la que se va a conectar, especifique LocalDB o SQL Server Express, en función de la versión de Visual Studio que use.</span><span class="sxs-lookup"><span data-stu-id="9068a-178">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="9068a-179">4. leer & escribir datos</span><span class="sxs-lookup"><span data-stu-id="9068a-179">4. Reading & Writing Data</span></span>

<span data-ttu-id="9068a-180">Ahora que tenemos un modelo, es el momento de usarlo para tener acceso a algunos datos.</span><span class="sxs-lookup"><span data-stu-id="9068a-180">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="9068a-181">Las clases que se van a usar para obtener acceso a los datos se generan automáticamente basándose en el archivo EDMX.</span><span class="sxs-lookup"><span data-stu-id="9068a-181">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="9068a-182">*Esta captura de pantalla es de Visual Studio 2012, si usa Visual Studio 2010, los archivos BloggingModel.tt y BloggingModel.Context.tt estarán directamente en el proyecto en lugar de anidados en el archivo EDMX.*</span><span class="sxs-lookup"><span data-stu-id="9068a-182">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![Clases generadas](~/ef6/media/generatedclasses.png)

<span data-ttu-id="9068a-184">Implemente el método Main en Program.cs como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="9068a-184">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="9068a-185">Este código crea una nueva instancia de nuestro contexto y, a continuación, la usa para insertar un nuevo blog.</span><span class="sxs-lookup"><span data-stu-id="9068a-185">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="9068a-186">A continuación, usa una consulta LINQ para recuperar todos los blogs de la base de datos ordenados alfabéticamente por título.</span><span class="sxs-lookup"><span data-stu-id="9068a-186">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="9068a-187">Ahora puede ejecutar la aplicación y probarla.</span><span class="sxs-lookup"><span data-stu-id="9068a-187">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="9068a-188">5. tratar con cambios en el modelo</span><span class="sxs-lookup"><span data-stu-id="9068a-188">5. Dealing with Model Changes</span></span>

<span data-ttu-id="9068a-189">Ahora es el momento de realizar algunos cambios en nuestro modelo, cuando realizamos estos cambios también necesitamos actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9068a-189">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="9068a-190">Comenzaremos agregando una nueva entidad de usuario a nuestro modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-190">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="9068a-191">Agregue un nuevo nombre de entidad de **usuario** **con el** nombre de usuario como el nombre de clave y la **cadena** como el tipo de propiedad de la clave.</span><span class="sxs-lookup"><span data-stu-id="9068a-191">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![Agregar entidad de usuario](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="9068a-193">Haga clic con el botón derecho en la propiedad **username** en la superficie de diseño y seleccione **propiedades**, en el ventana Propiedades cambie la configuración de **MaxLength** a **50** 
     *esto restringe los datos que se pueden almacenar en el nombre de usuario a 50 caracteres* .</span><span class="sxs-lookup"><span data-stu-id="9068a-193">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="9068a-194">Agregar una propiedad escalar **displayName** a la entidad **User**</span><span class="sxs-lookup"><span data-stu-id="9068a-194">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="9068a-195">Ahora tenemos un modelo actualizado y estamos preparados para actualizar la base de datos para dar cabida a nuestro nuevo tipo de entidad de usuario.</span><span class="sxs-lookup"><span data-stu-id="9068a-195">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="9068a-196">Haga clic con el botón secundario en la superficie de diseño y seleccione **generar base de datos a partir del modelo...**, Entity Framework calculará un script para volver a crear un esquema basado en el modelo actualizado.</span><span class="sxs-lookup"><span data-stu-id="9068a-196">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="9068a-197">Haga clic en **Finish** (Finalizar).</span><span class="sxs-lookup"><span data-stu-id="9068a-197">Click **Finish**</span></span>
-   <span data-ttu-id="9068a-198">Puede recibir advertencias sobre cómo sobrescribir el script DDL existente y las partes de asignación y almacenamiento del modelo; haga clic en **sí** para ambas advertencias.</span><span class="sxs-lookup"><span data-stu-id="9068a-198">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="9068a-199">El script SQL actualizado para crear la base de datos se abre automáticamente.</span><span class="sxs-lookup"><span data-stu-id="9068a-199">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="9068a-200">*El script que se genera quitará todas las tablas existentes y, a continuación, volverá a crear el esquema desde el principio. Esto puede funcionar para el desarrollo local, pero no es viable para insertar cambios en una base de datos que ya se ha implementado. Si necesita publicar cambios en una base de datos que ya se ha implementado, deberá editar el script o usar una herramienta de comparación de esquemas para calcular un script de migración.*</span><span class="sxs-lookup"><span data-stu-id="9068a-200">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="9068a-201">Haga clic con el botón derecho en el script y seleccione **Ejecutar**; se le pedirá que especifique la base de datos a la que se va a conectar, especifique LocalDB o SQL Server Express, en función de la versión de Visual Studio que use.</span><span class="sxs-lookup"><span data-stu-id="9068a-201">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="9068a-202">Resumen</span><span class="sxs-lookup"><span data-stu-id="9068a-202">Summary</span></span>

<span data-ttu-id="9068a-203">En este tutorial, hemos visto Model First desarrollo, que nos permitió crear un modelo en EF Designer y, a continuación, generar una base de datos a partir de ese modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-203">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="9068a-204">Después usamos el modelo para leer y escribir algunos datos de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9068a-204">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="9068a-205">Por último, hemos actualizado el modelo y, a continuación, hemos recreado el esquema de la base de datos para que coincida con el modelo.</span><span class="sxs-lookup"><span data-stu-id="9068a-205">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
