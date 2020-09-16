---
title: 'Introducción a WPF: EF Core'
description: Tutorial de introducción al uso de WPF con Entity Framework Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 1198da5c9564663ca26392b33462c727275a432d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619305"
---
# <a name="getting-started-with-wpf"></a>Introducción a WPF

En este tutorial paso a paso se muestra cómo enlazar tipos POCO a controles de WPF en un formulario "main-detail". La aplicación utiliza las API de Entity Framework para rellenar los objetos con datos de la base de datos, realizar un seguimiento de los cambios y conservar los datos en la base de datos.

El modelo define dos tipos que participan en una relación de uno a varios: **Category** (principal\\main) y **Product** (dependent\\detail). El marco de enlace de datos de WPF permite la navegación entre objetos relacionados: la selección de filas en la vista maestra hace que la vista de detalles se actualice con los datos secundarios correspondientes.

Las capturas de pantalla y las listas de código de este tutorial se han tomado de Visual Studio 2019 16.6.5.

> [!TIP]
> Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF) de este artículo.

## <a name="pre-requisites"></a>Requisitos previos

* Debe tener Visual Studio 2019 16.3 o posterior instalado con la **carga de trabajo de escritorio de .NET** seleccionada para completar este tutorial.
    
    Para obtener más información sobre cómo instalar la última versión de Visual Studio, vea [Instalación de Visual Studio](/visualstudio/install/install-visual-studio).

## <a name="create-the-application"></a>Crear la aplicación

1. Apertura de Visual Studio
2. En la ventana de inicio, elija **Crear proyecto**.
3. Busque "WPF", elija **Aplicación WPF (.NET Core)** y después seleccione **Siguiente**.
4. En la pantalla siguiente, asigne un nombre al proyecto, por ejemplo, **GetStartedWPF**, y elija **Crear**.

## <a name="install-the-entity-framework-nuget-packages"></a>Instalación de los paquetes de NuGet Entity Framework

1. Haga clic con el botón derecho en la solución y elija **Administrar paquetes NuGet para la solución...**

    ![Administración de paquetes de NuGet](_static/wpf-tutorial-nuget.jpg)

1. Escriba `entityframeworkcore.sqlite` en el cuadro de búsqueda.
1. Instale el paquete **Microsoft.EntityFrameworkCore.Sqlite**.
1. Compruebe el proyecto en el panel derecho y haga clic en **Instalar**.

    ![Paquete de Sqlite](_static/wpf-tutorial-sqlite.jpg)

1. Repita los pasos para buscar `entityframeworkcore.proxies` e instalar **Microsoft.EntityFrameworkCore.Proxies**.

> [!NOTE]
> Al instalar el paquete de Sqlite, se extrae automáticamente el paquete de base **Microsoft.EntityFrameworkCore** relacionado. El paquete **Microsoft.EntityFrameworkCore.Proxies** proporciona compatibilidad con los datos de "carga diferida". Esto significa que, si tiene entidades con entidades secundarias, solo se capturan los elementos primarios en la carga inicial. Los proxies detectan cuándo se produce un intento de acceso a las entidades secundarias, y las cargan automáticamente a petición. 

## <a name="define-a-model"></a>Definición de un modelo

En este tutorial, implementará un modelo con "Code First". Esto significa que EF Core creará las tablas de base de datos y el esquema en función de las clases de C# que defina.

Agregue una nueva clase. Asígnele el nombre `Product.cs` y rellénelo como se indica a continuación:

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

A continuación, agregue una clase denominada `Category.cs` y rellénela con el código siguiente:

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación. En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.

Además de definir entidades, debe definir una clase que derive de DbContext y exponga las propiedades DbSet&lt;TEntity&gt;. Las propiedades DbSet&lt;TEntity&gt; permiten que el contexto sepa qué tipos desea incluir en el modelo.

Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.

