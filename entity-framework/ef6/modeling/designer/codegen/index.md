---
title: 'Plantillas de generación de código del diseñador: EF6'
description: Plantillas de generación de código del diseñador en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/index
ms.openlocfilehash: 9eb0a930d7b283e449f6b08c090c8483453de452
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066491"
---
# <a name="designer-code-generation-templates"></a>Plantillas de generación de código del diseñador
Cuando se crea un modelo con Entity Framework Designer, las clases y el contexto derivado se generan automáticamente. Además de la generación de código predeterminada, también se proporcionan una serie de plantillas que pueden usarse para personalizar el código que se genera. Estas plantillas se proporcionan como plantillas de texto T4, lo que permite personalizarlas en caso necesario.

El código que se genera de forma predeterminada depende de la versión de Visual Studio en que se cree el modelo:

-   Los modelos creados en Visual Studio 2012 y 2013 generan clases de entidad POCO simples y un contexto que se deriva del elemento DbContext simplificado.
-   Los modelos creados en Visual Studio 2010 generan clases de entidad que se derivan de EntityObject y un contexto que se deriva de ObjectContext.
  > [!NOTE]    
  > Se recomienda cambiar a la plantilla Generador de DbContext una vez agregado el modelo.

En esta página se habla de las plantillas disponibles y luego se proporcionan instrucciones para agregar una plantilla al modelo.

## <a name="available-templates"></a>Plantillas disponibles

El equipo de Entity Framework proporciona las siguientes plantillas:

### <a name="dbcontext-generator"></a>Generador de DbContext

Esta plantilla genera clases de entidad POCO simples y un contexto que se deriva de DbContext mediante EF6.
Es la plantilla recomendada, a menos que tenga motivos para usar una de las otras plantillas que se indican a continuación.
También es la plantilla de generación de código que se obtiene de forma predeterminada cuando se usan versiones recientes de Visual Studio (Visual Studio 2013 y versiones posteriores): cuando se crea un nuevo modelo, se usa esta plantilla de forma predeterminada y los archivos T4 (.tt) se anidan en el archivo .edmx.

#### <a name="older-versions-of-visual-studio"></a>Versiones anteriores de Visual Studio
- **Visual Studio 2012:** para obtener las plantillas **EF 6.x DbContextGenerator**, tiene que instalar la versión más reciente de **Entity Framework Tools para Visual Studio**: vea la página [Get Entity Framework](xref:ef6/fundamentals/install) (Obtener Entity Framework) para obtener más información.
- **Visual Studio 2010:** las plantillas **EF 6.x DbContextGenerator** no están disponibles para Visual Studio 2010.

#### <a name="dbcontext-generator-for-ef-5x"></a>Generador de DbContext para EF 5.x

Si usa una versión anterior del paquete NuGet de EntityFramework (uno con una versión principal de 5), tiene que usar la plantilla **Generador de DbContext para EF 5.x**.

Si usa Visual Studio 2013 o 2012, esta plantilla ya está instalada.

Si usa Visual Studio 2010, tiene que seleccionar la pestaña **Online** al agregar la plantilla para descargarla desde la Galería de Visual Studio. También puede instalar la plantilla directamente desde la Galería de Visual Studio previamente. Dado que las plantillas están incluidas en las versiones posteriores de Visual Studio, las versiones de la galería solo se pueden instalar en Visual Studio 2010.

