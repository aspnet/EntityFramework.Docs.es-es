---
title: 'Cadenas de conexión y modelos: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
ms.openlocfilehash: 2c9f084107e4de7f5439bf0082b46a3b538496e0
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415956"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="6edac-102">Cadenas de conexión y modelos</span><span class="sxs-lookup"><span data-stu-id="6edac-102">Connection strings and models</span></span>
<span data-ttu-id="6edac-103">En este tema se explica cómo Entity Framework detecta qué conexión de base de datos se va a usar y cómo se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="6edac-103">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="6edac-104">Los modelos creados con Code First y EF Designer se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="6edac-104">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="6edac-105">Normalmente, una aplicación Entity Framework usa una clase derivada de DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-105">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="6edac-106">Esta clase derivada llamará a uno de los constructores de la clase base DbContext para controlar:</span><span class="sxs-lookup"><span data-stu-id="6edac-106">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="6edac-107">Cómo se conectará el contexto a una base de datos, es decir, cómo se encuentra o se usa una cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="6edac-107">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="6edac-108">Si el contexto usará calcular un modelo con Code First o cargar un modelo creado con el diseñador de EF</span><span class="sxs-lookup"><span data-stu-id="6edac-108">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="6edac-109">Opciones avanzadas adicionales</span><span class="sxs-lookup"><span data-stu-id="6edac-109">Additional advanced options</span></span>  

<span data-ttu-id="6edac-110">Los siguientes fragmentos muestran algunas de las formas en que se pueden usar los constructores DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-110">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="6edac-111">Usar Code First con conexión por Convención</span><span class="sxs-lookup"><span data-stu-id="6edac-111">Use Code First with connection by convention</span></span>  

<span data-ttu-id="6edac-112">Si no ha realizado ninguna otra configuración en la aplicación, la llamada al constructor sin parámetros en DbContext hará que DbContext se ejecute en modo de Code First con una conexión de base de datos creada por Convención.</span><span class="sxs-lookup"><span data-stu-id="6edac-112">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="6edac-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6edac-113">For example:</span></span>  

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

<span data-ttu-id="6edac-114">En este ejemplo DbContext usa el nombre completo del espacio de nombres de la clase de contexto derivada (demo. EF. BloggingContext) como nombre de la base de datos y crea una cadena de conexión para esta base de datos mediante SQL Express o LocalDB.</span><span class="sxs-lookup"><span data-stu-id="6edac-114">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="6edac-115">Si ambos están instalados, se usará SQL Express.</span><span class="sxs-lookup"><span data-stu-id="6edac-115">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="6edac-116">Visual Studio 2010 incluye SQL Express de forma predeterminada y Visual Studio 2012 y versiones posteriores incluyen LocalDB.</span><span class="sxs-lookup"><span data-stu-id="6edac-116">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="6edac-117">Durante la instalación, el paquete NuGet de EntityFramework comprueba qué servidor de base de datos está disponible.</span><span class="sxs-lookup"><span data-stu-id="6edac-117">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="6edac-118">Después, el paquete NuGet actualizará el archivo de configuración estableciendo el servidor de base de datos predeterminado que Code First usa al crear una conexión por Convención.</span><span class="sxs-lookup"><span data-stu-id="6edac-118">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="6edac-119">Si SQL Express se está ejecutando, se usará.</span><span class="sxs-lookup"><span data-stu-id="6edac-119">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="6edac-120">Si SQL Express no está disponible, LocalDB se registrará como predeterminado en su lugar.</span><span class="sxs-lookup"><span data-stu-id="6edac-120">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="6edac-121">No se realiza ningún cambio en el archivo de configuración si ya contiene un valor para el generador de conexiones predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6edac-121">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="6edac-122">Usar Code First con la conexión por Convención y el nombre de base de datos especificado</span><span class="sxs-lookup"><span data-stu-id="6edac-122">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="6edac-123">Si no ha realizado ninguna otra configuración en la aplicación, la llamada al constructor de cadena en DbContext con el nombre de la base de datos que desea usar hará que DbContext se ejecute en modo de Code First con una conexión de base de datos creada por Convención en la base de datos de ese nombre.</span><span class="sxs-lookup"><span data-stu-id="6edac-123">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="6edac-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6edac-124">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="6edac-125">En este ejemplo DbContext usa "BloggingDatabase" como nombre de la base de datos y crea una cadena de conexión para esta base de datos mediante SQL Express (instalado con Visual Studio 2010) o LocalDB (instalado con Visual Studio 2012).</span><span class="sxs-lookup"><span data-stu-id="6edac-125">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="6edac-126">Si ambos están instalados, se usará SQL Express.</span><span class="sxs-lookup"><span data-stu-id="6edac-126">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="6edac-127">Usar Code First con una cadena de conexión en el archivo app. config/Web. config</span><span class="sxs-lookup"><span data-stu-id="6edac-127">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="6edac-128">Puede optar por colocar una cadena de conexión en el archivo app. config o Web. config.</span><span class="sxs-lookup"><span data-stu-id="6edac-128">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="6edac-129">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6edac-129">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="6edac-130">Esta es una manera fácil de indicar a DbContext que use un servidor de base de datos que no sea SQL Express o LocalDB; el ejemplo anterior especifica una base de datos de SQL Server Compact Edition.</span><span class="sxs-lookup"><span data-stu-id="6edac-130">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="6edac-131">Si el nombre de la cadena de conexión coincide con el nombre del contexto (ya sea con o sin calificación de espacio de nombres), DbContext lo buscará cuando se use el constructor sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="6edac-131">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="6edac-132">Si el nombre de la cadena de conexión es diferente del nombre del contexto, puede indicar a DbContext que use esta conexión en el modo Code First pasando el nombre de la cadena de conexión al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-132">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="6edac-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6edac-133">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="6edac-134">Como alternativa, puede usar el formato "Name =\<nombre de cadena de conexión\>" para la cadena que se pasa al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-134">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="6edac-135">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6edac-135">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="6edac-136">Este formulario hace que sea explícito esperar que la cadena de conexión se encuentre en el archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="6edac-136">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="6edac-137">Se producirá una excepción si no se encuentra una cadena de conexión con el nombre especificado.</span><span class="sxs-lookup"><span data-stu-id="6edac-137">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="6edac-138">Base de datos/Model First con una cadena de conexión en el archivo app. config/Web. config</span><span class="sxs-lookup"><span data-stu-id="6edac-138">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="6edac-139">Los modelos creados con EF Designer son diferentes de Code First en que el modelo ya existe y no se genera a partir del código cuando se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6edac-139">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="6edac-140">El modelo normalmente existe como archivo EDMX en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6edac-140">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="6edac-141">El diseñador agregará una cadena de conexión de EF al archivo app. config o Web. config.</span><span class="sxs-lookup"><span data-stu-id="6edac-141">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="6edac-142">Esta cadena de conexión es especial, ya que contiene información sobre cómo encontrar la información en el archivo EDMX.</span><span class="sxs-lookup"><span data-stu-id="6edac-142">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="6edac-143">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6edac-143">For example:</span></span>  

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

