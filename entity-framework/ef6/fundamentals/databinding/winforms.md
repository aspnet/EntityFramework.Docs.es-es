---
title: DataBinding con WinForms-EF6
description: DataBinding con WinForms en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/databinding/winforms
ms.openlocfilehash: b316aea0a36edc8586a81427825ed4ee11fc9ef3
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073232"
---
# <a name="databinding-with-winforms"></a>Databinding with WinForms (Enlace de datos con WinForms)
En este tutorial paso a paso se muestra cómo enlazar tipos POCO a controles de formularios Windows Forms (WinForms) en un formulario "principal-detalle". La aplicación utiliza Entity Framework para rellenar los objetos con datos de la base de datos, realizar un seguimiento de los cambios y conservar los datos en la base de datos.

El modelo define dos tipos que participan en una relación de uno a varios: categoría ( \\ maestro principal) y producto ( \\ detalle dependiente). A continuación, se usan las herramientas de Visual Studio para enlazar los tipos definidos en el modelo con los controles de WinForms. El marco de enlace de datos de WinForms permite la navegación entre objetos relacionados: la selección de filas en la vista maestra hace que la vista de detalles se actualice con los datos secundarios correspondientes.

Las capturas de pantalla y las listas de código de este tutorial se han tomado de Visual Studio 2013 pero puede completar este tutorial con Visual Studio 2012 o Visual Studio 2010.

## <a name="pre-requisites"></a>Requisitos previos

Debe tener Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 instalado para completar este tutorial.

Si usa Visual Studio 2010, también tiene que instalar NuGet. Para obtener más información, consulte [instalación de NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="create-the-application"></a>Crear la aplicación

-   Apertura de Visual Studio
-   **Archivo- &gt; nuevo- &gt; proyecto....**
-   Seleccione **Windows** en el panel izquierdo y **Windows FormsApplication** en el panel derecho.
-   Escriba **WinFormswithEFSample** como nombre
-   Seleccione **Aceptar**.

## <a name="install-the-entity-framework-nuget-package"></a>Instalar el paquete NuGet de Entity Framework

-   En Explorador de soluciones, haga clic con el botón derecho en el proyecto **WinFormswithEFSample**
-   Seleccione **administrar paquetes NuGet..** .
-   En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework**
-   Haz clic en **Instalar**  
    > [!NOTE]
    > Además del ensamblado EntityFramework, también se agrega una referencia a System. ComponentModel. DataAnnotations. Si el proyecto tiene una referencia a System. Data. Entity, se quitará cuando se instale el paquete EntityFramework. El ensamblado System. Data. Entity ya no se utiliza para las aplicaciones de Entity Framework 6.

## <a name="implementing-ilistsource-for-collections"></a>Implementación de IListSource para colecciones

Las propiedades de colección deben implementar la interfaz IListSource para habilitar el enlace de datos bidireccional con la ordenación al utilizar Windows Forms. Para ello, vamos a ampliar ObservableCollection para agregar la funcionalidad IListSource.

-   Agregue una clase **ObservableListSource** al proyecto:
    -   Haga clic con el botón derecho en el nombre del proyecto
    -   Seleccionar **agregar- &gt; nuevo elemento**
    -   Seleccione **clase** y escriba **ObservableListSource** para el nombre de clase.
-   Reemplace el código generado de forma predeterminada con el código siguiente:

*Esta clase permite el enlace de datos bidireccional y la ordenación. La clase se deriva de ObservableCollection &lt; T &gt; y agrega una implementación explícita de IListSource. El método GetList () de IListSource se implementa para devolver una implementación de IBindingList que permanece sincronizada con ObservableCollection. La implementación de IBindingList generada por ToBindingList admite la ordenación. El método de extensión ToBindingList se define en el ensamblado EntityFramework.*

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a>Definición de un modelo

En este tutorial, puede elegir implementar un modelo mediante Code First o el diseñador de EF. Complete una de las dos secciones siguientes.

### <a name="option-1-define-a-model-using-code-first"></a>Opción 1: definir un modelo con Code First

En esta sección se muestra cómo crear un modelo y su base de datos asociada mediante Code First. Vaya a la sección siguiente (**opción 2: definir un modelo con Database First)** si prefiere usar Database First para aplicar ingeniería inversa al modelo desde una base de datos mediante el diseñador de EF.

Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio).

