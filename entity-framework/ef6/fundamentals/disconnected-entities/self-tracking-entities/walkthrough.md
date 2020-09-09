---
title: 'Tutorial de entidades de seguimiento propio: EF6'
description: Tutorial de entidades de seguimiento propio para Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 942baae158d89acec98e70c391f677349148068c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616141"
---
# <a name="self-tracking-entities-walkthrough"></a>Tutorial de entidades de seguimiento propio
> [!IMPORTANT]
> Ya no se recomienda usar la plantilla Entidades de autoseguimiento. Solo sigue estando disponible para la compatibilidad con las aplicaciones existentes. Si la aplicación necesita trabajar con gráficos desconectados de entidades, considere otras alternativas, como [Trackable Entities](https://trackableentities.github.io/), que es una tecnología similar a Entidades de autoseguimiento pero que la comunidad desarrolla de forma más activa, o escriba código personalizado mediante la API de seguimiento de cambios de bajo nivel.

En este tutorial se muestra el escenario en el que un servicio de Windows Communication Foundation (WCF) expone una operación que devuelve un gráfico de entidades. A continuación, una aplicación cliente manipula dicho gráfico y envía las modificaciones a una operación de servicio que valida y guarda las actualizaciones en una base de datos mediante Entity Framework.

Antes de completar este tutorial, asegúrese de leer la página [entidades de seguimiento propio](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) .

Este tutorial realiza las siguientes acciones:

-   Crea una base de datos a la que se tiene acceso.
-   Crea una biblioteca de clases que contiene el modelo.
-   Intercambia la plantilla generador de entidades de seguimiento propio.
-   Mueve las clases de entidad a un proyecto independiente.
-   Crea un servicio WCF que expone las operaciones para consultar y guardar entidades.
-   Crea aplicaciones cliente (consola y WPF) que consumen el servicio.

Usaremos Database First en este tutorial, pero las mismas técnicas se aplican igualmente a Model First.

## <a name="pre-requisites"></a>Requisitos previos

Para completar este tutorial, necesitará una versión reciente de Visual Studio.

## <a name="create-a-database"></a>Crear una base de datos

El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:

-   Si usa Visual Studio 2012, va a crear una base de datos de LocalDB.
-   Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.

Vamos a generar la base de datos.

-   Apertura de Visual Studio
-   **Vista &gt; Explorador de servidores**
-   Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**
-   Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar **Microsoft SQL Server** como origen de datos
-   Conéctese a LocalDB o a SQL Express, en función de la que haya instalado.
-   Escriba **STESample** como nombre de la base de datos
-   Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .
-   La nueva base de datos aparecerá ahora en Explorador de servidores
-   Si usa Visual Studio 2012
    -   Haga clic con el botón derecho en la base de datos en el Explorador de servidores y seleccione **Nueva consulta**
    -   Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .
-   Si usa Visual Studio 2010
    -   Seleccionar **datos: &gt; Editor de Transact SQL: &gt; nueva conexión de consulta...**
    -   Escriba **. \\ SQLEXPRESS** como nombre del servidor y haga clic en **Aceptar**
    -   Seleccione la base de datos **STESample** en la lista desplegable de la parte superior del editor de consultas.
    -   Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **ejecutar SQL** .

``` SQL
    CREATE TABLE [dbo].[Blogs] (
        [BlogId] INT IDENTITY (1, 1) NOT NULL,
        [Name] NVARCHAR (200) NULL,
        [Url]  NVARCHAR (200) NULL,
        CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
    );

    CREATE TABLE [dbo].[Posts] (
        [PostId] INT IDENTITY (1, 1) NOT NULL,
        [Title] NVARCHAR (200) NULL,
        [Content] NTEXT NULL,
        [BlogId] INT NOT NULL,
        CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
        CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
    );

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a>Crear el modelo

En primer lugar, necesitamos un proyecto en el que colocar el modelo.

-   **Archivo- &gt; nuevo- &gt; proyecto...**
-   Seleccione **Visual C \# ** en el panel izquierdo y, a continuación, **biblioteca de clases** .
-   Escriba **STESample** como nombre y haga clic en **Aceptar** .

Ahora vamos a crear un modelo simple en EF Designer para tener acceso a la base de datos:

-   **Proyecto- &gt; Agregar nuevo elemento...**
-   Seleccione **datos** en el panel izquierdo y, a continuación, **ADO.NET Entity Data Model**
-   Escriba **BloggingModel** como nombre y haga clic en **Aceptar** .
-   Seleccione **generar desde la base de datos** y haga clic en **siguiente** .
-   Escriba la información de conexión de la base de datos que creó en la sección anterior
-   Escriba **BloggingContext** como nombre de la cadena de conexión y haga clic en **siguiente** .
-   Active la casilla situada junto a **tablas** y haga clic en **Finalizar** .

## <a name="swap-to-ste-code-generation"></a>Intercambio a la generación de código de STE

Ahora es necesario deshabilitar la generación de código y el intercambio predeterminados para las entidades de seguimiento propio.

### <a name="if-you-are-using-visual-studio-2012"></a>Si usa Visual Studio 2012

-   Expanda **BloggingModel. edmx** en **Explorador de soluciones** y elimine **BloggingModel.TT** y **BloggingModel.Context.TT**; de 
     *esta forma, se deshabilitará la generación de código predeterminada*
-   Haga clic con el botón secundario en un área vacía de la superficie de EF Designer y seleccione **Agregar elemento de generación de código..** .
-   Seleccione **en línea** en el panel izquierdo y busque el **generador Ste** .
-   Seleccione el **generador Ste para la \# plantilla de C** , escriba **STETemplate** como nombre y haga clic en **Agregar** .
-   Los archivos **STETemplate.TT** y **STETemplate.Context.TT** se agregan anidados en el archivo BloggingModel. edmx.

### <a name="if-you-are-using-visual-studio-2010"></a>Si usa Visual Studio 2010

-   Haga clic con el botón secundario en un área vacía de la superficie de EF Designer y seleccione **Agregar elemento de generación de código..** .
-   Seleccione **código** en el panel izquierdo y, a continuación, **ADO.net generador de entidades de seguimiento propio** .
-   Escriba **STETemplate** como nombre y haga clic en **Agregar** .
-   Los archivos **STETemplate.TT** y **STETemplate.Context.TT** se agregan directamente al proyecto

## <a name="move-entity-types-into-separate-project"></a>Traslado de tipos de entidad a un proyecto independiente

Para usar entidades de seguimiento propio, nuestra aplicación cliente necesita acceso a las clases de entidad generadas a partir de nuestro modelo. Dado que no queremos exponer todo el modelo a la aplicación cliente, vamos a trasladar las clases de entidad a un proyecto independiente.

El primer paso es dejar de generar las clases de entidad en el proyecto existente:

-   Haga clic con el botón derecho en **STETemplate.TT** en **Explorador de soluciones** y seleccione **propiedades** .
-   En la ventana **propiedades** , desactive **TextTemplatingFileGenerator** de la propiedad **CustomTool** .
-   Expandir **STETemplate.TT** en **Explorador de soluciones** y eliminar todos los archivos anidados en él

A continuación, vamos a agregar un nuevo proyecto y a generar las clases de entidad en él.

-   **Archivo- &gt; agregar- &gt; proyecto...**
-   Seleccione **Visual C \# ** en el panel izquierdo y, a continuación, **biblioteca de clases** .
-   Escriba **STESample. Entities** como nombre y haga clic en **Aceptar** .
-   **Proyecto: &gt; Agregar elemento existente...**
-   Navegue hasta la carpeta del proyecto **STESample**
-   Seleccione esta información para ver **todos los archivos ( \* . \* )**
-   Seleccione el archivo **STETemplate.TT**
-   Haga clic en la flecha desplegable situada junto al botón **Agregar** y seleccione **Agregar como vínculo** .

    ![Agregar plantilla vinculada](~/ef6/media/addlinkedtemplate.png)

También vamos a asegurarnos de que las clases de entidad se generan en el mismo espacio de nombres que el contexto. Esto solo reduce el número de instrucciones Using que necesitamos agregar en toda la aplicación.

-   Haga clic con el botón derecho en el **STETemplate.TT** vinculado en **Explorador de soluciones** y seleccione **propiedades** .
-   En la ventana **propiedades** , establezca el espacio de nombres de la **herramienta personalizada** en **STESample**

El código generado por la plantilla STE necesitará una referencia a **System. Runtime. Serialization** para compilar. Esta biblioteca es necesaria para los atributos **DataContract** y **DataMember** de WCF que se utilizan en los tipos de entidad serializables.

-   Haga clic con el botón derecho en el proyecto **STESample. Entities** en **Explorador de soluciones** y seleccione **Agregar referencia..** .
    -   En Visual Studio 2012: Active la casilla situada junto a **System. Runtime. Serialization** y haga clic en **Aceptar** .
    -   En Visual Studio 2010: seleccione **System. Runtime. Serialization** y haga clic en **Aceptar** .

Por último, el proyecto con nuestro contexto en él necesitará una referencia a los tipos de entidad.

-   Haga clic con el botón derecho en el proyecto **STESample** en **Explorador de soluciones** y seleccione **Agregar referencia..** .
    -   En Visual Studio 2012: seleccione **solución** en el panel izquierdo, active la casilla situada junto a **STESample. entidades** y haga clic en **Aceptar** .
    -   En Visual Studio 2010: seleccione la pestaña **proyectos** , seleccione **STESample. Entities** y haga clic en **Aceptar** .

>[!NOTE]
> Otra opción para mover los tipos de entidad a un proyecto independiente consiste en mover el archivo de plantilla, en lugar de vincularlo desde su ubicación predeterminada. Si lo hace, tendrá que actualizar la variable **ArchivoDeEntrada** en la plantilla para proporcionar la ruta de acceso relativa al archivo edmx (en este ejemplo sería **.. \\ BloggingModel. edmx**).

## <a name="create-a-wcf-service"></a>Crear un servicio WCF

Ahora es el momento de agregar un servicio WCF para exponer nuestros datos, comenzaremos por crear el proyecto.

-   **Archivo- &gt; agregar- &gt; proyecto...**
-   Seleccione **Visual C \# ** en el panel izquierdo y, a continuación, **aplicación de servicio WCF** .
-   Escriba **STESample. Service** como nombre y haga clic en **Aceptar** .
-   Agregar una referencia al ensamblado **System. Data. Entity**
-   Agregar una referencia a los proyectos **STESample** y **STESample. Entities**

Necesitamos copiar la cadena de conexión de EF en este proyecto para que se encuentre en tiempo de ejecución.

-   Abra el archivo **App.Config** para el proyecto **STESample **y copie el elemento **connectionStrings** .
-   Pegue el elemento **connectionStrings** como un elemento secundario del elemento de **configuración** del archivo de **Web.Config** en el proyecto **STESample. Service.**

Ahora es el momento de implementar el servicio real.

-   Abra **IService1.CS** y reemplace el contenido por el código siguiente.

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   Abra **Service1. SVC** y reemplace el contenido por el código siguiente.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a>Consumo del servicio desde una aplicación de consola

Vamos a crear una aplicación de consola que use nuestro servicio.

-   **Archivo- &gt; nuevo- &gt; proyecto...**
-   Seleccione **Visual C \# ** en el panel izquierdo y, a continuación, **aplicación de consola**
-   Escriba **STESample. ConsoleTest** como nombre y haga clic en **Aceptar** .
-   Agregar una referencia al proyecto **STESample. Entities**

Necesitamos una referencia de servicio a nuestro servicio WCF

-   Haga clic con el botón derecho en el proyecto **STESample. ConsoleTest** en **Explorador de soluciones** y seleccione **Agregar referencia de servicio...**
-   Haga clic en **detectar**
-   Escriba **BloggingService** como espacio de nombres y haga clic en **Aceptar** .

Ahora podemos escribir código para consumir el servicio.

-   Abra **Program.CS** y reemplace el contenido por el código siguiente.

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

Ahora puede ejecutar la aplicación para verla en acción.

-   Haga clic con el botón derecho en el proyecto **STESample. ConsoleTest** en **Explorador de soluciones** y seleccione **depurar- &gt; Iniciar nueva instancia** .

Verá el siguiente resultado cuando se ejecute la aplicación.

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a>Consumo del servicio desde una aplicación de WPF

Vamos a crear una aplicación WPF que use nuestro servicio.

-   **Archivo- &gt; nuevo- &gt; proyecto...**
-   Seleccione **Visual C \# ** en el panel izquierdo y, a continuación, **aplicación WPF** .
-   Escriba **STESample. WPFTest** como nombre y haga clic en **Aceptar** .
-   Agregar una referencia al proyecto **STESample. Entities**

Necesitamos una referencia de servicio a nuestro servicio WCF

-   Haga clic con el botón derecho en el proyecto **STESample. WPFTest** en **Explorador de soluciones** y seleccione **Agregar referencia de servicio...**
-   Haga clic en **detectar**
-   Escriba **BloggingService** como espacio de nombres y haga clic en **Aceptar** .

Ahora podemos escribir código para consumir el servicio.

-   Abra **MainWindow. Xaml** y reemplace el contenido por el código siguiente.

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   Abra el código subyacente de MainWindow (**MainWindow.Xaml.CS**) y reemplace el contenido por el código siguiente.

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

Ahora puede ejecutar la aplicación para verla en acción.

-   Haga clic con el botón derecho en el proyecto **STESample. WPFTest** en **Explorador de soluciones** y seleccione **depurar- &gt; Iniciar nueva instancia** .
-   Puede manipular los datos mediante la pantalla y guardarlos a través del servicio mediante el botón **Guardar**

![Ventana principal de WPF](~/ef6/media/wpf.png)
