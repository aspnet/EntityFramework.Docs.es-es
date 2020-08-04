---
title: Actualización de EF Core 1,0 RC2 a RTM-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526776"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>Actualización de EF Core 1,0 RC2 a RTM

En este artículo se proporcionan instrucciones para mover una aplicación compilada con los paquetes de RC2 a 1.0.0 RTM.

## <a name="package-versions"></a>Versiones de paquetes

Los nombres de los paquetes de nivel superior que se instalarían normalmente en una aplicación no cambiaban entre RC2 y RTM.

**Debe actualizar los paquetes instalados a las versiones de RTM:**

* Los paquetes en tiempo de ejecución (por ejemplo, `Microsoft.EntityFrameworkCore.SqlServer` ) cambiaron de `1.0.0-rc2-final` a `1.0.0` .

* El `Microsoft.EntityFrameworkCore.Tools` paquete cambió de `1.0.0-preview1-final` a `1.0.0-preview2-final` . Tenga en cuenta que las herramientas siguen siendo versiones preliminares.

## <a name="existing-migrations-may-need-maxlength-added"></a>Es posible que las migraciones existentes necesiten maxLength agregadas

En RC2, la definición de columna en una migración `table.Column<string>(nullable: true)` se parecía y se buscó la longitud de la columna en algunos metadatos que almacenamos en el código subyacente a la migración. En RTM, la longitud se incluye ahora en el código con scaffolding `table.Column<string>(maxLength: 450, nullable: true)` .

Las migraciones existentes con scaffolding antes de usar RTM no tendrán el `maxLength` argumento especificado. Esto significa que se utilizará la longitud máxima admitida por la base de datos ( `nvarchar(max)` en SQL Server). Esto puede ser adecuado para algunas columnas, pero las columnas que forman parte de una clave, clave externa o índice deben actualizarse para incluir una longitud máxima. Por Convención, 450 es la longitud máxima utilizada para las claves, las claves externas y las columnas indizadas. Si ha configurado explícitamente una longitud en el modelo, debe utilizar esa longitud en su lugar.

### <a name="aspnet-identity"></a>ASP.NET Identity

Este cambio afecta a los proyectos que usan ASP.NET Identity y que se crearon a partir de una plantilla de proyecto anterior a RTM. La plantilla de proyecto incluye una migración que se usa para crear la base de datos. Esta migración se debe editar para especificar una longitud máxima de `256` para las columnas siguientes.

* **AspNetRoles**
  * NOMBRE
  * NormalizedName
* **AspNetUsers**
  * Correo electrónico
  * NormalizedEmail
  * NormalizedUserName
  * UserName

Si no se realiza este cambio, se producirá la siguiente excepción cuando la migración inicial se aplique a una base de datos.

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core: Quite "Imports" en project.jsen

Si tenía como destino .NET Core con RC2, necesitaba agregar `imports` a project.jscomo solución temporal para algunas de EF Core dependencias de que no admiten .net Standard. Ahora se pueden quitar.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> A partir de la versión 1,0 RTM, el [SDK de .net Core](https://www.microsoft.com/net/download/core) ya no admite `project.json` ni desarrolla aplicaciones de .net Core con Visual Studio 2015. Se recomienda [migrar de project.json a csproj](/dotnet/articles/core/migration/). Si usa Visual Studio, se recomienda que actualice a [visual studio 2017](https://www.visualstudio.com/downloads/).

## <a name="uwp-add-binding-redirects"></a>UWP: agregar redirecciones de enlace

Al intentar ejecutar los comandos EF en los proyectos de Plataforma universal de Windows (UWP) se produce el siguiente error:

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

Debe agregar manualmente redirecciones de enlace al proyecto de UWP. Cree un archivo denominado `App.config` en la carpeta raíz del proyecto y agregue redireccionamientos a las versiones de ensamblado correctas.

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
