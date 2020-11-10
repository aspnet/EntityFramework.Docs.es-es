---
title: Referencia de herramientas de EF Core (consola del administrador de paquetes)-EF Core
description: Guía de referencia de la consola del administrador de paquetes de Visual Studio Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/cli/powershell
ms.openlocfilehash: 4a1ab889fc1117b67252ace51fd3df4797b6c8d3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431357"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Referencia de herramientas de Entity Framework Core: consola del administrador de paquetes en Visual Studio

Las herramientas de la consola del administrador de paquetes (PMC) para Entity Framework Core realizar tareas de desarrollo en tiempo de diseño. Por ejemplo, se crean [migraciones](/aspnet/core/data/ef-mvc/migrations), se aplican migraciones y se genera código para un modelo basado en una base de datos existente. Los comandos se ejecutan dentro de Visual Studio mediante la [consola del administrador de paquetes](/nuget/tools/package-manager-console). Estas herramientas funcionan con proyectos de .NET Framework y .NET Core.

Si no usa Visual Studio, se recomienda usar en su lugar las [herramientas de línea de comandos de EF Core](xref:core/cli/dotnet) . Las herramientas de CLI de .NET Core son multiplataforma y se ejecutan en un símbolo del sistema.

## <a name="installing-the-tools"></a>Instalación de las herramientas

Instale las herramientas de la consola del administrador de paquetes ejecutando el siguiente comando en la **consola del administrador de paquetes** :

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Actualice las herramientas ejecutando el siguiente comando en la **consola del administrador de paquetes**.

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>Comprobación de la instalación

Ejecute este comando para comprobar que las herramientas están instaladas:

```powershell
Get-Help about_EntityFrameworkCore
```

La salida tiene el siguiente aspecto (no indica qué versión de las herramientas está usando):

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>Uso de las herramientas

Antes de usar las herramientas:

* Comprenda la diferencia entre el proyecto de destino y el de inicio.
* Aprenda a usar las herramientas con .NET Standard bibliotecas de clases.
* En el caso de los proyectos de ASP.NET Core, establezca el entorno.

### <a name="target-and-startup-project"></a>Proyecto de destino e inicio

Los comandos hacen referencia a un *proyecto* y un *proyecto de inicio*.

* El *proyecto* también se conoce como *proyecto de destino* porque es donde los comandos agregan o quitan archivos. De forma predeterminada, el proyecto **predeterminado** seleccionado en la **consola del administrador de paquetes** es el proyecto de destino. Puede especificar otro proyecto como proyecto de destino mediante la <nobr>`--project`</nobr> opción.

* El *proyecto de inicio* es el que las herramientas compilan y ejecutan. Las herramientas tienen que ejecutar código de aplicación en tiempo de diseño para obtener información sobre el proyecto, como la cadena de conexión a la base de datos y la configuración del modelo. De forma predeterminada, el **proyecto de inicio** en **Explorador de soluciones** es el proyecto de inicio. Puede especificar otro proyecto como proyecto de inicio mediante la <nobr>`--startup-project`</nobr> opción.

El proyecto de inicio y el proyecto de destino suelen ser el mismo proyecto. Un escenario típico en el que se trata de proyectos independientes es cuando:

* El contexto de EF Core y las clases de entidad se encuentran en una biblioteca de clases de .NET Core.
* Una aplicación de consola de .NET Core o una aplicación web hace referencia a la biblioteca de clases.