-   Agregar una nueva clase de **producto** a Project
-   Reemplace el código generado de forma predeterminada con el código siguiente:

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   Agregue una clase de **categoría** al proyecto.
-   Reemplace el código generado de forma predeterminada con el código siguiente:

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

Además de definir entidades, debe definir una clase que derive de **DbContext** y exponga propiedades de **DbSet &lt; &gt; ** . Las propiedades **DbSet** permiten que el contexto sepa qué tipos desea incluir en el modelo. Los tipos **DbContext** y **DbSet** se definen en el ensamblado EntityFramework.

Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.

-   Agregue una nueva clase **ProductContext** al proyecto.
-   Reemplace el código generado de forma predeterminada con el código siguiente:

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

Compile el proyecto.

### <a name="option-2-define-a-model-using-database-first"></a>Opción 2: definir un modelo con Database First

En esta sección se muestra cómo usar Database First para aplicar ingeniería inversa al modelo desde una base de datos mediante el diseñador de EF. Si ha completado la sección anterior (**opción 1: definir un modelo con Code First)**, omita esta sección y vaya directamente a la sección de **carga diferida** .

#### <a name="create-an-existing-database"></a>Crear una base de datos existente

Normalmente, cuando el destino es una base de datos existente, ya se creará, pero para este tutorial es necesario crear una base de datos para tener acceso a.

El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:

-   Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.
-   Si usa Visual Studio 2012, va a crear una base de datos de [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) .

Vamos a generar la base de datos.

-   **Vista &gt; Explorador de servidores**
-   Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**
-   Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar Microsoft SQL Server como origen de datos

    ![Cambiar origen de datos](~/ef6/media/changedatasource.png)

