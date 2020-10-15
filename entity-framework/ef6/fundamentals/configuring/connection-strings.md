---
title: 'Cadenas de conexión y modelos: EF6'
description: Cadenas de conexión y modelos de Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: c60acb965b7062f3cd35dab94ee8dfe48394969a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063392"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="6db29-103">Cadenas de conexión y modelos</span><span class="sxs-lookup"><span data-stu-id="6db29-103">Connection strings and models</span></span>
<span data-ttu-id="6db29-104">En este tema se explica cómo Entity Framework detecta qué conexión de base de datos se va a usar y cómo se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="6db29-104">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="6db29-105">Los modelos creados con Code First y EF Designer se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="6db29-105">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="6db29-106">Normalmente, una aplicación Entity Framework usa una clase derivada de DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-106">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="6db29-107">Esta clase derivada llamará a uno de los constructores de la clase base DbContext para controlar:</span><span class="sxs-lookup"><span data-stu-id="6db29-107">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="6db29-108">Cómo se conectará el contexto a una base de datos, es decir, cómo se encuentra o se usa una cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="6db29-108">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="6db29-109">Si el contexto usará calcular un modelo con Code First o cargar un modelo creado con el diseñador de EF</span><span class="sxs-lookup"><span data-stu-id="6db29-109">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="6db29-110">Opciones avanzadas adicionales</span><span class="sxs-lookup"><span data-stu-id="6db29-110">Additional advanced options</span></span>  

<span data-ttu-id="6db29-111">Los siguientes fragmentos muestran algunas de las formas en que se pueden usar los constructores DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-111">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="6db29-112">Usar Code First con conexión por Convención</span><span class="sxs-lookup"><span data-stu-id="6db29-112">Use Code First with connection by convention</span></span>  

<span data-ttu-id="6db29-113">Si no ha realizado ninguna otra configuración en la aplicación, la llamada al constructor sin parámetros en DbContext hará que DbContext se ejecute en modo de Code First con una conexión de base de datos creada por Convención.</span><span class="sxs-lookup"><span data-stu-id="6db29-113">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="6db29-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6db29-114">For example:</span></span>  

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

<span data-ttu-id="6db29-115">En este ejemplo DbContext usa el nombre completo del espacio de nombres de la clase de contexto derivada (demo. EF. BloggingContext) como nombre de la base de datos y crea una cadena de conexión para esta base de datos mediante SQL Express o LocalDB.</span><span class="sxs-lookup"><span data-stu-id="6db29-115">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="6db29-116">Si ambos están instalados, se usará SQL Express.</span><span class="sxs-lookup"><span data-stu-id="6db29-116">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="6db29-117">Visual Studio 2010 incluye SQL Express de forma predeterminada y Visual Studio 2012 y versiones posteriores incluyen LocalDB.</span><span class="sxs-lookup"><span data-stu-id="6db29-117">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="6db29-118">Durante la instalación, el paquete NuGet de EntityFramework comprueba qué servidor de base de datos está disponible.</span><span class="sxs-lookup"><span data-stu-id="6db29-118">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="6db29-119">Después, el paquete NuGet actualizará el archivo de configuración estableciendo el servidor de base de datos predeterminado que Code First usa al crear una conexión por Convención.</span><span class="sxs-lookup"><span data-stu-id="6db29-119">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="6db29-120">Si SQL Express se está ejecutando, se usará.</span><span class="sxs-lookup"><span data-stu-id="6db29-120">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="6db29-121">Si SQL Express no está disponible, LocalDB se registrará como predeterminado en su lugar.</span><span class="sxs-lookup"><span data-stu-id="6db29-121">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="6db29-122">No se realiza ningún cambio en el archivo de configuración si ya contiene un valor para el generador de conexiones predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6db29-122">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="6db29-123">Usar Code First con la conexión por Convención y el nombre de base de datos especificado</span><span class="sxs-lookup"><span data-stu-id="6db29-123">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="6db29-124">Si no ha realizado ninguna otra configuración en la aplicación, la llamada al constructor de cadena en DbContext con el nombre de la base de datos que desea usar hará que DbContext se ejecute en modo de Code First con una conexión de base de datos creada por Convención en la base de datos de ese nombre.</span><span class="sxs-lookup"><span data-stu-id="6db29-124">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="6db29-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6db29-125">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="6db29-126">En este ejemplo DbContext usa "BloggingDatabase" como nombre de la base de datos y crea una cadena de conexión para esta base de datos mediante SQL Express (instalado con Visual Studio 2010) o LocalDB (instalado con Visual Studio 2012).</span><span class="sxs-lookup"><span data-stu-id="6db29-126">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="6db29-127">Si ambos están instalados, se usará SQL Express.</span><span class="sxs-lookup"><span data-stu-id="6db29-127">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="6db29-128">Usar Code First con la cadena de conexión en el archivo de app.config/web.config</span><span class="sxs-lookup"><span data-stu-id="6db29-128">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="6db29-129">Puede optar por colocar una cadena de conexión en el archivo de app.config o web.config.</span><span class="sxs-lookup"><span data-stu-id="6db29-129">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="6db29-130">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6db29-130">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="6db29-131">Esta es una manera fácil de indicar a DbContext que use un servidor de base de datos que no sea SQL Express o LocalDB; el ejemplo anterior especifica una base de datos de SQL Server Compact Edition.</span><span class="sxs-lookup"><span data-stu-id="6db29-131">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="6db29-132">Si el nombre de la cadena de conexión coincide con el nombre del contexto (ya sea con o sin calificación de espacio de nombres), DbContext lo buscará cuando se use el constructor sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="6db29-132">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="6db29-133">Si el nombre de la cadena de conexión es diferente del nombre del contexto, puede indicar a DbContext que use esta conexión en el modo Code First pasando el nombre de la cadena de conexión al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-133">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="6db29-134">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6db29-134">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="6db29-135">Como alternativa, puede usar el formato "name = \<connection string name\> " para la cadena que se pasa al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-135">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="6db29-136">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6db29-136">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="6db29-137">Este formulario hace que sea explícito esperar que la cadena de conexión se encuentre en el archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="6db29-137">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="6db29-138">Se producirá una excepción si no se encuentra una cadena de conexión con el nombre especificado.</span><span class="sxs-lookup"><span data-stu-id="6db29-138">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="6db29-139">Base de datos/Model First con una cadena de conexión en el archivo de app.config/web.config</span><span class="sxs-lookup"><span data-stu-id="6db29-139">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="6db29-140">Los modelos creados con EF Designer son diferentes de Code First en que el modelo ya existe y no se genera a partir del código cuando se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6db29-140">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="6db29-141">El modelo normalmente existe como archivo EDMX en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6db29-141">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="6db29-142">El diseñador agregará una cadena de conexión EF al archivo app.config o web.config.</span><span class="sxs-lookup"><span data-stu-id="6db29-142">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="6db29-143">Esta cadena de conexión es especial, ya que contiene información sobre cómo encontrar la información en el archivo EDMX.</span><span class="sxs-lookup"><span data-stu-id="6db29-143">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="6db29-144">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6db29-144">For example:</span></span>  

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

