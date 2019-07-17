---
title: Cadenas de conexión - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: c306f9ca7a51fc9e3db18e883fd44f56dd1a3cb4
ms.sourcegitcommit: e90d6cfa3e96f10b8b5275430759a66a0c714ed1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286448"
---
# <a name="connection-strings"></a><span data-ttu-id="81fc1-102">Cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="81fc1-102">Connection Strings</span></span>

<span data-ttu-id="81fc1-103">La mayoría de los proveedores de base de datos requiere algún tipo de cadena de conexión para conectarse a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="81fc1-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="81fc1-104">A veces, esta cadena de conexión contiene información confidencial que debe protegerse.</span><span class="sxs-lookup"><span data-stu-id="81fc1-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="81fc1-105">También es posible que deba cambiar la cadena de conexión como mover la aplicación entre entornos, como desarrollo, pruebas y producción.</span><span class="sxs-lookup"><span data-stu-id="81fc1-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="net-framework-applications"></a><span data-ttu-id="81fc1-106">Aplicaciones de .NET framework</span><span class="sxs-lookup"><span data-stu-id="81fc1-106">.NET Framework Applications</span></span>

<span data-ttu-id="81fc1-107">Aplicaciones de .NET framework, como WPF, WinForms, consola y ASP.NET 4, tienen un patrón de cadena de conexión probadas y comprobadas.</span><span class="sxs-lookup"><span data-stu-id="81fc1-107">.NET Framework applications, such as WinForms, WPF, Console, and ASP.NET 4, have a tried and tested connection string pattern.</span></span> <span data-ttu-id="81fc1-108">La cadena de conexión debe agregarse al archivo App.config de la aplicación (Web.config si está utilizando ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="81fc1-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="81fc1-109">Si la cadena de conexión contiene información confidencial, como el nombre de usuario y contraseña, puede proteger el contenido del archivo de configuración utilizando [configuración protegida](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span><span class="sxs-lookup"><span data-stu-id="81fc1-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> <span data-ttu-id="81fc1-110">El `providerName` configuración no es necesaria en las cadenas de conexión de EF Core almacenadas en el archivo App.config porque se configura el proveedor de base de datos a través del código.</span><span class="sxs-lookup"><span data-stu-id="81fc1-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="81fc1-111">A continuación, puede leer la cadena de conexión mediante el `ConfigurationManager` API en su contexto `OnConfiguring` método.</span><span class="sxs-lookup"><span data-stu-id="81fc1-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="81fc1-112">Es posible que deba agregar una referencia a la `System.Configuration` ensamblado de marco de trabajo para poder usar esta API.</span><span class="sxs-lookup"><span data-stu-id="81fc1-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="81fc1-113">Plataforma universal de Windows (UWP)</span><span class="sxs-lookup"><span data-stu-id="81fc1-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="81fc1-114">Las cadenas de conexión en una aplicación de UWP suelen ser una conexión de SQLite que solo especifica un nombre de archivo local.</span><span class="sxs-lookup"><span data-stu-id="81fc1-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="81fc1-115">Normalmente no contienen información confidencial y no deben cambiarse según se implementa una aplicación.</span><span class="sxs-lookup"><span data-stu-id="81fc1-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="81fc1-116">Por lo tanto, estas cadenas de conexión suelen funcionar correctamente al dejarse en el código, como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="81fc1-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="81fc1-117">Si desea moverlas fuera del código UWP admite el concepto de configuración, consulte el [sección de configuración de la aplicación de la documentación de UWP](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="81fc1-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a><span data-ttu-id="81fc1-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81fc1-118">ASP.NET Core</span></span>

<span data-ttu-id="81fc1-119">En ASP.NET Core, el sistema de configuración es muy flexible y la cadena de conexión se pueden almacenar en `appsettings.json`, una variable de entorno, el almacén secreto de usuario u otro origen de configuración.</span><span class="sxs-lookup"><span data-stu-id="81fc1-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="81fc1-120">Consulte la [sección de configuración de la documentación de ASP.NET Core](https://docs.asp.net/en/latest/fundamentals/configuration.html) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="81fc1-120">See the [Configuration section of the ASP.NET Core documentation](https://docs.asp.net/en/latest/fundamentals/configuration.html) for more details.</span></span> <span data-ttu-id="81fc1-121">El ejemplo siguiente muestra la cadena de conexión almacenada en `appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="81fc1-121">The following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="81fc1-122">Normalmente se configura en el contexto `Startup.cs` con la cadena de conexión que se leen de la configuración.</span><span class="sxs-lookup"><span data-stu-id="81fc1-122">The context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="81fc1-123">Tenga en cuenta la `GetConnectionString()` método busca un valor de configuración cuya clave es `ConnectionStrings:<connection string name>`.</span><span class="sxs-lookup"><span data-stu-id="81fc1-123">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="81fc1-124">Debe importar el [Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration) espacio de nombres para utilizar este método de extensión.</span><span class="sxs-lookup"><span data-stu-id="81fc1-124">You need to import the [Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
