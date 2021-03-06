---
title: 'Cadenas de conexión: EF Core'
description: Administrar cadenas de conexión en entornos diferentes con Entity Framework Core
author: bricelam
ms.date: 10/27/2016
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 75e364eddd02087cffdffd1c152d1e988a99817b
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983565"
---
# <a name="connection-strings"></a>Cadenas de conexión

La mayoría de los proveedores de bases de datos requieren algún tipo de cadena de conexión para conectarse a la base de datos. A veces, esta cadena de conexión contiene información confidencial que debe protegerse. También es posible que necesite cambiar la cadena de conexión a medida que mueva la aplicación entre entornos, como desarrollo, pruebas y producción.

## <a name="aspnet-core"></a>ASP.NET Core

En ASP.NET Core el sistema de configuración es muy flexible y la cadena de conexión podría almacenarse en `appsettings.json` , una variable de entorno, el almacén de secretos de usuario u otro origen de configuración. Consulte la [sección configuración de la documentación de ASP.net Core](/aspnet/core/fundamentals/configuration) para obtener más detalles.

Por ejemplo, puede usar la [herramienta Administrador de secretos](/aspnet/core/security/app-secrets#secret-manager) para almacenar la contraseña de la base de datos y, a continuación, en la técnica scaffolding, usar una cadena de conexión que solo conste de `Name=<database-alias>` .

```dotnetcli
dotnet user-secrets set ConnectionStrings:YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings:YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

O en el ejemplo siguiente se muestra la cadena de conexión almacenada en `appsettings.json` .

```json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

Después, el contexto se configura normalmente en `Startup.cs` con la cadena de conexión que se lee de la configuración. Tenga en cuenta que el `GetConnectionString()` método busca un valor de configuración cuya clave sea `ConnectionStrings:<connection string name>` . Debe importar el espacio de nombres [Microsoft.Extensions.Configprimario](/dotnet/api/microsoft.extensions.configuration) para usar este método de extensión.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```

## <a name="winforms--wpf-applications"></a>Aplicaciones de WinForms & WPF

Las aplicaciones WinForms, WPF y ASP.NET 4 tienen un patrón de cadena de conexión probado y probado. La cadena de conexión debe agregarse al archivo de App.config de la aplicación (Web.config si usa ASP.NET). Si la cadena de conexión contiene información confidencial, como el nombre de usuario y la contraseña, puede proteger el contenido del archivo de configuración mediante la [configuración protegida](/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]
> La `providerName` configuración no es necesaria en EF Core cadenas de conexión almacenadas en App.config porque el proveedor de base de datos se configura mediante código.

Después, puede leer la cadena de conexión con la `ConfigurationManager` API en el método del contexto `OnConfiguring` . Es posible que tenga que agregar una referencia al ensamblado del marco `System.Configuration` para poder usar esta API.

```csharp
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

## <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Las cadenas de conexión en una aplicación UWP suelen ser una conexión SQLite que solo especifica un nombre de archivo local. Normalmente no contienen información confidencial y no es necesario cambiarla a medida que se implementa una aplicación. Como tal, estas cadenas de conexión suelen quedar en el código, como se muestra a continuación. Si quiere moverlos fuera del código, UWP admite el concepto de configuración, consulte la [sección configuración de la aplicación de la documentación de UWP](/windows/uwp/app-settings/store-and-retrieve-app-data) para obtener más información.

```csharp
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