<span data-ttu-id="6db29-145">El diseñador de EF también generará código que indica a DbContext que use esta conexión pasando el nombre de la cadena de conexión al constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-145">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="6db29-146">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6db29-146">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="6db29-147">DbContext sabe cargar el modelo existente (en lugar de usar Code First para calcularlo a partir del código) porque la cadena de conexión es una cadena de conexión EF que contiene los detalles del modelo que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="6db29-147">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="6db29-148">Otras opciones del constructor DbContext</span><span class="sxs-lookup"><span data-stu-id="6db29-148">Other DbContext constructor options</span></span>  

<span data-ttu-id="6db29-149">La clase DbContext contiene otros constructores y patrones de uso que permiten algunos escenarios más avanzados.</span><span class="sxs-lookup"><span data-stu-id="6db29-149">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="6db29-150">Algunas de éstas son:</span><span class="sxs-lookup"><span data-stu-id="6db29-150">Some of these are:</span></span>  

- <span data-ttu-id="6db29-151">Puede usar la clase DbModelBuilder para crear un modelo de Code First sin crear instancias de una instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-151">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="6db29-152">El resultado de este es un objeto DbModel.</span><span class="sxs-lookup"><span data-stu-id="6db29-152">The result of this is a DbModel object.</span></span> <span data-ttu-id="6db29-153">Después, puede pasar este objeto DbModel a uno de los constructores DbContext cuando esté listo para crear la instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-153">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="6db29-154">Puede pasar una cadena de conexión completa a DbContext en lugar de solo la base de datos o el nombre de la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="6db29-154">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="6db29-155">De forma predeterminada, esta cadena de conexión se usa con el proveedor System. Data. SqlClient; Esto se puede cambiar estableciendo una implementación diferente de IConnectionFactory en el contexto. Database. DefaultConnectionFactory.</span><span class="sxs-lookup"><span data-stu-id="6db29-155">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="6db29-156">Puede usar un objeto DbConnection existente pasándolo a un constructor DbContext.</span><span class="sxs-lookup"><span data-stu-id="6db29-156">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="6db29-157">Si el objeto de conexión es una instancia de EntityConnection, se usará el modelo especificado en la conexión en lugar de calcular un modelo mediante Code First.</span><span class="sxs-lookup"><span data-stu-id="6db29-157">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="6db29-158">Si el objeto es una instancia de algún otro tipo (por ejemplo, SqlConnection), el contexto lo utilizará para el modo de Code First.</span><span class="sxs-lookup"><span data-stu-id="6db29-158">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="6db29-159">Puede pasar un ObjectContext existente a un constructor DbContext para crear un DbContext que ajuste el contexto existente.</span><span class="sxs-lookup"><span data-stu-id="6db29-159">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="6db29-160">Se puede usar para las aplicaciones existentes que usan ObjectContext pero que desean aprovechar DbContext en algunas partes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6db29-160">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
