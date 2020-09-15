---
title: Enlace de enlaces con WPF-EF6
description: Enlace de enlaces con WPF en Entity Framework 6
author: divega
ms.date: 05/19/2020
uid: ef6/fundamentals/databinding/wpf
ms.openlocfilehash: 85aea308d89241f1da9633ef60f4bc8c3997561d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072985"
---
# <a name="databinding-with-wpf"></a>Databinding with WPF (Enlace de datos con WPF)

> [!IMPORTANT]
> **Este documento solo es válido para WPF en el .NET Framework**
>
> En este documento se describe el enlace de DataBindings para WPF en el .NET Framework. En el caso de los nuevos proyectos de .NET Core, se recomienda usar [EF Core](xref:core/index) en lugar de Entity Framework 6. La documentación de DataBinding en EF Core está aquí: [Introducción con WPF](xref:core/get-started/wpf).

En este tutorial paso a paso se muestra cómo enlazar tipos POCO a controles de WPF en un formulario "maestro-detalles". La aplicación utiliza las API de Entity Framework para rellenar los objetos con datos de la base de datos, realizar un seguimiento de los cambios y conservar los datos en la base de datos.

El modelo define dos tipos que participan en una relación de uno a varios: **categoría** ( \\ maestro principal) y **producto** ( \\ detalle dependiente). A continuación, se usan las herramientas de Visual Studio para enlazar los tipos definidos en el modelo a los controles de WPF. El marco de enlace de datos de WPF permite la navegación entre objetos relacionados: la selección de filas en la vista maestra hace que la vista de detalles se actualice con los datos secundarios correspondientes.

Las capturas de pantalla y las listas de código de este tutorial se han tomado de Visual Studio 2013 pero puede completar este tutorial con Visual Studio 2012 o Visual Studio 2010.

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>Usar la opción ' Object ' para crear orígenes de datos de WPF

Con la versión anterior de Entity Framework usamos para recomendar el uso de la opción de **base** de datos al crear un nuevo origen de datos basado en un modelo creado con el diseñador de EF. Esto se debe a que el diseñador genera un contexto que se deriva de las clases de ObjectContext y de entidad derivadas de EntityObject. El uso de la opción de base de datos le ayudará a escribir el mejor código para interactuar con esta superficie de API.

Los diseñadores de EF para Visual Studio 2012 y Visual Studio 2013 generan un contexto que se deriva de DbContext junto con clases de entidad POCO simples. Con Visual Studio 2010, se recomienda cambiar a una plantilla de generación de código que use DbContext, tal y como se describe más adelante en este tutorial.

Al usar la superficie de API de DbContext, debe usar la opción de **objeto** al crear un nuevo origen de datos, como se muestra en este tutorial.

Si es necesario, puede [revertir a la generación de código basada en ObjectContext](xref:ef6/modeling/designer/codegen/legacy-objectcontext) para los modelos creados con el diseñador de EF.

## <a name="pre-requisites"></a>Requisitos previos

Debe tener Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 instalado para completar este tutorial.

Si usa Visual Studio 2010, también tiene que instalar NuGet. Para obtener más información, consulte [instalación de NuGet](/nuget/install-nuget-client-tools).  

## <a name="create-the-application"></a>Crear la aplicación

- Apertura de Visual Studio
- **Archivo- &gt; nuevo- &gt; proyecto....**
- Seleccione **ventanas**   en el panel izquierdo y **WPFApplication** en el panel derecho.
- Escriba **WPFwithEFSample**   como nombre
- Seleccione **Aceptar** .

## <a name="install-the-entity-framework-nuget-package"></a>Instalar el paquete NuGet de Entity Framework

- En Explorador de soluciones, haga clic con el botón derecho en el proyecto **WinFormswithEFSample**
- Seleccione **administrar paquetes NuGet..** .
- En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework**
- Haz clic en **Instalar**  
    >[!NOTE]
    > Además del ensamblado EntityFramework, también se agrega una referencia a System. ComponentModel. DataAnnotations. Si el proyecto tiene una referencia a System. Data. Entity, se quitará cuando se instale el paquete EntityFramework. El ensamblado System. Data. Entity ya no se utiliza para las aplicaciones de Entity Framework 6.

