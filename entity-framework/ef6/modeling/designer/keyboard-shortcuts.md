---
title: Métodos abreviados de teclado de Entity Framework Designer-EF6
description: Entity Framework Designer métodos abreviados de teclado en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 3552f2b9bfc499654bc1dce4a81d37cacc8b7996
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073388"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Métodos abreviados de teclado Entity Framework Designer
Esta página proporciona una lista de shorcuts de teclado disponibles en las distintas pantallas de la Entity Framework Tools para Visual Studio.

## <a name="adonet-entity-data-model-wizard"></a>Asistente para Entity Data Model de ADO.NET

### <a name="step-one-choose-model-contents"></a>Paso 1: elegir el contenido del modelo

![Asistente uno](~/ef6/media/wizardone.png)

| Acceso directo  | Acción                                                     | Notas                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | Pasar a la siguiente pantalla                                        | No está disponible para todas las selecciones del contenido del modelo. |
| **Alt + f** | Finalice el asistente.                                              | No está disponible para todas las selecciones del contenido del modelo. |
| **Alt + w** | Cambiar el foco a "¿Qué debe contener el modelo?" . |                                                     |

### <a name="step-two-choose-your-connection"></a>Paso dos: elegir la conexión

![Asistente dos](~/ef6/media/wizardtwo.png)

| Acceso directo  | Acción                                                     | Notas                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | Pasar a la siguiente pantalla                                        |                                                         |
| **ALT + p** | Pasar a la pantalla anterior                                    |                                                         |
| **Alt + w** | Cambiar el foco a "¿Qué debe contener el modelo?" . |                                                         |
| **Alt + c** | Abra la ventana "propiedades de conexión".                    | Permite la definición de una nueva conexión de base de datos. |
| **Alt + e** | Excluir datos confidenciales de la cadena de conexión          |                                                         |
| **ALT + i** | Incluir datos confidenciales en la cadena de conexión            |                                                         |
| **Alt + s** | Alternar la opción "Guardar configuración de conexión en App.Config" |                                                         |

### <a name="step-three-choose-your-version"></a>Paso tres: elegir la versión

![Asistente tres](~/ef6/media/wizardthree.png)

| Acceso directo  | Acción                                             | Notas                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | Pasar a la siguiente pantalla                                |                                                                                       |
| **ALT + p** | Pasar a la pantalla anterior                            |                                                                                       |
| **Alt + w** | Cambiar el foco a la selección de la versión de Entity Framework | Permite especificar una versión diferente de Entity Framework para su uso en el proyecto. |

### <a name="step-four-choose-your-database-objects-and-settings"></a>Paso cuatro: elegir los objetos y la configuración de la base de datos

![Asistente cuatro](~/ef6/media/wizardfour.png)

| Acceso directo  | Acción                                                                                    | Notas                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | Finalice el asistente.                                                                             |                                                                     |
| **ALT + p** | Pasar a la pantalla anterior                                                                   |                                                                     |
| **Alt + w** | Cambiar el foco al panel de selección de objetos de base de datos                                            | Permite especificar los objetos de base de datos a los que se va a aplicar ingeniería inversa.    |
| **Alt + s** | Alternar la opción "pluralización o singularización de los nombres de objeto generados"                       |                                                                     |
| **Alt + k** | Alternar la opción "incluir columnas de clave externa en el modelo"                              | No está disponible para todas las selecciones del contenido del modelo.                 |
| **ALT + i** | Alternar la opción "importar funciones y procedimientos almacenados seleccionados en el modelo de entidad" | No está disponible para todas las selecciones del contenido del modelo.                 |
| **Alt + m** | Cambia el foco al campo de texto "espacio de nombres del modelo".                                        | No está disponible para todas las selecciones del contenido del modelo.                 |
| **Espacia** | Alternar selección en elemento                                                               | Si el elemento tiene elementos secundarios, todos los elementos secundarios también se alternarán |
| **Left**  | Contraer árbol secundario                                                                       |                                                                     |
| **Right** | Expandir árbol secundario                                                                         |                                                                     |
| **Up** (Arriba)    | Navegar al elemento anterior en el árbol                                                      |                                                                     |
| **Bajar**  | Navegar al siguiente elemento en el árbol                                                          |                                                                     |

## <a name="ef-designer-surface"></a>Superficie del diseñador de EF

![Superficie del diseñador](~/ef6/media/designersurface.png)

