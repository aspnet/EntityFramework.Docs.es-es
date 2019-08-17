---
title: Referencia de herramientas de EF Core (CLI de .NET)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 0278353640ea242df9e6ee5278c9dda78bfd341b
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565269"
---
# <a name="entity-framework-core-tools-reference---net-cli"></a>Referencia de herramientas de Entity Framework Core: CLI de .NET

Las herramientas de la interfaz de la línea de comandos (CLI) para Entity Framework Core realizar tareas de desarrollo en tiempo de diseño. Por ejemplo, se crean [migraciones](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations), se aplican migraciones y se genera código para un modelo basado en una base de datos existente. Los comandos son una extensión del comando [dotnet](/dotnet/core/tools) multiplataforma, que forma parte de la [SDK de .net Core](https://www.microsoft.com/net/core). Estas herramientas funcionan con proyectos de .NET Core.

Si usa Visual Studio, se recomienda usar en su lugar las herramientas de la [consola del administrador de paquetes](powershell.md) :
* Funcionan automáticamente con el proyecto actual seleccionado en la **consola del administrador de paquetes** sin necesidad de cambiar manualmente los directorios.
* Abren automáticamente los archivos generados por un comando una vez completado el comando.

## <a name="installing-the-tools"></a>Instalación de las herramientas

El procedimiento de instalación depende del tipo y la versión del proyecto:

* EF Core 3. x
* ASP.NET Core versión 2,1 y versiones posteriores
* EF Core 2. x
* EF Core 1. x

### <a name="ef-core-3x"></a>EF Core 3. x

* `dotnet ef`debe instalarse como una herramienta global o local. La mayoría de los `dotnet ef` desarrolladores se instalarán como una herramienta global con el siguiente comando:

  ``` console
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  ```

  También puede usar `dotnet ef` como herramienta local. Para usarlo como una herramienta local, restaure las dependencias de un proyecto que la declara como una dependencia de herramientas mediante un [archivo de manifiesto](https://github.com/dotnet/cli/issues/10288)de la herramienta.

* Instale el [SDK de .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)). El SDK debe instalarse incluso si tiene la versión más reciente de Visual Studio.

* Instale el paquete `Microsoft.EntityFrameworkCore.Design` más reciente.

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 +

* Instale el [SDK de .net Core](https://www.microsoft.com/net/download/core)actual. El SDK debe estar instalado incluso si dispone de la versión más reciente de Visual Studio 2017.

  Esto es todo lo que se necesita para ASP.net Core 2.1 + porque `Microsoft.EntityFrameworkCore.Design` el paquete se incluye en el metapaquete [Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2. x (no ASP.NET Core)

Los `dotnet ef` comandos se incluyen en el SDK de .net Core, pero para habilitar los comandos que tiene para instalar el `Microsoft.EntityFrameworkCore.Design` paquete.

* Instale el [SDK de .net Core](https://www.microsoft.com/net/download/core)actual. El SDK debe instalarse incluso si tiene la versión más reciente de Visual Studio.

* Instale el paquete estable `Microsoft.EntityFrameworkCore.Design` más reciente.

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1. x

* Instale la versión SDK de .NET Core 2.1.200. Las versiones posteriores no son compatibles con las herramientas de la CLI para EF Core 1,0 y 1,1.

* Configure la aplicación para que use la versión del SDK de 2.1.200 modificando su archivo [global. JSON](/dotnet/core/tools/global-json) . Este archivo se incluye normalmente en el directorio de la solución (uno encima del proyecto).

* Edite el archivo de proyecto `Microsoft.EntityFrameworkCore.Tools.DotNet` y agréguelo `DotNetCliToolReference` como un elemento. Especifique la versión más reciente de 1. x, por ejemplo: 1.1.6. Vea el ejemplo de archivo de proyecto al final de esta sección.

* Instale la versión 1. x más reciente del `Microsoft.EntityFrameworkCore.Design` paquete, por ejemplo:

  ```console
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  Con las referencias de paquete agregadas, el archivo de proyecto tiene un aspecto similar al siguiente:

  ``` xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>netcoreapp1.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                        Version="1.1.6"
                         PrivateAssets="All" />
    </ItemGroup>
    <ItemGroup>
       <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                              Version="1.1.6" />
    </ItemGroup>
  </Project>
  ```

  Una referencia de paquete `PrivateAssets="All"` con no se expone a los proyectos que hacen referencia a este proyecto. Esta restricción es especialmente útil para los paquetes que normalmente se usan solo durante el desarrollo.

### <a name="verify-installation"></a>Comprobar la instalación

Ejecute los siguientes comandos para comprobar que las herramientas de la CLI de EF Core están instaladas correctamente:

  ``` Console
  dotnet restore
  dotnet ef
  ```

La salida del comando identifica la versión de las herramientas en uso:

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="using-the-tools"></a>Uso de las herramientas

Antes de usar las herramientas, puede que tenga que crear un proyecto de inicio o establecer el entorno.

### <a name="target-project-and-startup-project"></a>Proyecto de destino y proyecto de inicio

Los comandos hacen referencia a un *proyecto* y un *proyecto de inicio*.

* El *proyecto* también se conoce como *proyecto de destino* porque es donde los comandos agregan o quitan archivos. De forma predeterminada, el proyecto en el directorio actual es el proyecto de destino. Puede especificar otro proyecto como proyecto de destino mediante la <nobr>`--project`</nobr> opción.

* El *proyecto de inicio* es el que las herramientas compilan y ejecutan. Las herramientas tienen que ejecutar código de aplicación en tiempo de diseño para obtener información sobre el proyecto, como la cadena de conexión a la base de datos y la configuración del modelo. De forma predeterminada, el proyecto en el directorio actual es el proyecto de inicio. Puede especificar otro proyecto como proyecto de inicio mediante la <nobr>`--startup-project`</nobr> opción.

El proyecto de inicio y el proyecto de destino suelen ser el mismo proyecto. Un escenario típico en el que se trata de proyectos independientes es cuando:

* El contexto de EF Core y las clases de entidad se encuentran en una biblioteca de clases de .NET Core.
* Una aplicación de consola de .NET Core o una aplicación web hace referencia a la biblioteca de clases.

También es posible [colocar el código de las migraciones en una biblioteca de clases independiente del contexto de EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Otras plataformas de destino

Las herramientas de la CLI funcionan con proyectos de .NET Core y proyectos de .NET Framework. Es posible que las aplicaciones que tienen el modelo de EF Core en una biblioteca de clases .NET Standard no tengan un proyecto de .NET Core o .NET Framework. Por ejemplo, esto es cierto para las aplicaciones Xamarin y Plataforma universal de Windows. En tales casos, puede crear un proyecto de aplicación de consola de .NET Core cuyo único propósito es actuar como proyecto de inicio para las herramientas. El proyecto puede ser un proyecto ficticio sin código &mdash; real, solo es necesario para proporcionar un destino para las herramientas.

¿Por qué es necesario un proyecto ficticio? Como se mencionó anteriormente, las herramientas tienen que ejecutar código de aplicación en tiempo de diseño. Para ello, deben usar el tiempo de ejecución de .NET Core. Cuando el modelo de EF Core está en un proyecto que tiene como destino .NET Core o .NET Framework, las herramientas de EF Core toman prestado el tiempo de ejecución del proyecto. No pueden hacerlo si el modelo de EF Core está en una biblioteca de clases .NET Standard. El .NET Standard no es una implementación real de .NET; es una especificación de un conjunto de API que las implementaciones de .NET deben admitir. Por lo tanto .NET Standard no es suficiente para que las herramientas de EF Core ejecuten código de aplicación. El proyecto ficticio que cree para usarlo como proyecto de inicio proporciona una plataforma de destino concreta en la que las herramientas pueden cargar la biblioteca de clases de .NET Standard.

### <a name="aspnet-core-environment"></a>Entorno de ASP.NET Core

Para especificar el entorno de ASP.NET Core proyectos, establezca la variable de entorno **ASPNETCORE_ENVIRONMENT** antes de ejecutar los comandos.

## <a name="common-options"></a>Opciones comunes

|                   | Opción                            | DESCRIPCIÓN                                                                                                                                                                                                                                                   |
|:------------------|:----------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                   | `--json`                          | Muestra la salida JSON.                                                                                                                                                                                                                                             |
| <nobr>`-c`</nobr> | `--context <DBCONTEXT>`           | La clase `DbContext` que se va a usar. Nombre de clase solo o completo con espacios de nombres.  Si se omite esta opción, EF Core buscará la clase de contexto. Si hay varias clases de contexto, se requiere esta opción.                                            |
| `-p`              | `--project <PROJECT>`             | Ruta de acceso relativa a la carpeta de proyecto del proyecto de destino.  El valor predeterminado es la carpeta actual.                                                                                                                                                              |
| `-s`              | `--startup-project <PROJECT>`     | Ruta de acceso relativa a la carpeta de proyecto del proyecto de inicio. El valor predeterminado es la carpeta actual.                                                                                                                                                              |
|                   | `--framework <FRAMEWORK>`         | Moniker de la [plataforma de destino](/dotnet/standard/frameworks#supported-target-framework-versions) para la [plataforma de destino](/dotnet/standard/frameworks).  Use cuando el archivo del proyecto especifique varias plataformas de destino y desee seleccionar una de ellas. |
|                   | `--configuration <CONFIGURATION>` | La configuración de compilación, por ejemplo `Debug` : `Release`o.                                                                                                                                                                                                   |
|                   | `--runtime <IDENTIFIER>`          | Identificador del Runtime de destino para el que se van a restaurar los paquetes. Para obtener una lista de identificadores de tiempo de ejecución (RID), consulte el [catálogo de RID](/dotnet/core/rid-catalog).                                                                                                      |
| `-h`              | `--help`                          | Mostrar información de la ayuda.                                                                                                                                                                                                                                        |
| `-v`              | `--verbose`                       | Mostrar la salida detallada.                                                                                                                                                                                                                                          |
|                   | `--no-color`                      | No colorear la salida.                                                                                                                                                                                                                                        |
|                   | `--prefix-output`                 | Prefijo de salida con nivel.                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>eliminación de base de datos de dotnet EF

Quita la base de datos.

Opciones:

|                   | Opción                   | DESCRIPCIÓN                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>`-f`</nobr> | <nobr>`--force`</nobr>   | No confirme.                                           |
|                   | <nobr>`--dry-run`</nobr> | Mostrar la base de datos que se va a quitar, pero no quitarla. |

## <a name="dotnet-ef-database-update"></a>actualización de la base de datos de dotnet EF

Actualiza la base de datos a la última migración o a una migración especificada.

Argumentos:

| Argumento      | DESCRIPCIÓN                                                                                                                                                                                                                                                     |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<MIGRATION>` | La migración de destino. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración* y hace que se reviertan todas las migraciones. Si no se especifica ninguna migración, el comando toma como valor predeterminado la última migración. |

En los siguientes ejemplos se actualiza la base de datos a una migración especificada. El primero usa el nombre de la migración y el segundo usa el identificador de migración:

```console
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate
```

## <a name="dotnet-ef-dbcontext-info"></a>información de dbcontext de dotnet EF

Obtiene información sobre un `DbContext` tipo.

## <a name="dotnet-ef-dbcontext-list"></a>lista de dbcontext de dotnet EF

Enumera los `DbContext` tipos disponibles.

## <a name="dotnet-ef-dbcontext-scaffold"></a>scaffold de dbcontext de dotnet EF

Genera código para `DbContext` los tipos de entidad y para una base de datos. Para que este comando genere un tipo de entidad, la tabla de base de datos debe tener una clave principal.

Argumentos:

| Argumento       | DESCRIPCIÓN                                                                                                                                                                                                             |
|:---------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<CONNECTION>` | Cadena de conexión a la base de datos. En el caso de los proyectos de ASP.net Core 2. x, el valor puede ser *name =\<nombre de la cadena de conexión >* . En ese caso, el nombre procede de los orígenes de configuración que se configuran para el proyecto. |
| `<PROVIDER>`   | Proveedor que se va a usar. Normalmente, es el nombre del paquete NuGet, por ejemplo: `Microsoft.EntityFrameworkCore.SqlServer`.                                                                                           |

Opciones:

|                 | Opción                                   | DESCRIPCIÓN                                                                                                                                                                    |
|:----------------|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | `--data-annotations`                     | Use los atributos para configurar el modelo (siempre que sea posible). Si se omite esta opción, solo se usa la API fluida.                                                                |
| `-c`            | `--context <NAME>`                       | Nombre de la `DbContext` clase que se va a generar.                                                                                                                                 |
|                 | `--context-dir <PATH>`                   | Directorio en el que se `DbContext` va a colocar el archivo de clase. Las rutas de acceso son relativas al directorio del proyecto. Los espacios de nombres se derivan de los nombres de carpeta.                                 |
| `-f`            | `--force`                                | Sobrescribe los archivos existentes.                                                                                                                                                      |
| `-o`            | `--output-dir <PATH>`                    | Directorio en el que se colocarán los archivos de clase de entidad. Las rutas de acceso son relativas al directorio del proyecto.                                                                                       |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | Esquemas de las tablas para las que se van a generar tipos de entidad. Para especificar varios esquemas, repita `--schema` cada uno de ellos. Si se omite esta opción, se incluyen todos los esquemas.          |
| `-t`            | `--table <TABLE_NAME>`...                | Tablas para las que se van a generar tipos de entidad. Para especificar varias tablas, repita `-t` o `--table` para cada una de ellas. Si se omite esta opción, se incluyen todas las tablas.                |
|                 | `--use-database-names`                   | Utilice nombres de tabla y columna exactamente como aparecen en la base de datos. Si se omite esta opción, los nombres de base de datos se cambian para C# ajustarse mejor a las convenciones de estilo de nombre. |

En el ejemplo siguiente se scaffoldingan todos los esquemas y las tablas y se colocan los nuevos archivos en la carpeta *Models* .

```console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

En el ejemplo siguiente se scaffolding solo las tablas seleccionadas y se crea el contexto en una carpeta independiente con un nombre especificado:

```console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext
```

## <a name="dotnet-ef-migrations-add"></a>Migraciones de dotnet EF Add

Agrega una nueva migración.

Argumentos:

| Argumento | DESCRIPCIÓN                |
|:---------|:---------------------------|
| `<NAME>` | El nombre de la migración. |

Opciones:

|                   | Opción                             | DESCRIPCIÓN                                                                                                      |
|:------------------|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>`-o`</nobr> | <nobr>`--output-dir <PATH>`</nobr> | Directorio (y subespacio de nombres) que se va a usar. Las rutas de acceso son relativas al directorio del proyecto. El valor predeterminado es "migraciones". |

## <a name="dotnet-ef-migrations-list"></a>lista de migraciones de dotnet EF

Muestra las migraciones disponibles.

## <a name="dotnet-ef-migrations-remove"></a>Migraciones de dotnet EF quitar

Quita la última migración (revierte los cambios de código que se realizaron para la migración).

Opciones:

|                   | Opción    | DESCRIPCIÓN                                                                     |
|:------------------|:----------|:--------------------------------------------------------------------------------|
| <nobr>`-f`</nobr> | `--force` | Revertir la migración (revertir los cambios que se aplicaron a la base de datos). |

## <a name="dotnet-ef-migrations-script"></a>script de migraciones de dotnet EF

Genera un script SQL a partir de las migraciones.

Argumentos:

| Argumento | DESCRIPCIÓN                                                                                                                                                   |
|:---------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<FROM>` | La migración inicial. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración*. El valor predeterminado es 0. |
| `<TO>`   | La migración final. Tiene como valor predeterminado la última migración.                                                                                                         |

Opciones:

|                   | Opción            | DESCRIPCIÓN                                                        |
|:------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`-o`</nobr> | `--output <FILE>` | Archivo en el que se va a escribir el script.                                   |
| `-i`              | `--idempotent`    | Generar un script que se puede usar en una base de datos en cualquier migración. |

En el ejemplo siguiente se crea un script para la migración de InitialCreate:

```console
dotnet ef migrations script 0 InitialCreate
```

En el ejemplo siguiente se crea un script para todas las migraciones después de la migración de InitialCreate.

```console
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>Recursos adicionales

* [Migraciones](xref:core/managing-schemas/migrations/index)
* [Ingeniería inversa](xref:core/managing-schemas/scaffolding)