-   Conéctese a LocalDB o a SQL Express, en función de la que haya instalado y escriba **Products** como nombre de la base de datos.

    ![Agregar conexión LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![Agregar conexión Express](~/ef6/media/addconnectionexpress.png)

-   Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .

    ![Creación de una base de datos](~/ef6/media/createdatabase.png)

-   La nueva base de datos aparecerá ahora en Explorador de servidores, haga clic con el botón derecho en ella y seleccione **nueva consulta** .
-   Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a>Modelo de ingeniería inversa

Vamos a hacer uso de Entity Framework Designer, que se incluye como parte de Visual Studio, para crear nuestro modelo.

-   **Proyecto- &gt; Agregar nuevo elemento...**
-   Seleccione **datos** en el menú de la izquierda y, a continuación, **ADO.NET Entity Data Model**
-   Escriba **ProductModel** como nombre y haga clic en **Aceptar** .
-   Se iniciará el **Asistente para Entity Data Model**
-   Seleccione **generar desde la base de datos** y haga clic en **siguiente** .

    ![Elegir contenido de Model](~/ef6/media/choosemodelcontents.png)

-   Seleccione la conexión a la base de datos creada en la primera sección, escriba **ProductContext** como nombre de la cadena de conexión y haga clic en **siguiente** .

    ![Elegir la conexión](~/ef6/media/chooseyourconnection.png)

-   Haga clic en la casilla situada junto a "tablas" para importar todas las tablas y haga clic en "finalizar".

    ![Elección de los objetos](~/ef6/media/chooseyourobjects.png)

Una vez que se completa el proceso de ingeniería inversa, el nuevo modelo se agrega al proyecto y se abre para que pueda verlo en el Entity Framework Designer. También se ha agregado un archivo App.config al proyecto con los detalles de conexión de la base de datos.

#### <a name="additional-steps-in-visual-studio-2010"></a>Pasos adicionales en Visual Studio 2010

Si está trabajando en Visual Studio 2010, tendrá que actualizar el diseñador de EF para usar la generación de código EF6.

-   Haga clic con el botón derecho en una zona vacía del modelo en el diseñador de EF y seleccione **Agregar elemento de generación de código..** .
-   Seleccione **plantillas en línea** en el menú de la izquierda y busque **DbContext**
-   Seleccione el **generador de DbContext de EF 6. x para C \# ,** escriba **ProductsModel** como nombre y haga clic en Agregar.

#### <a name="updating-code-generation-for-data-binding"></a>Actualizar la generación de código para el enlace de datos

EF genera código a partir del modelo mediante plantillas T4. Las plantillas que se incluyen con Visual Studio o que se descargan de la galería de Visual Studio están pensadas para uso general. Esto significa que las entidades generadas a partir de estas plantillas tienen &lt; propiedades ICollection T simples &gt; . Sin embargo, cuando se realiza el enlace de datos, es aconsejable tener propiedades de colección que implementen IListSource. Este es el motivo por el que hemos creado la clase ObservableListSource anterior y ahora vamos a modificar las plantillas para hacer uso de esta clase.

-   Abra el **Explorador de soluciones** y busque el archivo **ProductModel. edmx.**
-   Busque el archivo **ProductModel.TT** que se anidará en el archivo ProductModel. edmx.

    ![Plantilla de modelo de producto](~/ef6/media/productmodeltemplate.png)

-   Haga doble clic en el archivo ProductModel.tt para abrirlo en el editor de Visual Studio
-   Busque y reemplace las dos apariciones de "**ICollection**" por "**ObservableListSource**". Se encuentran en aproximadamente las líneas 296 y 484.
-   Busque y reemplace la primera aparición de "**HashSet**" por "**ObservableListSource**". Esta repetición se encuentra en aproximadamente la línea 50. **No** Reemplace la segunda aparición de HashSet que se encuentra más adelante en el código.
-   Guarde el archivo ProductModel.tt. Esto debería hacer que se vuelva a generar el código para las entidades. Si el código no se vuelve a generar automáticamente, haga clic con el botón derecho en ProductModel.tt y elija "ejecutar herramienta personalizada".

Si ahora abre el archivo Category.cs (que está anidado en ProductModel.tt), debería ver que la colección Products tiene el tipo **ObservableListSource &lt; Product &gt; **.

Compile el proyecto.

## <a name="lazy-loading"></a>Carga diferida

La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación. En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.

EF ofrece una opción para cargar automáticamente entidades relacionadas desde la base de datos la primera vez que se tiene acceso a la propiedad de navegación. Con este tipo de carga (denominado carga diferida), tenga en cuenta que la primera vez que se accede a cada propiedad de navegación se ejecutará una consulta independiente en la base de datos si el contenido no está ya en el contexto.

Cuando se usan tipos de entidad POCO, EF consigue la carga diferida mediante la creación de instancias de tipos de proxy derivados durante el tiempo de ejecución y, a continuación, la invalidación de las propiedades virtuales en las clases para agregar el enlace de carga. Para obtener la carga diferida de los objetos relacionados, debe declarar captadores de propiedades de navegación como **Public** y **virtual** (**reemplazable** en Visual Basic) y la clase no debe estar **sellada** (**NotOverridable** en Visual Basic). Cuando se usa Database First propiedades de navegación se convierten automáticamente en virtual para habilitar la carga diferida. En la sección Code First hemos optado por que las propiedades de navegación sean virtuales por la misma razón

## <a name="bind-object-to-controls"></a>Enlace de objetos a controles

Agregue las clases que se definen en el modelo como orígenes de datos para esta aplicación de WinForms.

-   En el menú principal, seleccione **proyecto- &gt; Agregar nuevo origen de datos.** ..
    (en Visual Studio 2010, debe seleccionar **datos- &gt; Agregar nuevo origen de datos.**..)
-   En la ventana elegir un tipo de origen de datos, seleccione **objeto** y haga clic en **siguiente** .
-   En el cuadro de diálogo Seleccionar los objetos de datos, **WinFormswithEFSample** dos veces y seleccione **categoría** no es necesario seleccionar el origen de datos del producto, porque lo haremos a través de la propiedad del producto en el origen de datos de la categoría.

    ![Origen de datos](~/ef6/media/datasource.png)

-   Haga clic en **Finish** (Finalizar).
    Si la ventana orígenes de datos no aparece, seleccione **Ver- &gt; otras ventanas-otros &gt; orígenes de datos** .
-   Presione el icono de anclaje para que la ventana orígenes de datos no se oculte automáticamente. Es posible que tenga que presionar el botón actualizar si la ventana ya estaba visible.

    ![Origen de datos 2](~/ef6/media/datasource2.png)

-   En Explorador de soluciones, haga doble clic en el archivo **Form1.CS** para abrir el formulario principal en el diseñador.
-   Seleccione el origen de datos de la **categoría** y arrástrelo en el formulario. De forma predeterminada, se agregan al diseñador un nuevo control DataGridView (**categoryDataGridView**) y controles de la barra de herramientas de navegación. Estos controles están enlazados a los componentes BindingSource (**categoryBindingSource**) y navegador de enlace (**categoryBindingNavigator**) que también se crean.
-   Edite las columnas en el **categoryDataGridView**. Queremos establecer la columna **CategoryID** en solo lectura. La base de datos genera el valor de la propiedad **CategoryID** después de guardar los datos.
    -   Haga clic con el botón secundario en el control DataGridView y seleccione Editar columnas...
    -   Seleccione la columna CategoryId y establezca ReadOnly en true.
    -   Presione Aceptar
-   Seleccione productos en el origen de datos de la categoría y arrástrelos en el formulario. ProductDataGridView y productBindingSource se agregan al formulario.
-   Edite las columnas en el productDataGridView. Queremos ocultar las columnas CategoryId y categoría y establecer ProductId en solo lectura. La base de datos genera el valor de la propiedad ProductId después de guardar los datos.
    -   Haga clic con el botón secundario en el control DataGridView y seleccione **Editar columnas.**...
    -   Seleccione la columna **ProductID** y establezca **ReadOnly** en **true**.
    -   Seleccione la columna **CategoryID** y presione el botón **quitar** . Haga lo mismo con la columna **categoría** .
    -   Haga clic en **Aceptar**.

    Hasta ahora, asociamos nuestros controles DataGridView a componentes BindingSource en el diseñador. En la siguiente sección, agregaremos código al código subyacente para establecer categoryBindingSource. DataSource en la colección de entidades cuyo seguimiento realiza actualmente DbContext. Cuando hemos arrastrado y colocado productos desde la categoría, el WinForms se encarga de configurar la propiedad productsBindingSource. DataSource en categoryBindingSource y la propiedad productsBindingSource. DataMember en Products. Debido a este enlace, solo se mostrarán en el productDataGridView los productos que pertenecen a la categoría seleccionada actualmente.
-   Habilite el botón **Guardar** en la barra de herramientas de navegación; para ello, haga clic con el botón secundario del mouse y seleccione **habilitado**.

    ![Diseñador de formulario 1](~/ef6/media/form1-designer.png)

-   Agregue el controlador de eventos para el botón Guardar haciendo doble clic en el botón. Esto agregará el controlador de eventos y le llevará al código subyacente para el formulario. El código del controlador de eventos ** \_ click de categoryBindingNavigatorSaveItem** se agregará en la sección siguiente.

## <a name="add-the-code-that-handles-data-interaction"></a>Agregar el código que controla la interacción de datos

Ahora agregaremos el código para usar el ProductContext para realizar el acceso a los datos. Actualice el código para la ventana de formulario principal, como se muestra a continuación.

El código declara una instancia de ejecución prolongada de ProductContext. El objeto ProductContext se usa para consultar y guardar datos en la base de datos. A continuación, se llama al método Dispose () en la instancia de ProductContext desde el método de cierre de sesión reemplazado. Los comentarios de código proporcionan detalles sobre lo que hace el código.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a>Prueba de la aplicación Windows Forms

-   Compile y ejecute la aplicación y puede probar la funcionalidad.

    ![Formulario 1 antes de guardar](~/ef6/media/form1beforesave.png)

-   Después de guardar las claves generadas por el almacén, se muestran en la pantalla.

    ![Formulario 1 después de guardar](~/ef6/media/form1aftersave.png)

-   Si ha usado Code First, también verá que se ha creado una base de datos **WinFormswithEFSample. ProductContext** .

    ![Explorador de objetos de servidor](~/ef6/media/serverobjexplorer.png)