| Acceso directo                                                                                | Acción                      | Notas                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espacio/Entrar**                                                                         | Alternar selección            | Alterna la selección en el objeto que tiene el foco.                                                                                                                                                                                         |
| **Esc**                                                                                 | Cancelar selección            | Cancela la selección actual.                                                                                                                                                                                                      |
| **CTRL+A**                                                                            | Seleccionar todo                  | Selecciona todas las formas en la superficie de diseño.                                                                                                                                                                                       |
| **Flecha arriba**                                                                            | Subir                     | Sube la entidad seleccionada un incremento de la cuadrícula. <br/> Si está en una lista, se desplaza al subcampo relacionado anterior.                                                                                                                            |
| **Flecha abajo**                                                                          | Bajar                   | Baja una entidad seleccionada un incremento de la cuadrícula. <br/> Si se encuentra en una lista, se desplaza al siguiente subcampo relacionado.                                                                                                                              |
| **Flecha izquierda**                                                                          | Moverse a la izquierda                   | Mueve la entidad seleccionada a la izquierda un incremento de la cuadrícula. <br/> Si está en una lista, se desplaza al subcampo relacionado anterior.                                                                                                                          |
| **Flecha derecha**                                                                         | Moverse a la derecha                  | Mueve la entidad seleccionada a la derecha un incremento de la cuadrícula. <br/> Si se encuentra en una lista, se desplaza al siguiente subcampo relacionado.                                                                                                                             |
| **Mayús + Flecha izquierda**                                                                  | Tamaño de la forma izquierda             | Reduce el ancho de la entidad seleccionada en un incremento de la cuadrícula.                                                                                                                                                                     |
| **Mayús + Flecha derecha**                                                                 | Tamaño de la forma derecha            | Aumenta el ancho de la entidad seleccionada en un incremento de la cuadrícula.                                                                                                                                                                   |
| **Página principal**                                                                                | Primer elemento del mismo nivel                  | Mueve el foco y la selección al primer objeto en la superficie de diseño en el mismo nivel del mismo nivel.                                                                                                                                         |
| **Fin**                                                                                 | Último elemento del mismo nivel                   | Mueve el foco y la selección al último objeto en la superficie de diseño en el mismo nivel del mismo nivel.                                                                                                                                          |
| **CTRL+Inicio**                                                                         | Primer elemento del mismo nivel (foco)          | Igual que el primer elemento del mismo nivel, pero mueve el foco en lugar de mover el foco y la selección.                                                                                                                                                          |
| **CTRL+Fin**                                                                          | Último elemento del mismo nivel (foco)           | Igual que el último elemento del mismo nivel, pero mueve el foco en lugar de mover el foco y la selección.                                                                                                                                                           |
| **Pestaña**                                                                                 | Siguiente elemento del mismo nivel                   | Mueve el foco y la selección al siguiente objeto en la superficie de diseño en el mismo nivel del mismo nivel.                                                                                                                                          |
| **Mayús+Tab**                                                                           | Anterior del mismo nivel               | Mueve el foco y la selección al objeto anterior en la superficie de diseño en el mismo nivel del mismo nivel.                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | Siguiente elemento del mismo nivel (foco)           | Igual que el siguiente elemento del mismo nivel, pero mueve el foco en lugar de mover el foco y la selección.                                                                                                                                                           |
| **Alt + Ctrl + Mayús + Tab**                                                                  | Anterior del mismo nivel (enfoque)       | Igual que el elemento anterior del mismo nivel, pero mueve el foco en lugar de mover el foco y la selección.                                                                                                                                                       |
| **&lt;**                                                                                | Ascende                      | Se desplaza al siguiente objeto en la superficie de diseño un nivel superior de la jerarquía. Si no hay formas por encima de esta forma en la jerarquía (es decir, el objeto se coloca directamente en la superficie de diseño), se selecciona el diagrama. |
| **&gt;**                                                                                | Descendientes                     | Se desplaza al siguiente objeto contenido en la superficie de diseño un nivel por debajo de este en la jerarquía. Si no hay ningún objeto contenido, se trata de una operación no operativa.                                                                              |
| **Ctrl + &lt;**                                                                         | Ascend (Focus)              | Igual que el comando Ascend, pero mueve el foco sin selección.                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         | Descendente (foco)             | Igual que el comando descendente, pero mueve el foco sin selección.                                                                                                                                                                         |
| **Mayús + fin**                                                                         | Seguir a conectado         | Desde una entidad, se mueve a una entidad a la que está conectada esta entidad.                                                                                                                                                               |
| **Resguardo**                                                                                 | Eliminar                      | Elimine un objeto o un conector del diagrama.                                                                                                                                                                                     |
| **Complemento**                                                                                 | Insertar                      | Agrega una nueva propiedad a una entidad cuando se selecciona el encabezado de compartimiento "propiedades escalares" o una propiedad en sí.                                                                                                           |
| **AvPág**                                                                               | Desplazar diagrama hacia arriba           | Desplaza la superficie de diseño hacia arriba, en incrementos iguales al 75% del alto de la superficie de diseño actualmente visible.                                                                                                                    |
| **Av Pág**                                                                             | Desplazar diagrama hacia abajo         | Desplaza la superficie de diseño hacia abajo.                                                                                                                                                                                                    |
| **Mayús + Av Pág**                                                                     | Desplazar diagrama a la derecha        | Desplaza la superficie de diseño a la derecha.                                                                                                                                                                                            |
| **Mayús + re pág**                                                                       | Desplazar diagrama a la izquierda         | Desplaza la superficie de diseño a la izquierda.                                                                                                                                                                                             |
| **F2**                                                                                  | Acceso al modo de edición             | Método abreviado de teclado estándar para entrar en el modo de edición de un control de texto.                                                                                                                                                               |
| **Mayús + F10**                                                                         | Mostrar menú contextual       | Método abreviado de teclado estándar para mostrar el menú contextual de un elemento seleccionado.                                                                                                                                                          |
| **Control + Mayús + clic con el botón primario del mouse**  <br/> **Control + Mayús + MouseWheel hacia delante**  | Zoom semántico            | Acerca el área de la vista de diagrama debajo del puntero del mouse.                                                                                                                                                                 |
| **Control + Mayús + clic con el botón secundario del mouse** <br/> **Control + Mayús + MouseWheel hacia atrás** | Alejar semántica           | Aleja el área de la vista de diagrama debajo del puntero del mouse. Vuelve a centrar el diagrama cuando aleja el centro del diagrama actual.                                                                          |
| **Control + Mayús + ' + '** <br/> **Control + MouseWheel Forward**                        | Acercar                     | Acerca el centro de la vista de diagrama.                                                                                                                                                                                         |
| **Control + Mayús + '-'** <br/> **Control + MouseWheel hacia atrás**                       | Alejar                    | Aleja el área en la que se hace clic en la vista de diagrama. Vuelve a centrar el diagrama cuando aleja el centro del diagrama actual.                                                                                            |
| **Control + Mayús + dibujar un rectángulo con el botón primario del mouse hacia abajo**                  | Área de zoom                   | Acerca centrada en el área que ha seleccionado. Cuando mantenga presionadas las teclas Control + Mayús, verá que el cursor cambia a una lupa, lo que le permite definir el área de zoom.                        |
| **Tecla del menú contextual + m '**                                                              | Abrir la ventana detalles de la asignación | Abre la ventana detalles de la asignación para editar las asignaciones de la entidad seleccionada                                                                                                                                                               |