Agregue una nueva clase `ProductContext.cs` al proyecto con la siguiente definición:

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet` informa a EF Core de qué entidades de C# se deben asignar a la base de datos.
* Hay varias maneras de configurar `DbContext` de EF Core. Puede leer sobre ellas en: [Configuración de un DbContext](xref:core/miscellaneous/configuring-dbcontext).
* En este ejemplo se usa la invalidación `OnConfiguring` para especificar un archivo de datos de Sqlite.
* La llamada a `UseLazyLoadingProxies` indica a EF Core que implemente la carga diferida, por lo que las entidades secundarias se cargan automáticamente cuando se obtiene acceso a ellas desde el elemento primario.

Presione **CTRL + MAYÚS + B** o vaya a **Compilar &gt; Compilar solución** para compilar el proyecto.

> [!TIP]
> Obtenga información sobre los distintos pasos para mantener sincronizados la base de datos y los modelos de EF Core: [Administración de esquemas de base de datos](xref:core/managing-schemas/index).

## <a name="lazy-loading"></a>Carga diferida

La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación. En Entity Framework Core, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.

EF Core ofrece una opción para cargar automáticamente las entidades relacionadas desde la base de datos la primera vez que se accede a la propiedad de navegación. Con este tipo de carga (denominado carga diferida), tenga en cuenta que la primera vez que se accede a cada propiedad de navegación se ejecutará una consulta independiente en la base de datos si el contenido no está ya en el contexto.

Al usar tipos de entidad "Plain Old C# Object" (POCO), EF Core logra la carga diferida mediante la creación de instancias de tipos de proxy derivados durante el tiempo de ejecución y, después, la invalidación de las propiedades virtuales de las clases para agregar el enlace de carga. Para obtener la carga diferida de los objetos relacionados, debe declarar captadores de propiedades de navegación como **público** y **virtual** (**Overridable** en Visual Basic), y la clase no debe **sellarse** (**NotOverridable** en Visual Basic). Al usar Database First, las propiedades de navegación se convierten automáticamente en virtuales para habilitar la carga diferida. 

## <a name="bind-object-to-controls"></a>Enlace de objetos a controles

Agregue las clases que se definen en el modelo como orígenes de datos para esta aplicación WPF.

1. Haga doble clic en **MainWindow.xaml** en el Explorador de soluciones para abrir el formulario principal.
1. Elija la pestaña **XAML** para editar el código XAML.
1. Inmediatamente después de la etiqueta de apertura `Window`, agregue los orígenes siguientes para conectarse a las entidades de EF Core.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. De esta forma, se configura el origen para las categorías "principales" y el segundo origen para los productos "detallados".
1. A continuación, agregue el marcado siguiente al código XAML después de la etiqueta de cierre `Window.Resources`.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. Tenga en cuenta que el elemento `CategoryId` se establece en `ReadOnly` porque está asignado por la base de datos y no se puede cambiar.

## <a name="adding-a-details-grid"></a>Adición de una cuadrícula de detalles

Ahora que la cuadrícula existe para mostrar las categorías, se puede agregar la cuadrícula de detalles para mostrar los productos.

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

Por último, agregue un botón `Save` y una conexión en el evento de clic a `Button_Click`.

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

La vista de diseño debería tener un aspecto similar a este:

![Captura de pantalla de WPF Designer](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>Adición de código que controla la interacción con los datos

Es el momento de agregar algunos controladores de eventos a la ventana principal.

1. En la ventana de XAML, haga clic en el elemento **&lt;Ventana&gt;** , para seleccionar la ventana principal.
1. En la ventana **Propiedades**, elija **Eventos** en la parte superior derecha y, a continuación, haga doble clic en el cuadro de texto que se encuentra a la derecha de la etiqueta **Cargado**.

    ![Propiedades de la ventana principal](_static/wpf-tutorial-loaded.jpg)

Esto lo lleva al código subyacente del formulario; ahora, se editará el código para usar el elemento `ProductContext` para ejecutar el acceso a los datos. Actualice el código como se muestra a continuación.

El código declara una instancia de larga duración de `ProductContext`. El objeto `ProductContext` se utiliza para consultar y guardar datos en la base de datos. A continuación, se llama al método `Dispose()` en la instancia de `ProductContext` desde el método `OnClosing` invalidado.Los comentarios del código explican qué hace cada paso.

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> El código usa una llamada a `EnsureCreated()` para compilar la base de datos en la primera ejecución. Esto es aceptable para demostraciones, pero en las aplicaciones de producción debe examinar [migraciones](xref:core/managing-schemas/migrations/index) para administrar el esquema. El código también se ejecuta de forma sincrónica porque usa una base de datos SQLite local. En escenarios de producción que normalmente implican un servidor remoto, considere la posibilidad de utilizar las versiones asincrónicas de los métodos `Load` y `SaveChanges`.

## <a name="test-the-wpf-application"></a>Prueba de una aplicación WPF

Para compilar y ejecutar la aplicación, presione **F5** o seleccione **Depurar &gt; Iniciar depuración**. La base de datos debe crearse automáticamente con un archivo denominado `products.db`. Escriba un nombre de categoría y presione ENTRAR y, a continuación, agregue productos a la cuadrícula inferior. Haga clic en Guardar y observe la actualización de la cuadrícula con los identificadores proporcionados por la base de datos. Resalte una fila y presione **Eliminar** para quitarla. La entidad se eliminará al hacer clic en **Guardar**.

![Aplicación en ejecución](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>Notificación de cambio de propiedad

Este ejemplo se basa en cuatro pasos para sincronizar las entidades con la interfaz de usuario.

1. La llamada inicial a `_context.Categories.Load()` carga los datos de categorías.
1. Los proxies de carga diferida cargan los datos de los productos dependientes.
1. El seguimiento de cambios integrado de EF Core realiza las modificaciones necesarias en las entidades, incluidas las inserciones y eliminaciones, cuando se llama a `_context.SaveChanges()`.
1. Las llamadas a `DataGridView.Items.Refresh()` fuerzan una recarga con los identificadores recién generados.

Esto sirve para el ejemplo de introducción, pero puede que necesite código adicional para otros escenarios. Los controles de WPF representan la interfaz de usuario mediante la lectura de los campos y las propiedades de las entidades. Cuando se edita un valor en la interfaz de usuario (UI), ese valor se pasa a la entidad. Al cambiar el valor de una propiedad directamente en la entidad, como cargarla desde la base de datos, WPF no reflejará inmediatamente los cambios en la interfaz de usuario. El motor de representación debe recibir notificaciones de los cambios. El proyecto lo hizo llamando manualmente a `Refresh()`. Una manera sencilla de automatizar esta notificación es mediante la implementación de la interfaz [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged). Los componentes de WPF detectarán automáticamente la interfaz y se registrarán para los eventos de cambio. La entidad es responsable de generar estos eventos.

> [!TIP]
> Para obtener más información sobre cómo administrar los cambios, lea: [Implementación de la notificación de cambio de propiedad](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [Configuración de un DbContext](xref:core/miscellaneous/configuring-dbcontext).
