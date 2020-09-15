---
title: 'Varios diagramas por modelo: EF6'
description: Varios diagramas por modelo en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/multiple-diagrams
ms.openlocfilehash: d934a1b709c93fe8e6b57d40dd874b564885b19f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073336"
---
# <a name="multiple-diagrams-per-model"></a>Varios diagramas por modelo
> [!NOTE]
> **EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

En este vídeo y en la página se muestra cómo dividir un modelo en varios diagramas mediante el Entity Framework Designer (EF Designer). Es posible que desee usar esta característica cuando el modelo sea demasiado grande para verlo o editarlo.

En versiones anteriores del diseñador de EF solo podía tener un diagrama por el archivo EDMX. A partir de Visual Studio 2012, puede usar el diseñador de EF para dividir el archivo EDMX en varios diagramas.

## <a name="watch-the-video"></a>Visualización del vídeo
En este vídeo se muestra cómo dividir un modelo en varios diagramas mediante el Entity Framework Designer (EF Designer). Es posible que desee usar esta característica cuando el modelo sea demasiado grande para verlo o editarlo.

**Presentada por**: Julia Kornich

**Vídeo**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)  |  [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)

## <a name="ef-designer-overview"></a>Información general del diseñador EF

Al crear un modelo mediante el Asistente para Entity Data Model del diseñador de EF, se crea un archivo. edmx y se agrega a la solución. Este archivo define la forma de las entidades y cómo se asignan a la base de datos.

El diseñador de EF consta de los siguientes componentes:

-   Superficie de diseño visual para editar el modelo. Puede crear, modificar o eliminar entidades y asociaciones.
-    **Model Browser**   Ventana del explorador de modelos que proporciona vistas de árbol del modelo.Las entidades y sus asociaciones se encuentran en la carpeta * \[ modelname \] * . Las tablas y restricciones de base de datos se encuentran en la * \[ modelname \] *. Carpeta de almacenamiento.
-   Una ventana de **detalles de asignación** para ver y editar asignaciones. Puede asignar tipos de entidad o asociaciones a tablas, columnas y procedimientos almacenados de base de datos. 

La ventana superficie de diseño visual se abre automáticamente cuando finaliza el Asistente para Entity Data Model. Si el explorador de modelos no está visible, haga clic con el botón secundario en la superficie de diseño principal y seleccione **Explorador de modelos**.

En la captura de pantalla siguiente se muestra un archivo. edmx abierto en el diseñador de EF. En la captura de pantalla se muestra la superficie de diseño visual (a la izquierda) y la ventana del **Explorador de modelos**   (a la derecha).

![EF Designer 2](~/ef6/media/efdesigner2.png)

Para deshacer una operación realizada en EF Designer, haga clic en Ctrl + Z.

## <a name="working-with-diagrams"></a>Trabajar con diagramas

De forma predeterminada, el diseñador de EF crea un diagrama denominado Diagram1. Si tiene un diagrama con un gran número de entidades y asociaciones, lo más conveniente es dividirlos lógicamente. A partir de Visual Studio 2012, puede ver el modelo conceptual en varios diagramas.   

A medida que agrega nuevos diagramas, aparecen en la carpeta diagramas de la ventana Explorador de modelos. Para cambiar el nombre de un diagrama: seleccione el diagrama en la ventana Explorador de modelos, haga clic en una vez en el nombre y escriba el nuevo nombre. También puede hacer clic con el botón secundario en el nombre del diagrama y seleccionar **cambiar nombre**.

El nombre del diagrama se muestra junto al nombre del archivo. edmx en el editor de Visual Studio. Por ejemplo, Model1. edmx \[ Diagram1 \] .

![Nombre del diagrama](~/ef6/media/diagramname.png)

El contenido de los diagramas (forma y color de las entidades y asociaciones) se almacena en el archivo. edmx. diagram. Para ver este archivo, seleccione Explorador de soluciones y desdoblar el archivo. edmx. 

![Archivos de diagrama](~/ef6/media/diagramfiles.png)

No debe modificar manualmente el archivo. edmx. Diagram, es posible que el contenido de este archivo se sobrescriba con el diseñador de EF.
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a>Dividir entidades y asociaciones en un nuevo diagrama

Puede seleccionar entidades en el diagrama existente (mantenga presionada la tecla Mayús para seleccionar varias entidades). Haga clic con el botón secundario del mouse y seleccione **ir al nuevo diagrama**. Se crea el nuevo diagrama y las entidades seleccionadas y sus asociaciones se mueven al diagrama.

Como alternativa, puede hacer clic con el botón secundario en la carpeta diagramas del explorador de modelos y seleccionar **Agregar nuevo diagrama.** Después, puede arrastrar y colocar entidades desde la carpeta tipos de entidad del explorador de modelos hasta la superficie de diseño.

También puede cortar o copiar entidades (con las teclas Ctrl-X o Ctrl + C) de un diagrama y pegar (mediante la tecla Ctrl-V) en la otra. Si el diagrama en el que pega una entidad ya contiene una entidad con el mismo nombre, se creará una nueva entidad y se agregará al modelo.Por ejemplo: Diagram2 contiene la entidad Department. A continuación, pegue otro departamento en Diagram2. La entidad Department1 se crea y se agrega al modelo conceptual.   

Para incluir entidades relacionadas en un diagrama, haga clic en la entidad y seleccione **incluir relacionados**. Esto hará que se realice una copia de las entidades y asociaciones relacionadas en el diagrama especificado.

## <a name="changing-the-color-of-entities"></a>Cambiar el color de las entidades

Además de dividir un modelo en varios diagramas, también puede cambiar los colores de las entidades.

Para cambiar el color, seleccione una entidad (o varias entidades) en la superficie de diseño. A continuación, haga clic con el botón secundario del mouse y seleccione **propiedades**. En el ventana Propiedades, seleccione la propiedad **color de relleno** . Especifique el color mediante un nombre de color válido (por ejemplo, rojo) o un RGB válido (por ejemplo, 255, 128, 128). 

![Cambiar color](~/ef6/media/color.png)

## <a name="summary"></a>Resumen

En este tema, hemos examinado cómo dividir un modelo en varios diagramas y cómo especificar un color diferente para una entidad mediante el Entity Framework Designer. 
