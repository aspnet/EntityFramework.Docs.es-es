# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="274eb-101">Contribución a la documentación de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="274eb-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="274eb-102">A continuación se explica el proceso de contribución a los artículos y ejemplos de código de la documentación de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="274eb-102">The process of contributing articles and code samples to the Entity Framework documentation is explained below.</span></span> <span data-ttu-id="274eb-103">Las contribuciones pueden ser tan simples como corregir errores ortográficos o tan complejos como redactar artículos nuevos.</span><span class="sxs-lookup"><span data-stu-id="274eb-103">Contributions can be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="274eb-104">Cómo hacer una corrección o sugerencia sencilla</span><span class="sxs-lookup"><span data-stu-id="274eb-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="274eb-105">Los artículos se almacenan como archivos Markdown en este repositorio.</span><span class="sxs-lookup"><span data-stu-id="274eb-105">Articles are stored as Markdown files in this repository.</span></span> <span data-ttu-id="274eb-106">Para realizar un cambio sencillo al contenido de un archivo Markdown, haga clic en el vínculo **Editar** de la esquina superior derecha de la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="274eb-106">To make a simple change to the content of a Markdown file, click the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="274eb-107">Puede expandir la barra **Opciones** para mostrar el vínculo **Editar**.</span><span class="sxs-lookup"><span data-stu-id="274eb-107">You might need to expand the **options** bar to see the **Edit** link.</span></span> <span data-ttu-id="274eb-108">Siga las instrucciones para crear una solicitud de incorporación de cambios (PR).</span><span class="sxs-lookup"><span data-stu-id="274eb-108">Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="274eb-109">El equipo de EF revisará la PR y la aceptará o sugerirá cambios.</span><span class="sxs-lookup"><span data-stu-id="274eb-109">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="274eb-110">Cómo realizar un envío más complejo</span><span class="sxs-lookup"><span data-stu-id="274eb-110">How to make a more complex submission</span></span>

