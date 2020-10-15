# <a name="contributing-to-the-entity-framework-documentation"></a>Contribución a la documentación de Entity Framework

A continuación se explica el proceso de contribución a los artículos y ejemplos de código de la documentación de Entity Framework. Las contribuciones pueden ser tan simples como corregir errores ortográficos o tan complejos como redactar artículos nuevos.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Cómo hacer una corrección o sugerencia sencilla

Los artículos se almacenan como archivos Markdown en este repositorio. Para realizar un cambio sencillo al contenido de un archivo Markdown, haga clic en el vínculo **Editar** de la esquina superior derecha de la ventana del explorador. Puede expandir la barra **Opciones** para mostrar el vínculo **Editar**. Siga las instrucciones para crear una solicitud de incorporación de cambios (PR). El equipo de EF revisará la PR y la aceptará o sugerirá cambios.

## <a name="how-to-make-a-more-complex-submission"></a>Cómo realizar un envío más complejo

Necesita tener conocimientos básicos de [Git y GitHub.com](https://guides.github.com/activities/hello-world/).

* Abra un [asunto](https://github.com/dotnet/EntityFramework.Docs/issues/new) que describa qué quiere hacer, como cambiar un artículo existente o crear uno nuevo. Espere la aprobación del equipo de EF antes de dedicar mucho tiempo.
* Bifurque el repositorio [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) y cree una rama para los cambios.
* Envíe una solicitud de incorporación de cambios (PR) al administrador con los cambios.
* Responder a los comentarios de la PR.

## <a name="markdown-syntax"></a>Sintaxis de Markdown

Los artículos se escriben en [DocFx flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), un superconjunto de [GitHub flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/). Para obtener ejemplos de sintaxis y metadatos de DFM para las características de interfaz de usuario usadas en la documentación de EF, vea [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) (Metadatos y plantillas de Markdown) en la guía de estilo del repositorio .NET Core.

## <a name="folder-structure-conventions"></a>Convenciones de estructura de carpetas

Las imágenes y el resto del contenido estático se almacenan en una carpeta `_static` dentro de cada área o carpeta del sitio.

Los ejemplos de código se almacenan en la carpeta raíz `samples`. Estos ejemplos se organizan en una estructura de carpetas que imita la estructura de la documentación (en la carpeta raíz `entity-framework`).

## <a name="code-snippets"></a>Fragmentos de código

Los artículos suelen contener fragmentos de código para ilustrar el contenido. DFM permite copiar código en el archivo de Markdown o hacer referencia a un archivo de código independiente. Siempre que sea posible, use archivos de código independientes para minimizar la posibilidad de errores en el código. Los archivos de código deben estar almacenarse en el repositorio con la estructura de carpetas que se ha descrito antes para proyectos de ejemplo.

Estos son algunos ejemplos de [sintaxis de fragmento de código DFM](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).

Para representar un archivo de código completo como un fragmento de código:

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

Para representar una parte de un archivo como un fragmento de código mediante el uso de números de línea:

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

Para fragmentos de código en C#, haga referencia a una [región de C#](https://msdn.microsoft.com/library/9a1ybwek.aspx). Use regiones en vez de números de línea. Los números de línea de un archivo de código tienden a cambiar y dejan de estar sincronizados con las referencias de números de línea en Markdown. Las regiones de C# se pueden anidar. Si se hace referencia a la región externa, las directivas `#region` y `#endregion` internas no se representan en un fragmento de código.

Para representar una región de C# denominada "snippet_Example":

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

Para resaltar las líneas seleccionadas en un fragmento representado (normalmente se representa con el color de fondo amarillo):

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a>Probar los cambios con DocFX

Pruebe los cambios con la [herramienta de línea de comandos DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), que crea una versión del sitio hospedada localmente. DocFX no representa las extensiones de sitio y de estilo creadas para docs.microsoft.com.

DocFX requiere .NET Framework en Windows o Mono para Linux o macOS.

### <a name="windows-instructions"></a>Instrucciones para Windows

* Descargue y descomprima *docfx.zip* desde [versiones de DocFX](https://github.com/dotnet/docfx/releases).
* Agregue DocFX a la ruta de acceso.
* En una ventana de línea de comandos, desplácese hasta el repositorio clonado (que contiene el archivo *docfx.json*) y ejecute el siguiente comando:

   ```console
   docfx -t default --serve
   ```

* En un navegador, vaya a `http://localhost:8080`.

### <a name="mono-instructions"></a>Instrucciones para Mono

* Instale Mono a través de Homebrew: `brew install mono`.
* Descargue la [versión más reciente de DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).
* Extraiga en `\bin\docfx`.
* Cree un alias para **docfx**:

  ```console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* Ejecute **docfx** en el repositorio clonado para generar el sitio y **docfx-serve** para ver el sitio en `http://localhost:8080`.

## <a name="voice-and-tone"></a>Voz y tono

Nuestro objetivo consiste en escribir documentación que sea fácil de comprender para el público más amplio posible. Con ese fin, hemos establecido directrices para escribir el estilo que queremos que sigan nuestros colaboradores. Para obtener más información, vea [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) (Directrices de voz y tono) en el repositorio de .NET Core.