También es posible [colocar el código de las migraciones en una biblioteca de clases independiente del contexto de EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Otras plataformas de destino

Las herramientas de la consola del administrador de paquetes funcionan con proyectos de .NET Core o .NET Framework. Es posible que las aplicaciones que tienen el modelo de EF Core en una biblioteca de clases .NET Standard no tengan un proyecto de .NET Core o .NET Framework. Por ejemplo, esto es cierto para las aplicaciones Xamarin y Plataforma universal de Windows. En tales casos, puede crear un proyecto de aplicación de consola de .NET Core o .NET Framework cuyo único propósito es actuar como proyecto de inicio para las herramientas. El proyecto puede ser un proyecto ficticio sin código real &mdash; , solo es necesario para proporcionar un destino para las herramientas.

¿Por qué es necesario un proyecto ficticio? Como se mencionó anteriormente, las herramientas tienen que ejecutar código de aplicación en tiempo de diseño. Para ello, deben usar .NET Core o .NET Framework Runtime. Cuando el modelo de EF Core está en un proyecto que tiene como destino .NET Core o .NET Framework, las herramientas de EF Core toman prestado el tiempo de ejecución del proyecto. No pueden hacerlo si el modelo de EF Core está en una biblioteca de clases .NET Standard. El .NET Standard no es una implementación real de .NET; es una especificación de un conjunto de API que las implementaciones de .NET deben admitir. Por lo tanto .NET Standard no es suficiente para que las herramientas de EF Core ejecuten código de aplicación. El proyecto ficticio que cree para usarlo como proyecto de inicio proporciona una plataforma de destino concreta en la que las herramientas pueden cargar la biblioteca de clases de .NET Standard.

### <a name="aspnet-core-environment"></a>Entorno de ASP.NET Core

Para especificar [el entorno](/aspnet/core/fundamentals/environments) de ASP.net Core proyectos, establezca **env: ASPNETCORE_ENVIRONMENT** antes de ejecutar los comandos.

A partir de EF Core 5,0, también se pueden pasar argumentos adicionales a Program. CreateHostBuilder, lo que le permite especificar el entorno en la línea de comandos:

```powershell
Update-Database -Args '--environment Production'
```

## <a name="common-parameters"></a>Parámetros comunes

En la tabla siguiente se muestran los parámetros que son comunes a todos los comandos EF Core:

| Parámetro                 | Descripción                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Contexto \<String>        | La clase `DbContext` que se va a usar. Nombre de clase solo o completo con espacios de nombres.  Si se omite este parámetro, EF Core encuentra la clase de contexto. Si hay varias clases de contexto, este parámetro es obligatorio. |
| -Proyecto \<String>        | Proyecto de destino. Si se omite este parámetro, el **proyecto predeterminado** de la **consola del administrador de paquetes** se utiliza como proyecto de destino.                                                                             |
| <nobr>-Proyecto</nobr>\<String> | Proyecto de inicio. Si se omite este parámetro, el **proyecto de inicio** de **las propiedades** de la solución se usa como proyecto de destino.                                                                                 |
| -Args \<String>           | Argumentos pasados a la aplicación. Agregado en EF Core 5,0.                                                                                                                                                           |
| -Verbose                  | Mostrar resultado detallado.                                                                                                                                                                                                 |

Para mostrar información de ayuda sobre un comando, use el `Get-Help` comando de PowerShell.

> [!TIP]
> Los parámetros context, Project y proyecto admiten la expansión de pestañas.

## <a name="add-migration"></a>Add-Migration

Agrega una nueva migración.

Parámetros:

| Parámetro                         | Descripción                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Nombre \<String><nobr>       | El nombre de la migración. Este es un parámetro posicional y es obligatorio.                                              |
| <nobr>-OutputDir \<String></nobr> | El directorio que se usa para generar los archivos. Las rutas de acceso son relativas al directorio del proyecto de destino. El valor predeterminado es "migraciones". |
| <nobr>Espacio de nombres \<String></nobr> | Espacio de nombres que se va a usar para las clases generadas. De forma predeterminada, se genera desde el directorio de salida. Agregado en EF Core 5,0.  |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

## <a name="drop-database"></a>Drop-Database

Quita la base de datos.

Parámetros:

| Parámetro | Descripción                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | Mostrar la base de datos que se va a quitar, pero no quitarla. |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

## <a name="get-dbcontext"></a>Get-DbContext

Muestra y obtiene información acerca de los `DbContext` tipos disponibles.

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

## <a name="get-migration"></a>Get-Migration

Muestra las migraciones disponibles. Agregado en EF Core 5,0.

Parámetros:

| Parámetro                          | Descripción                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <nobr>-Conexión \<String></nobr> | La cadena de conexión a la base de datos. Tiene como valor predeterminado el especificado en AddDbContext o en alconfigure. |
| -Noconnect                         | No se conecte a la base de datos.                                                                         |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

## <a name="remove-migration"></a>Remove-Migration

Quita la última migración (revierte los cambios de código que se realizaron para la migración).

Parámetros:

| Parámetro | Descripción                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | Revertir la migración (revertir los cambios que se aplicaron a la base de datos). |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

## <a name="scaffold-dbcontext"></a>Scaffold-DbContext

Genera código para los `DbContext` tipos de entidad y para una base de datos. Para `Scaffold-DbContext` que genere un tipo de entidad, la tabla de base de datos debe tener una clave principal.

Parámetros:

| Parámetro                          | Descripción                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Conexión \<String></nobr> | La cadena de conexión a la base de datos. En el caso de los proyectos de ASP.NET Core 2. x, el valor puede ser *name = \<name of connection string>*. En ese caso, el nombre procede de los orígenes de configuración que se configuran para el proyecto. Este es un parámetro posicional y es obligatorio. |
| <nobr>-Proveedor \<String></nobr>   | Proveedor que se va a usar. Normalmente, es el nombre del paquete NuGet, por ejemplo: `Microsoft.EntityFrameworkCore.SqlServer` . Este es un parámetro posicional y es obligatorio.                                                                                           |
| -OutputDir \<String>               | Directorio en el que se colocarán los archivos. Las rutas de acceso son relativas al directorio del proyecto.                                                                                                                                                                                             |
| -ContextDir \<String>              | Directorio en el que se va a colocar el `DbContext` archivo. Las rutas de acceso son relativas al directorio del proyecto.                                                                                                                                                               |
| Espacio de nombres \<String>               | Espacio de nombres que se va a usar para todas las clases generadas. De forma predeterminada, se genera a partir del espacio de nombres raíz y el directorio de salida. Agregado en EF Core 5,0.                                                                                                                           |
| -ContextNamespace \<String>        | Espacio de nombres que se va a utilizar para la clase generada `DbContext` . Nota: invalida `-Namespace` . Agregado en EF Core 5,0.                                                                                                                                                           |
| -Contexto \<String>                 | Nombre de la `DbContext` clase que se va a generar.                                                                                                                                                                                                                          |
| -Esquemas \<String[]>               | Esquemas de las tablas para las que se van a generar tipos de entidad. Si se omite este parámetro, se incluyen todos los esquemas.                                                                                                                                                             |
| -Tablas \<String[]>                | Tablas para las que se van a generar tipos de entidad. Si se omite este parámetro, se incluyen todas las tablas.                                                                                                                                                                         |
| -DataAnnotations                   | Use los atributos para configurar el modelo (siempre que sea posible). Si se omite este parámetro, solo se usa la API fluida.                                                                                                                                                      |
| -UseDatabaseNames                  | Utilice nombres de tabla y columna exactamente como aparecen en la base de datos. Si se omite este parámetro, los nombres de base de datos se cambian para ajustarse mejor a las convenciones de estilo de nombre de C#.                                                                                       |
| -Force                             | Sobrescribe los archivos existentes.                                                                                                                                                                                                                                               |
| -NoOnConfiguring                   | No generar `DbContext.OnConfiguring` . Agregado en EF Core 5,0.                                                                                                                                                                                                         |
| -Nopluralización                       | No use pluralizador. Agregado en EF Core 5,0.                                                                                                                                                                                                                         |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

Ejemplo:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

Ejemplo que scaffolding solo selecciona tablas y crea el contexto en una carpeta independiente con un nombre y un espacio de nombres especificados:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

En el ejemplo siguiente se lee la cadena de conexión de la configuración del proyecto, posiblemente establecida mediante la [herramienta Administrador de secretos](/aspnet/core/security/app-secrets#secret-manager).

```powershell
Scaffold-DbContext "Name=ConnectionStrings.Blogging" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="script-dbcontext"></a>Script-DbContext

Genera un script SQL desde DbContext. Omite las migraciones. Agregado en EF Core 3,0.

Parámetros:

| Parámetro                      | Descripción                      |
| ------------------------------ | -------------------------------- |
| <nobr>-Salida \<String></nobr> | Archivo en el que se va a escribir el resultado. |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

## <a name="script-migration"></a>Script-Migration

Genera un script SQL que aplica todos los cambios de una migración seleccionada a otra migración seleccionada.

Parámetros:

| Parámetro                    | Descripción                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-Desde*\<String>            | La migración inicial. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración*. El valor predeterminado es 0.                                                              |
| *-Hasta*\<String>              | La migración final. Tiene como valor predeterminado la última migración.                                                                                                                                                                      |
| -Idempotente                  | Generar un script que se puede usar en una base de datos en cualquier migración.                                                                                                                                                         |
| <nobr>-Transtransacciones</nobr> | No genere instrucciones de transacciones de SQL. Agregado en EF Core 5,0.                                                                                                                                                           |
| -Salida \<String>            | Archivo en el que se va a escribir el resultado. Si se omite este parámetro, el archivo se crea con un nombre generado en la misma carpeta en que se crean los archivos en tiempo de ejecución de la aplicación, por ejemplo: */obj/Debug/netcoreapp2.1/ghbkztfz.SQL/*. |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

> [!TIP]
> Los parámetros para, de y de salida admiten la expansión de pestañas.

En el ejemplo siguiente se crea un script para la migración de InitialCreate (desde una base de datos sin ninguna migración), mediante el nombre de la migración.

```powershell
Script-Migration 0 InitialCreate
```

En el ejemplo siguiente se crea un script para todas las migraciones después de la migración de InitialCreate con el identificador de migración.

```powershell
Script-Migration 20180904195021_InitialCreate
```

## <a name="update-database"></a>Update-Database

Actualiza la base de datos a la última migración o a una migración especificada.

| Parámetro                           | Descripción                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-Migración*\<String></nobr> | La migración de destino. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración* y hace que se reviertan todas las migraciones. Si no se especifica ninguna migración, el comando toma como valor predeterminado la última migración. |
| <nobr>-Conexión \<String></nobr>  | La cadena de conexión a la base de datos. Tiene como valor predeterminado el especificado en `AddDbContext` o `OnConfiguring` . Agregado en EF Core 5,0.                                                                                                                                |

Los [parámetros comunes](#common-parameters) se enumeran a continuación.

> [!TIP]
> El parámetro Migration admite la expansión de pestañas.

En el ejemplo siguiente se revierten todas las migraciones.

```powershell
Update-Database 0
```

En los siguientes ejemplos se actualiza la base de datos a una migración especificada. El primero usa el nombre de la migración y el segundo usa el identificador de migración y una conexión especificada:

```powershell
Update-Database InitialCreate
Update-Database 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>Recursos adicionales

* [Migraciones](xref:core/managing-schemas/migrations/index)
* [Ingeniería inversa](xref:core/managing-schemas/scaffolding)