<span data-ttu-id="274eb-111">Necesita tener conocimientos básicos de [Git y GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="274eb-111">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="274eb-112">Abra un [asunto](https://github.com/dotnet/EntityFramework.Docs/issues/new) que describa qué quiere hacer, como cambiar un artículo existente o crear uno nuevo.</span><span class="sxs-lookup"><span data-stu-id="274eb-112">Open an [issue](https://github.com/dotnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="274eb-113">Espere la aprobación del equipo de EF antes de dedicar mucho tiempo.</span><span class="sxs-lookup"><span data-stu-id="274eb-113">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="274eb-114">Bifurque el repositorio [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) y cree una rama para los cambios.</span><span class="sxs-lookup"><span data-stu-id="274eb-114">Fork the [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="274eb-115">Envíe una solicitud de incorporación de cambios (PR) al administrador con los cambios.</span><span class="sxs-lookup"><span data-stu-id="274eb-115">Submit a pull request (PR) to master with your changes.</span></span>
* <span data-ttu-id="274eb-116">Responder a los comentarios de la PR.</span><span class="sxs-lookup"><span data-stu-id="274eb-116">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="274eb-117">Sintaxis de Markdown</span><span class="sxs-lookup"><span data-stu-id="274eb-117">Markdown syntax</span></span>

<span data-ttu-id="274eb-118">Los artículos se escriben en [DocFx flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), un superconjunto de [GitHub flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="274eb-118">Articles are written in [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="274eb-119">Para obtener ejemplos de sintaxis y metadatos de DFM para las características de interfaz de usuario usadas en la documentación de EF, vea [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) (Metadatos y plantillas de Markdown) en la guía de estilo del repositorio .NET Core.</span><span class="sxs-lookup"><span data-stu-id="274eb-119">For examples of DFM syntax and metadata for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Core repo style guide.</span></span>

## <a name="folder-structure-conventions"></a><span data-ttu-id="274eb-120">Convenciones de estructura de carpetas</span><span class="sxs-lookup"><span data-stu-id="274eb-120">Folder structure conventions</span></span>

<span data-ttu-id="274eb-121">Las imágenes y el resto del contenido estático se almacenan en una carpeta `_static` dentro de cada área o carpeta del sitio.</span><span class="sxs-lookup"><span data-stu-id="274eb-121">Images and other static content are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="274eb-122">Los ejemplos de código se almacenan en la carpeta raíz `samples`.</span><span class="sxs-lookup"><span data-stu-id="274eb-122">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="274eb-123">Estos ejemplos se organizan en una estructura de carpetas que imita la estructura de la documentación (en la carpeta raíz `entity-framework`).</span><span class="sxs-lookup"><span data-stu-id="274eb-123">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="274eb-124">Fragmentos de código</span><span class="sxs-lookup"><span data-stu-id="274eb-124">Code snippets</span></span>

<span data-ttu-id="274eb-125">Los artículos suelen contener fragmentos de código para ilustrar el contenido.</span><span class="sxs-lookup"><span data-stu-id="274eb-125">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="274eb-126">DFM permite copiar código en el archivo de Markdown o hacer referencia a un archivo de código independiente.</span><span class="sxs-lookup"><span data-stu-id="274eb-126">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="274eb-127">Siempre que sea posible, use archivos de código independientes para minimizar la posibilidad de errores en el código.</span><span class="sxs-lookup"><span data-stu-id="274eb-127">Whenever possible, use separate code files to minimize the chance of errors in the code.</span></span> <span data-ttu-id="274eb-128">Los archivos de código deben estar almacenarse en el repositorio con la estructura de carpetas que se ha descrito antes para proyectos de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="274eb-128">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="274eb-129">Estos son algunos ejemplos de [sintaxis de fragmento de código DFM](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span><span class="sxs-lookup"><span data-stu-id="274eb-129">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="274eb-130">Para representar un archivo de código completo como un fragmento de código:</span><span class="sxs-lookup"><span data-stu-id="274eb-130">To render an entire code file as a snippet:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="274eb-131">Para representar una parte de un archivo como un fragmento de código mediante el uso de números de línea:</span><span class="sxs-lookup"><span data-stu-id="274eb-131">To render a portion of a file as a snippet by using line numbers:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="274eb-132">Para fragmentos de código en C#, haga referencia a una [región de C#](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span><span class="sxs-lookup"><span data-stu-id="274eb-132">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="274eb-133">Use regiones en vez de números de línea.</span><span class="sxs-lookup"><span data-stu-id="274eb-133">Use regions rather than line numbers.</span></span> <span data-ttu-id="274eb-134">Los números de línea de un archivo de código tienden a cambiar y dejan de estar sincronizados con las referencias de números de línea en Markdown.</span><span class="sxs-lookup"><span data-stu-id="274eb-134">Line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="274eb-135">Las regiones de C# se pueden anidar.</span><span class="sxs-lookup"><span data-stu-id="274eb-135">C# regions can be nested.</span></span> <span data-ttu-id="274eb-136">Si se hace referencia a la región externa, las directivas `#region` y `#endregion` internas no se representan en un fragmento de código.</span><span class="sxs-lookup"><span data-stu-id="274eb-136">If you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="274eb-137">Para representar una región de C# denominada "snippet_Example":</span><span class="sxs-lookup"><span data-stu-id="274eb-137">To render a C# region named "snippet_Example":</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="274eb-138">Para resaltar las líneas seleccionadas en un fragmento representado (normalmente se representa con el color de fondo amarillo):</span><span class="sxs-lookup"><span data-stu-id="274eb-138">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="274eb-139">Probar los cambios con DocFX</span><span class="sxs-lookup"><span data-stu-id="274eb-139">Test your changes with DocFX</span></span>

<span data-ttu-id="274eb-140">Pruebe los cambios con la [herramienta de línea de comandos DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), que crea una versión del sitio hospedada localmente.</span><span class="sxs-lookup"><span data-stu-id="274eb-140">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="274eb-141">DocFX no representa las extensiones de sitio y de estilo creadas para docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="274eb-141">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="274eb-142">DocFX requiere .NET Framework en Windows o Mono para Linux o macOS.</span><span class="sxs-lookup"><span data-stu-id="274eb-142">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="274eb-143">Instrucciones para Windows</span><span class="sxs-lookup"><span data-stu-id="274eb-143">Windows instructions</span></span>

* <span data-ttu-id="274eb-144">Descargue y descomprima *docfx.zip* desde [versiones de DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="274eb-144">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="274eb-145">Agregue DocFX a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="274eb-145">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="274eb-146">En una ventana de línea de comandos, desplácese hasta el repositorio clonado (que contiene el archivo *docfx.json*) y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="274eb-146">In a command-line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ```console
   docfx -t default --serve
   ```

* <span data-ttu-id="274eb-147">En un navegador, vaya a `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="274eb-147">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="274eb-148">Instrucciones para Mono</span><span class="sxs-lookup"><span data-stu-id="274eb-148">Mono instructions</span></span>

* <span data-ttu-id="274eb-149">Instale Mono a través de Homebrew: `brew install mono`.</span><span class="sxs-lookup"><span data-stu-id="274eb-149">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="274eb-150">Descargue la [versión más reciente de DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span><span class="sxs-lookup"><span data-stu-id="274eb-150">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="274eb-151">Extraiga en `\bin\docfx`.</span><span class="sxs-lookup"><span data-stu-id="274eb-151">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="274eb-152">Cree un alias para **docfx**:</span><span class="sxs-lookup"><span data-stu-id="274eb-152">Create an alias for **docfx**:</span></span>

  ```console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="274eb-153">Ejecute **docfx** en el repositorio clonado para generar el sitio y **docfx-serve** para ver el sitio en `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="274eb-153">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="274eb-154">Voz y tono</span><span class="sxs-lookup"><span data-stu-id="274eb-154">Voice and tone</span></span>

<span data-ttu-id="274eb-155">Nuestro objetivo consiste en escribir documentación que sea fácil de comprender para el público más amplio posible.</span><span class="sxs-lookup"><span data-stu-id="274eb-155">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="274eb-156">Con ese fin, hemos establecido directrices para escribir el estilo que queremos que sigan nuestros colaboradores.</span><span class="sxs-lookup"><span data-stu-id="274eb-156">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="274eb-157">Para obtener más información, vea [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) (Directrices de voz y tono) en el repositorio de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="274eb-157">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET Core repo.</span></span>