## <a name="mapping-details-window"></a>Detalles de la asignación (ventana)

![Métodos abreviados de detalles de asignación](~/ef6/media/mappingdetailsshortcuts.png)

| Acceso directo                  | Acción         | Notas                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **Pestaña**                   | Cambiar contexto | Cambia entre el área de la ventana principal y la barra de herramientas de la izquierda.                                                                     |
| **Teclas de dirección**            | Navegación     | Subir y bajar filas, o de derecha a izquierda entre las columnas del área de la ventana principal. Desplácese entre los botones de la barra de herramientas de la izquierda. |
| **Entrar** <br/> **Espacia** | Seleccionar         | Selecciona un botón de la barra de herramientas de la izquierda.                                                                                          |
| **Alt + flecha abajo**      | Abrir lista      | Desplegar una lista si se selecciona una celda que tenga una lista desplegable.                                                                     |
| **Entrar**                 | Seleccionar lista    | Selecciona un elemento de una lista desplegable.                                                                                               |
| **Esc**                   | Cerrar lista     | Cierra una lista desplegable.                                                                                                              |

## <a name="visual-studio-navigation"></a>Navegación de Visual Studio

Entity Framework también proporciona una serie de acciones que pueden tener los métodos abreviados de teclado personalizados asignados (no se asignan accesos directos de forma predeterminada). Para crear estos métodos abreviados personalizados, haga clic en el menú herramientas y, a continuación, en opciones.En entorno, elija teclado.Desplácese hacia abajo en la lista en el centro hasta que pueda seleccionar el comando que desee, escriba el acceso directo en el cuadro de texto "presionar teclas de método abreviado" y haga clic en asignar. Los métodos abreviados posibles son los siguientes:

| Acceso directo                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Add. ComplexProperty. ComplexTypes**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddCodeGenerationItem**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddFunctionImport**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. AddEnumType**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. Association**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ComplexProperty**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ComplexType**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. Entity**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. FunctionImport**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. inheritance**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. NavigationProperty**               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ScalarProperty**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNewDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddtoDiagram**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Close**                                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Collapse**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ConverttoEnum**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. diagram. CollapseAll**                     |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. diagram. ExpandAll**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. diagram. ExportasImage**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. diagram. LayoutDiagram**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Edit**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. EntityKey**                               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Expand**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. FunctionImportMapping**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GenerateDatabasefromModel**               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GoToDefinition**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Grid. ShowGrid**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Grid. SnaptoGrid**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.IncludeRelated**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MappingDetails**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ModelBrowser**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveDiagramstoSeparateFile**              |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down5**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToBottom**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToTop**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up5**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MovetonewDiagram**                        |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Open**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. refactorizar. MovetoNewComplexType**           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.Rename**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.RemovefromDiagram**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Rename**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ScalarPropertyFormat. DisplayName**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. BaseType**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. Entity**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. Property**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. SubType**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. SelectAll**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAssociation**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinModelBrowser**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.StoredProcedureMapping**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. TableMapping**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.UpdateModelfromDatabase**                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Validate**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 10**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 100**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 125**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 150**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 200**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 25**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 300**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 33**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 400**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 50**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 66**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 75**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. Custom**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. zoom**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. ZoomOut**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. ZoomtoFit**                          |
| **View.EntityDataModelBrowser**                                                                |
| **View.EntityDataModelMappingDetails**                                                         |