## <a name="define-a-model"></a>Definición de un modelo

En este tutorial, puede elegir implementar un modelo mediante Code First o el diseñador de EF. Complete una de las dos secciones siguientes.

### <a name="option-1-define-a-model-using-code-first"></a>Opción 1: definir un modelo con Code First

En esta sección se muestra cómo crear un modelo y su base de datos asociada mediante Code First. Vaya a la sección siguiente (**opción 2: definir un modelo con Database First)** si prefiere usar Database First para aplicar ingeniería inversa al modelo desde una base de datos mediante el diseñador de EF.

Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio).

- Agregue una nueva clase a **WPFwithEFSample:**
  - Haga clic con el botón derecho en el nombre del proyecto
  - Seleccione **Agregar**y, a continuación, **nuevo elemento** .
  - Seleccione **clase** y escriba **Product**   como nombre de clase.
- Reemplace la definición de clase de **producto**   por el código siguiente:

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

- Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación. En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.

Además de definir entidades, debe definir una clase que derive de DbContext y exponga las propiedades DbSet&lt;TEntity&gt;. Las propiedades DbSet&lt;TEntity&gt; permiten que el contexto sepa qué tipos desea incluir en el modelo.

Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.

- Agregue una nueva clase **ProductContext** al proyecto con la siguiente definición:

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
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

- Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.
- Si usa Visual Studio 2012, va a crear una base de datos de [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) .

Vamos a generar la base de datos.

- **Vista &gt; Explorador de servidores**
- Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**
- Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar Microsoft SQL Server como origen de datos

    ![Cambiar origen de datos](~/ef6/media/changedatasource.png)