- [Generador de DbContext para EF 5.x para C#](https://visualstudiogallery.msdn.microsoft.com/da740968-02f9-42a9-9ee4-1a9a06d896a2)
- [Generador de DbContext para EF 5.x para sitios web de C#](https://visualstudiogallery.msdn.microsoft.com/5d01a981-91b8-492c-b42c-c771c3f31e03)
- [Generador de DbContext para EF 5.x para VB.NET](https://visualstudiogallery.msdn.microsoft.com/875c882d-333e-455a-8dae-5353510527dd?src=featured)
- [Generador de DbContext para EF 5.x para sitios web de VB.NET](https://visualstudiogallery.msdn.microsoft.com/d4d7d4cd-c2d0-43e6-8944-12f6ff8f2614)

#### <a name="dbcontext-generator-for-ef-4x"></a>Generador de DbContext para EF 4.x

Si usa una versión anterior del paquete NuGet de EntityFramework (uno con una versión principal de 4), tiene que usar la plantilla **Generador de DbContext para EF 4.x**. La encontrará en la pestaña **Online** al agregar la plantilla o puede instalarla directamente desde la Galería de Visual Studio previamente.

- [Generador de DbContext para EF 4.x para C#](https://visualstudiogallery.msdn.microsoft.com/7812b04c-db36-4817-8a84-e73c452410a2)
- [Generador de DbContext para EF 4.x para sitios web de C#](https://visualstudiogallery.msdn.microsoft.com/de0e9bc6-e86a-4448-8a2e-a1260a53203e)
- [Generador de DbContext para EF 4.x para VB.NET](https://visualstudiogallery.msdn.microsoft.com/73679ae5-e358-4e76-a538-c7b5e04ac073)
- [Generador de DbContext para EF 4.x para sitios web de VB.NET](https://visualstudiogallery.msdn.microsoft.com/86f5a660-306e-4831-840c-2e4ee7474a92)

### <a name="entityobject-generator"></a>Generador de EntityObject

Esta plantilla genera clases de entidad que se derivan de EntityObject y un contexto que se deriva de ObjectContext.

> [!NOTE]
> Considere la posibilidad de usar Generador de DbContext

Generador de DbContext es ahora la plantilla recomendada para las nuevas aplicaciones. Generador de DbContext aprovecha la API de DbContext, que es más sencilla. Generador de EntityObject sigue estando disponible para la compatibilidad con las aplicaciones existentes.

**Visual Studio 2010, 2012 &amp; 2013**

Tiene que seleccionar la pestaña **Online** al agregar la plantilla para descargarla desde la Galería de Visual Studio. También puede instalar la plantilla directamente desde la Galería de Visual Studio previamente.

- [Generador de EntityObject para EF 6.x para C#](https://visualstudiogallery.msdn.microsoft.com/66612113-549c-4a9e-a14a-f629ceb3f89a)
- [Generador de EntityObject para EF 6.x para sitios web de C#](https://visualstudiogallery.msdn.microsoft.com/076140f3-6dbe-451f-a0e0-16b6d2bd8996)
- [Generador de EntityObject para EF 6.x para VB.NET](https://visualstudiogallery.msdn.microsoft.com/ff479d55-2c85-43c5-a4d6-21cd659435ea)
- [Generador de EntityObject para EF 6.x para sitios web de VB.NET](https://visualstudiogallery.msdn.microsoft.com/668e2b92-c142-4da2-8e60-866c6346fc6a)

**Generador de EntityObject para EF 5.x**


Si usa Visual Studio 2012 o 2013, tiene que seleccionar la pestaña **Online** al agregar la plantilla para descargarla desde la Galería de Visual Studio. También puede instalar la plantilla directamente desde la Galería de Visual Studio previamente. Dado que las plantillas están incluidas en Visual Studio 2010, las versiones de la galería solo se pueden instalar en Visual Studio 2012 y 2013.

- [Generador de EntityObject para EF 5.x para C#](https://visualstudiogallery.msdn.microsoft.com/1da40393-b5ec-404a-a000-6a7e6e911339)
- [Generador de EntityObject para EF 5.x para sitios web de C#](https://visualstudiogallery.msdn.microsoft.com/94b48556-fcf0-4b9b-8615-20f9066ae9ac)
- [Generador de EntityObject para EF 5.x para VB.NET](https://visualstudiogallery.msdn.microsoft.com/92c0129e-40dc-488c-a836-7e30846dfb30)
- [Generador de EntityObject para EF 5.x para sitios web de VB.NET](https://visualstudiogallery.msdn.microsoft.com/5dd7f75c-8c98-4eb7-b4bc-06f0d0b03b41)

Si solo quiere la generación de código de ObjectContext y no necesita editar la plantilla, puede [revertir a la generación de código de EntityObject](xref:ef6/modeling/designer/codegen/legacy-objectcontext).

Si usa Visual Studio 2010, esta plantilla ya está instalada. Si crea un nuevo modelo en Visual Studio 2010, esta plantilla se usa de forma predeterminada, pero los archivos .tt no se incluyen en el proyecto. Si quiere personalizar la plantilla, tiene que agregarla al proyecto.

### <a name="self-tracking-entities-ste-generator"></a>Generador de entidades de autoseguimiento (STE)

Esta plantilla genera clases de entidad de autoseguimiento y un contexto que se deriva de ObjectContext. En una aplicación de EF, un contexto es el responsable de realizar el seguimiento de los cambios en las entidades. Pero en los escenarios de n niveles, es posible que el contexto no esté disponible en el nivel que modifica las entidades. Las entidades de autoseguimiento ayudan a realizar el seguimiento de los cambios en cualquier nivel. Para obtener más información, vea [Entidades de autoseguimiento](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index).

> [!NOTE]
> La plantilla STE no se recomienda

Ya no se recomienda usar la plantilla STE en las aplicaciones nuevas, aunque sigue estando disponible para la compatibilidad con las aplicaciones existentes. Visite el artículo sobre [entidades desconectadas](xref:ef6/fundamentals/disconnected-entities/index) para ver otras opciones recomendadas para escenarios de n niveles.

> [!NOTE]
> No hay ninguna versión para EF 6.x de la plantilla STE.


> [!NOTE]
> No hay ninguna versión para Visual Studio 2013 de la plantilla STE.

### <a name="visual-studio-2012"></a>Visual Studio 2012

Si usa Visual Studio 2012, tiene que seleccionar la pestaña **Online** al agregar la plantilla para descargarla desde la Galería de Visual Studio. También puede instalar la plantilla directamente desde la Galería de Visual Studio previamente. Dado que las plantillas están incluidas en Visual Studio 2010, las versiones de la galería solo se pueden instalar en Visual Studio 2012.

- [Generador de STE para EF 5.x para C#](https://visualstudiogallery.msdn.microsoft.com/a3ac10a5-9365-4096-bb58-d9a1ba71db8f)
- [Generador de STE para EF 5.x para sitios web de C#](https://visualstudiogallery.msdn.microsoft.com/1b55ab82-eeb4-47ba-8d35-3c7c8b5f5a8c)
- [Generador de STE para EF 5.x para VB.NET](https://visualstudiogallery.msdn.microsoft.com/1ba8c6a3-44e9-4e1f-b21e-596f3168474b)
- [Generador de STE para EF 5.x para sitios web de VB.NET](https://visualstudiogallery.msdn.microsoft.com/a9fd5f0a-9af4-4e32-9c09-0e057072152e)

#### <a name="visual-studio-2010"></a>Visual Studio 2010**

Si usa Visual Studio 2010, esta plantilla ya está instalada.

### <a name="poco-entity-generator"></a>Generador de entidades POCO

Esta plantilla genera clases de entidad POCO y un contexto que se deriva de ObjectContext

> [!NOTE]
> Considere la posibilidad de usar Generador de DbContext

Generador de DbContext es ahora la plantilla recomendada para generar clases POCO en nuevas aplicaciones. Generador de DbContext aprovecha la nueva API de DbContext y puede generar clases POCO más sencillas. Generador de entidades POCO sigue estando disponible para la compatibilidad con las aplicaciones existentes.

> [!NOTE]
> No hay ninguna versión para EF 5.x o EF 6.x de la plantilla STE.

> [!NOTE]
> No hay ninguna versión para Visual Studio 2013 de la plantilla POCO.

#### <a name="visual-studio-2012-amp-visual-studio-2010"></a>Visual Studio 2012 y Visual Studio 2010

Tiene que seleccionar la pestaña **Online** al agregar la plantilla para descargarla desde la Galería de Visual Studio. También puede instalar la plantilla directamente desde la Galería de Visual Studio previamente.

- [Generador de POCO para EF 4.x para C#](https://visualstudiogallery.msdn.microsoft.com/23df0450-5677-4926-96cc-173d02752313)
- [Generador de POCO para EF 4.x para sitios web de C#](https://visualstudiogallery.msdn.microsoft.com/fe568da5-aa1a-4178-a2a5-48813c707a7f)
- [Generador de POCO para EF 4.x para VB.NET](https://visualstudiogallery.msdn.microsoft.com/53ecbded-8936-4299-ab04-1e44e5489752)
- [Generador de POCO para EF 4.x para sitios web de VB.NET](https://visualstudiogallery.msdn.microsoft.com/463c5aca-05ad-4cdb-910b-2e4f83269e34)

### <a name="what-are-the-web-sites-templates"></a>Qué son las plantillas "Sitios web"

Las plantillas "Sitios web" (es decir, **Generador de DbContext para EF 5.x para sitios web de C\#** ) se usan en proyectos de sitio web creados con **Archivo -&gt; Nuevo -&gt; Sitio web...** . Son distintas a las aplicaciones web, creadas con **Archivo -&gt; Nuevo -&gt; Proyecto...** , que usan las plantillas estándar. Se proporcionan plantillas independientes porque así lo exige el sistema de plantilla de elemento de Visual Studio.

## <a name="using-a-template"></a>Uso de una plantilla

Para empezar a usar una plantilla de generación de código, haga clic con el botón derecho en un punto vacío de la superficie de diseño de EF Designer y seleccione **Agregar elemento de generación de código...**

![Agregación de elementos de generación de código](~/ef6/media/add-code-gen-item.png)

Si ya ha instalado la plantilla que quiere usar (o estaba incluida en Visual Studio), estará disponible en la sección **Código** o **Datos** del menú izquierdo.

![Plantilla instalada](~/ef6/media/installed.png)

Si aún no tiene instalada la plantilla, seleccione **Online** en el menú izquierdo y busque la plantilla que quiere.

![Buscar plantilla](~/ef6/media/search.png) 

Si usa Visual Studio 2012, los nuevos archivos .tt aparecen anidados en el archivo .edmx.*

> [!NOTE]
> En el caso de los modelos creados en Visual Studio 2012, tiene que eliminar las plantillas usadas para la generación de código predeterminada; si no lo hace, se generarán clases y contexto duplicados. Los archivos predeterminados son **&lt;nombreDelModelo&gt;.tt** y **&lt;nombreDelModelo&gt;.context.tt**. 

![Plantillas de VS2012](~/ef6/media/vs2012-templates.png)

Si usa Visual Studio 2010, los archivos tt se agregan directamente al proyecto.  

![Plantillas de VS2010](~/ef6/media/vs2010-templates.png)