<span data-ttu-id="6edac-144">El diseñador de EF también generará código que indica a DbContext que use esta conexión pasando el nombre de la cadena de conexión al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-144">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="6edac-145">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6edac-145">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="6edac-146">DbContext sabe cargar el modelo existente (en lugar de usar Code First para calcularlo a partir del código) porque la cadena de conexión es una cadena de conexión EF que contiene los detalles del modelo que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="6edac-146">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="6edac-147">Otras opciones del constructor DbContext</span><span class="sxs-lookup"><span data-stu-id="6edac-147">Other DbContext constructor options</span></span>  

<span data-ttu-id="6edac-148">La clase DbContext contiene otros constructores y patrones de uso que permiten algunos escenarios más avanzados.</span><span class="sxs-lookup"><span data-stu-id="6edac-148">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="6edac-149">Algunas de éstas son:</span><span class="sxs-lookup"><span data-stu-id="6edac-149">Some of these are:</span></span>  

- <span data-ttu-id="6edac-150">Puede usar la clase DbModelBuilder para crear un modelo de Code First sin crear instancias de una instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-150">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="6edac-151">El resultado de este es un objeto DbModel.</span><span class="sxs-lookup"><span data-stu-id="6edac-151">The result of this is a DbModel object.</span></span> <span data-ttu-id="6edac-152">Después, puede pasar este objeto DbModel a uno de los constructores DbContext cuando esté listo para crear la instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-152">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="6edac-153">Puede pasar una cadena de conexión completa a DbContext en lugar de solo la base de datos o el nombre de la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="6edac-153">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="6edac-154">De forma predeterminada, esta cadena de conexión se usa con el proveedor System. Data. SqlClient; Esto se puede cambiar estableciendo una implementación diferente de IConnectionFactory en el contexto. Database. DefaultConnectionFactory.</span><span class="sxs-lookup"><span data-stu-id="6edac-154">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="6edac-155">Puede usar un objeto DbConnection existente pasándolo a un constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6edac-155">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="6edac-156">Si el objeto de conexión es una instancia de EntityConnection, se usará el modelo especificado en la conexión en lugar de calcular un modelo mediante Code First.</span><span class="sxs-lookup"><span data-stu-id="6edac-156">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="6edac-157">Si el objeto es una instancia de algún otro tipo (por ejemplo, SqlConnection), el contexto lo utilizará para el modo de Code First.</span><span class="sxs-lookup"><span data-stu-id="6edac-157">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="6edac-158">Puede pasar un ObjectContext existente a un constructor DbContext para crear un DbContext que ajuste el contexto existente.</span><span class="sxs-lookup"><span data-stu-id="6edac-158">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="6edac-159">Se puede usar para las aplicaciones existentes que usan ObjectContext pero que desean aprovechar DbContext en algunas partes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6edac-159">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