- Conéctese a LocalDB o a SQL Express, en función de la que haya instalado y escriba **Products** como nombre de la base de datos.

    ![Agregar conexión LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![Agregar conexión Express](~/ef6/media/addconnectionexpress.png)

- Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .

    ![Creación de una base de datos](~/ef6/media/createdatabase.png)

- La nueva base de datos aparecerá ahora en Explorador de servidores, haga clic con el botón derecho en ella y seleccione **nueva consulta** .
- Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .

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

- **Proyecto- &gt; Agregar nuevo elemento...**
- Seleccione **datos** en el menú de la izquierda y, a continuación, **ADO.NET Entity Data Model**
- Escriba **ProductModel** como nombre y haga clic en **Aceptar** .
- Se iniciará el **Asistente para Entity Data Model**
- Seleccione **generar desde la base de datos** y haga clic en **siguiente** .

    ![Elegir contenido de Model](~/ef6/media/choosemodelcontents.png)

- Seleccione la conexión a la base de datos creada en la primera sección, escriba **ProductContext** como nombre de la cadena de conexión y haga clic en **siguiente** .

    ![Elegir la conexión](~/ef6/media/chooseyourconnection.png)

- Haga clic en la casilla situada junto a "tablas" para importar todas las tablas y haga clic en "finalizar".

    ![Elección de los objetos](~/ef6/media/chooseyourobjects.png)

Una vez que se completa el proceso de ingeniería inversa, el nuevo modelo se agrega al proyecto y se abre para que pueda verlo en el Entity Framework Designer. También se ha agregado un archivo App.config al proyecto con los detalles de conexión de la base de datos.

#### <a name="additional-steps-in-visual-studio-2010"></a>Pasos adicionales en Visual Studio 2010

Si está trabajando en Visual Studio 2010, tendrá que actualizar el diseñador de EF para usar la generación de código EF6.

- Haga clic con el botón derecho en una zona vacía del modelo en el diseñador de EF y seleccione **Agregar elemento de generación de código..** .
- Seleccione **plantillas en línea** en el menú de la izquierda y busque **DbContext**
- Seleccione el **generador de DbContext de EF 6. x para C \# ,** escriba **ProductsModel** como nombre y haga clic en Agregar.

#### <a name="updating-code-generation-for-data-binding"></a>Actualizar la generación de código para el enlace de datos

EF genera código a partir del modelo mediante plantillas T4. Las plantillas que se incluyen con Visual Studio o que se descargan de la galería de Visual Studio están pensadas para uso general. Esto significa que las entidades generadas a partir de estas plantillas tienen &lt; propiedades ICollection T simples &gt; . Sin embargo, al realizar el enlace de datos mediante WPF, es conveniente usar **ObservableCollection** para las propiedades de colección para que WPF pueda realizar un seguimiento de los cambios realizados en las colecciones. A este fin, modificaremos las plantillas para usar ObservableCollection.

- Abra el **Explorador de soluciones** y busque el archivo **ProductModel. edmx.**
- Busque el archivo **ProductModel.TT** que se anidará en el archivo ProductModel. edmx.

    ![Plantilla de modelo de producto de WPF](~/ef6/media/wpfproductmodeltemplate.png)

- Haga doble clic en el archivo ProductModel.tt para abrirlo en el editor de Visual Studio
- Busque y reemplace las dos apariciones de "**ICollection**" por "**ObservableCollection**". Se encuentran aproximadamente en las líneas 296 y 484.
- Busque y reemplace la primera aparición de "**HashSet**" con "**ObservableCollection**". Esta repetición se encuentra aproximadamente en la línea 50. **No** Reemplace la segunda aparición de HashSet que se encuentra más adelante en el código.
- Busque y reemplace la única aparición de "**System. Collections. Generic**" por "**System. Collections. ObjectModel**". Esto se encuentra aproximadamente en la línea 424.
- Guarde el archivo ProductModel.tt. Esto debería hacer que se vuelva a generar el código para las entidades. Si el código no se vuelve a generar automáticamente, haga clic con el botón derecho en ProductModel.tt y elija "ejecutar herramienta personalizada".

Si ahora abre el archivo Category.cs (que está anidado en ProductModel.tt), debería ver que la colección Products tiene el tipo **ObservableCollection &lt; Product &gt; **.

Compile el proyecto.

## <a name="lazy-loading"></a>Carga diferida

La propiedad **Products** de la clase **Category** y la propiedad **Category** de la clase **Product** son propiedades de navegación. En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.

EF ofrece una opción para cargar automáticamente entidades relacionadas desde la base de datos la primera vez que se tiene acceso a la propiedad de navegación. Con este tipo de carga (denominado carga diferida), tenga en cuenta que la primera vez que se accede a cada propiedad de navegación se ejecutará una consulta independiente en la base de datos si el contenido no está ya en el contexto.

Cuando se usan tipos de entidad POCO, EF consigue la carga diferida mediante la creación de instancias de tipos de proxy derivados durante el tiempo de ejecución y, a continuación, la invalidación de las propiedades virtuales en las clases para agregar el enlace de carga. Para obtener la carga diferida de los objetos relacionados, debe declarar captadores de propiedades de navegación como **Public** y **virtual** (**reemplazable** en Visual Basic) y la clase no debe estar **sellada** (**NotOverridable** en Visual Basic). Cuando se usa Database First propiedades de navegación se convierten automáticamente en virtual para habilitar la carga diferida. En la sección Code First hemos optado por que las propiedades de navegación sean virtuales por la misma razón

## <a name="bind-object-to-controls"></a>Enlace de objetos a controles

Agregue las clases que se definen en el modelo como orígenes de datos para esta aplicación WPF.

- Haga doble clic en **MainWindow.xaml** en el Explorador de soluciones para abrir el formulario principal.
- En el menú principal, seleccione **proyecto- &gt; Agregar nuevo origen de datos.** ..
    (en Visual Studio 2010, debe seleccionar **datos- &gt; Agregar nuevo origen de datos.**..)
- En elegir un origen de datos Typewindow, seleccione **objeto** y haga clic en **siguiente** .
- En el cuadro de diálogo Seleccionar los objetos de datos, **WPFwithEFSample**   dos veces y seleccione **categoría**  
    *No es necesario seleccionar el origen de datos del **producto** , porque lo haremos a través de la propiedad del **producto**en el origen de datos de la **categoría** .*  

    ![Seleccionar objetos de datos](~/ef6/media/selectdataobjects.png)

- Haga clic en **Finish** (Finalizar).
- La ventana orígenes de datos se abre junto a la ventana MainWindow. XAML *si la ventana orígenes de datos no aparece, seleccione **Ver- &gt; otros orígenes de &gt; datos de Windows** * .
- Presione el icono de anclaje para que la ventana orígenes de datos no se oculte automáticamente. Es posible que tenga que presionar el botón actualizar si la ventana ya estaba visible.

    ![Orígenes de datos](~/ef6/media/datasources.png)

- Seleccione el origen de datos de la **categoría** y arrástrelo en el formulario.

Al arrastrar este origen, ocurre lo siguiente:

- El recurso **categoryViewSource** y el control **categoryDataGrid** se agregaron a XAML
- La propiedad DataContext del elemento Grid primario se estableció en "{StaticResource **categoryViewSource** }".El recurso **categoryViewSource** actúa como origen de enlace para el \\ elemento primario de la cuadrícula externa. Los elementos de la cuadrícula interna heredan entonces el valor DataContext de la cuadrícula primaria (la propiedad ItemsSource de categoryDataGrid se establece en "{Binding}")

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a>Adición de una cuadrícula de detalles

Ahora que tenemos una cuadrícula para mostrar las categorías, vamos a agregar una cuadrícula de detalles para mostrar los productos asociados.

- Seleccione la propiedad **Products** en el origen de datos **Category** y arrástrela en el formulario.
  - El recurso **categoryProductsViewSource** y la cuadrícula **productDataGrid** se agregan a XAML
  - La ruta de acceso de enlace de este recurso se establece en Products
  - El marco de enlace de datos de WPF garantiza que solo los productos relacionados con la categoría seleccionada se muestran en **productDataGrid**
- En el cuadro de herramientas, arrastre el **botón** al formulario. Establezca la propiedad **nombre** en **buttonSave** y la propiedad **contenido** en **Guardar**.

El formulario debe tener un aspecto similar al siguiente:

![Formulario del diseñador](~/ef6/media/designer.png)

## <a name="add-code-that-handles-data-interaction"></a>Adición de código que controla la interacción con los datos

Es el momento de agregar algunos controladores de eventos a la ventana principal.

- En la ventana XAML, haga clic en el elemento ** &lt; Window** ; Esto selecciona la ventana principal.
- En la ventana **propiedades** , elija **eventos** en la parte superior derecha y, a continuación, haga doble clic en el cuadro de texto a la derecha de la etiqueta **cargada** .

    ![Propiedades de la ventana principal](~/ef6/media/mainwindowproperties.png)

- Agregue también el evento **click** del botón **Guardar** haciendo doble clic en el botón Guardar en el diseñador.

Esto le lleva al código subyacente para el formulario. ahora editaremos el código para usar el ProductContext para realizar el acceso a los datos. Actualice el código para MainWindow, tal como se muestra a continuación.

El código declara una instancia de ejecución prolongada de **ProductContext**. El objeto **ProductContext** se usa para consultar y guardar datos en la base de datos. A continuación, se llama a **Dispose ()** en la instancia de **ProductContext** desde el método de **cierre** de sesión reemplazado.Los comentarios de código proporcionan detalles sobre lo que hace el código.

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a>Prueba de una aplicación WPF

- Compile y ejecute la aplicación. Si ha usado Code First, verá que se ha creado una base de datos **WPFwithEFSample. ProductContext** .
- Escriba un nombre de categoría en la cuadrícula superior y los nombres de producto en la cuadrícula inferior   *no escriba nada en las columnas de ID., porque la base de datos genera la clave principal.*

    ![Ventana principal con nuevas categorías y productos](~/ef6/media/screen1.png)

- Presione el botón **Guardar** para guardar los datos en la base de datos.

Después de la llamada a **SaveChanges ()** de DbContext, los identificadores se rellenan con los valores generados por la base de datos. Dado que llamamos a **Refresh ()** después de **SaveChanges ()** , los controles **DataGrid** también se actualizan con los nuevos valores.

![Ventana principal con los identificadores rellenados](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>Recursos adicionales

Para obtener más información sobre el enlace de datos a colecciones mediante WPF, vea [este tema](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) en la documentación de WPF.  
