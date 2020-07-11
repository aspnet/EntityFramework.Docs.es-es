---
title: Referencia de herramientas de EF Core (CLI de .NET)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 8ff2f3481c7f0c255def3272ca53370faba33e95
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238182"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Referencia de herramientas de Entity Framework Core-CLI de .NET Core

Las herramientas de la interfaz de la línea de comandos (CLI) para Entity Framework Core realizar tareas de desarrollo en tiempo de diseño. Por ejemplo, se crean [migraciones](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), se aplican migraciones y se genera código para un modelo basado en una base de datos existente. Los comandos son una extensión del comando [dotnet](/dotnet/core/tools) multiplataforma, que forma parte de la [SDK de .net Core](https://www.microsoft.com/net/core). Estas herramientas funcionan con proyectos de .NET Core.

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

* `dotnet ef`debe instalarse como una herramienta global o local. La mayoría de los desarrolladores se instalarán `dotnet ef` como una herramienta global con el siguiente comando:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  También puede usar `dotnet ef` como herramienta local. Para usarlo como una herramienta local, restaure las dependencias de un proyecto que la declara como una dependencia de herramientas mediante un [archivo de manifiesto](https://github.com/dotnet/cli/issues/10288)de la herramienta.

* Instale el [SDK de .NET Core](https://www.microsoft.com/net/download/core).

* Instale el paquete más reciente `Microsoft.EntityFrameworkCore.Design` .

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 +

* Instale el [SDK de .net Core](https://www.microsoft.com/net/download/core)actual. El SDK debe estar instalado incluso si dispone de la versión más reciente de Visual Studio 2017.

  Esto es todo lo que se necesita para ASP.NET Core 2.1 + porque el `Microsoft.EntityFrameworkCore.Design` paquete se incluye en el [metapaquete Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2. x (no ASP.NET Core)

Los `dotnet ef` comandos se incluyen en el SDK de .net Core, pero para habilitar los comandos que tiene para instalar el `Microsoft.EntityFrameworkCore.Design` paquete.

* Instale el [SDK de .net Core](https://www.microsoft.com/net/download/core)actual. El SDK debe instalarse incluso si tiene la versión más reciente de Visual Studio.

* Instale el paquete estable más reciente `Microsoft.EntityFrameworkCore.Design` .

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1. x

* Instale la versión SDK de .NET Core 2.1.200. Las versiones posteriores no son compatibles con las herramientas de la CLI para EF Core 1,0 y 1,1.

* Configure la aplicación para que use la versión del SDK de 2.1.200 modificando su [global.jsen](/dotnet/core/tools/global-json) el archivo. Este archivo se incluye normalmente en el directorio de la solución (uno encima del proyecto).

* Edite el archivo de proyecto y agréguelo `Microsoft.EntityFrameworkCore.Tools.DotNet` como un `DotNetCliToolReference` elemento. Especifique la versión más reciente de 1. x, por ejemplo: 1.1.6. Vea el ejemplo de archivo de proyecto al final de esta sección.

* Instale la versión 1. x más reciente del `Microsoft.EntityFrameworkCore.Design` paquete, por ejemplo:

  ```dotnetcli
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

  Una referencia de paquete con `PrivateAssets="All"` no se expone a los proyectos que hacen referencia a este proyecto. Esta restricción es especialmente útil para los paquetes que normalmente se usan solo durante el desarrollo.

### <a name="verify-installation"></a>Comprobación de la instalación

Ejecute los siguientes comandos para comprobar que las herramientas de la CLI de EF Core están instaladas correctamente:

  ```dotnetcli
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

## <a name="updating-the-tools"></a>Actualización de las herramientas

Use `dotnet tool update --global dotnet-ef` para actualizar las herramientas globales a la última versión disponible, si tiene instaladas las herramientas de forma local en el uso del proyecto `dotnet tool update dotnet-ef` . Instale una versión específica anexando `--version <VERSION>` al comando. Consulte la sección [actualización](/dotnet/core/tools/dotnet-tool-update) de la documentación de la herramienta dotnet para obtener más detalles.

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

Las herramientas de la CLI funcionan con proyectos de .NET Core y proyectos de .NET Framework. Es posible que las aplicaciones que tienen el modelo de EF Core en una biblioteca de clases .NET Standard no tengan un proyecto de .NET Core o .NET Framework. Por ejemplo, esto es cierto para las aplicaciones Xamarin y Plataforma universal de Windows. En tales casos, puede crear un proyecto de aplicación de consola de .NET Core cuyo único propósito es actuar como proyecto de inicio para las herramientas. El proyecto puede ser un proyecto ficticio sin código real &mdash; , solo es necesario para proporcionar un destino para las herramientas.

¿Por qué es necesario un proyecto ficticio? Como se mencionó anteriormente, las herramientas tienen que ejecutar código de aplicación en tiempo de diseño. Para ello, deben usar el tiempo de ejecución de .NET Core. Cuando el modelo de EF Core está en un proyecto que tiene como destino .NET Core o .NET Framework, las herramientas de EF Core toman prestado el tiempo de ejecución del proyecto. No pueden hacerlo si el modelo de EF Core está en una biblioteca de clases .NET Standard. El .NET Standard no es una implementación real de .NET; es una especificación de un conjunto de API que las implementaciones de .NET deben admitir. Por lo tanto .NET Standard no es suficiente para que las herramientas de EF Core ejecuten código de aplicación. El proyecto ficticio que cree para usarlo como proyecto de inicio proporciona una plataforma de destino concreta en la que las herramientas pueden cargar la biblioteca de clases de .NET Standard.

### <a name="aspnet-core-environment"></a>Entorno de ASP.NET Core

Para especificar el entorno de ASP.NET Core proyectos, establezca la variable de entorno **ASPNETCORE_ENVIRONMENT** antes de ejecutar los comandos.

## <a name="common-options"></a>Opciones comunes

| Opción                                         | Short             | Descripción                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | Muestra la salida JSON.                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | La clase `DbContext` que se va a usar. Nombre de clase solo o completo con espacios de nombres.  Si se omite esta opción, EF Core buscará la clase de contexto. Si hay varias clases de contexto, se requiere esta opción.                                            |
| `--project <PROJECT>`                          | `-p`              | Ruta de acceso relativa a la carpeta de proyecto del proyecto de destino.  El valor predeterminado es la carpeta actual.                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | Ruta de acceso relativa a la carpeta de proyecto del proyecto de inicio. El valor predeterminado es la carpeta actual.                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | Moniker de la [plataforma de destino](/dotnet/standard/frameworks#supported-target-framework-versions) para la [plataforma de destino](/dotnet/standard/frameworks).  Use cuando el archivo del proyecto especifique varias plataformas de destino y desee seleccionar una de ellas. |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | La configuración de compilación, por ejemplo: `Debug` o `Release` .                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | Identificador del Runtime de destino para el que se van a restaurar los paquetes. Para obtener una lista de identificadores de tiempo de ejecución (RID), consulte el [catálogo de RID](/dotnet/core/rid-catalog).                                                                                                      |
| `--help`                                       | `-h`              | Muestra información de ayuda.                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | Mostrar resultado detallado.                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | No colorear la salida.                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | Prefijo de salida con nivel.                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>eliminación de base de datos de dotnet EF

Quita la base de datos.

Opciones:

| Opción                   | Short             | Descripción                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | No confirme.                                           |
| <nobr>`--dry-run`</nobr> |                   | Mostrar la base de datos que se va a quitar, pero no quitarla. |

## <a name="dotnet-ef-database-update"></a>actualización de la base de datos de dotnet EF

Actualiza la base de datos a la última migración o a una migración especificada.

Argumentos:

| Argumento                   | Descripción                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | La migración de destino. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración* y hace que se reviertan todas las migraciones. Si no se especifica ninguna migración, el comando toma como valor predeterminado la última migración. |

Opciones:

| Opción                                    | Descripción                                                                                                                                        |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | La cadena de conexión a la base de datos. Tiene como valor predeterminado el especificado en `AddDbContext` o `OnConfiguring` . (Disponible en EF Core 5.0.0 en adelante). |

En los siguientes ejemplos se actualiza la base de datos a una migración especificada. El primero usa el nombre de la migración y el segundo usa el identificador de migración y una conexión especificada:

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>información de dbcontext de dotnet EF

Obtiene información sobre un `DbContext` tipo.

## <a name="dotnet-ef-dbcontext-list"></a>lista de dbcontext de dotnet EF

Enumera los `DbContext` tipos disponibles.

## <a name="dotnet-ef-dbcontext-scaffold"></a>scaffold de dbcontext de dotnet EF

Genera código para los `DbContext` tipos de entidad y para una base de datos. Para que este comando genere un tipo de entidad, la tabla de base de datos debe tener una clave principal.

Argumentos:

| Argumento                    | Descripción                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | La cadena de conexión a la base de datos. En el caso de los proyectos de ASP.NET Core 2. x, el valor puede ser *name = \<name of connection string> *. En ese caso, el nombre procede de los orígenes de configuración que se configuran para el proyecto. |
| `<PROVIDER>`                | Proveedor que se va a usar. Normalmente, es el nombre del paquete NuGet, por ejemplo: `Microsoft.EntityFrameworkCore.SqlServer` .                                                                                           |

Opciones:

| Opción                                   | Short             | Descripción                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | Use los atributos para configurar el modelo (siempre que sea posible). Si se omite esta opción, solo se usa la API fluida.                                                                |
| `--context <NAME>`                       | `-c`              | Nombre de la `DbContext` clase que se va a generar.                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | Directorio en el que se va a colocar el `DbContext` archivo de clase. Las rutas de acceso son relativas al directorio del proyecto. Los espacios de nombres se derivan de los nombres de carpeta.                                 |
| `--context-namespace <NAMESPACE>`        |                   | Espacio de nombres que se va a utilizar para la clase generada `DbContext` . Nota: invalida `--namespace` . (Disponible en EFCore 5.0.0 en adelante).                                                |
| `--force`                                | `-f`              | Sobrescribe los archivos existentes.                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | Directorio en el que se colocarán los archivos de clase de entidad. Las rutas de acceso son relativas al directorio del proyecto.                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | Espacio de nombres que se va a usar para todas las clases generadas. De forma predeterminada, se genera a partir del espacio de nombres raíz y el directorio de salida. (Disponible en EFCore 5.0.0 en adelante).                 |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | Esquemas de las tablas para las que se van a generar tipos de entidad. Para especificar varios esquemas, repita `--schema` cada uno de ellos. Si se omite esta opción, se incluyen todos los esquemas.          |
| `--table <TABLE_NAME>`...                | `-t`              | Tablas para las que se van a generar tipos de entidad. Para especificar varias tablas, repita `-t` o `--table` para cada una de ellas. Si se omite esta opción, se incluyen todas las tablas.                |
| `--use-database-names`                   |                   | Utilice nombres de tabla y columna exactamente como aparecen en la base de datos. Si se omite esta opción, se cambian los nombres de base de datos para que se ajusten mejor a las convenciones de estilo de nombre de C#. |
| `--no-onconfiguring`                     |                   | Suprime la generación del `OnConfiguring` método en la clase generada `DbContext` . (Disponible en EFCore 5.0.0 en adelante).                                                 |

En el ejemplo siguiente se scaffoldingan todos los esquemas y las tablas y se colocan los nuevos archivos en la carpeta *Models* .

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

En el ejemplo siguiente se scaffolding solo las tablas seleccionadas y se crea el contexto en una carpeta independiente con un nombre y un espacio de nombres especificados:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

## <a name="dotnet-ef-migrations-add"></a>Migraciones de dotnet EF Add

Agrega una nueva migración.

Argumentos:

| Argumento              | Descripción                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | El nombre de la migración. |

Opciones:

| Opción                                 | Short             | Descripción                                                                                                                             |
|:---------------------------------------|:------------------|:----------------------------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | El directorio que se usa para generar los archivos. Las rutas de acceso son relativas al directorio del proyecto de destino. El valor predeterminado es "migraciones".                    |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | Espacio de nombres que se va a usar para las clases generadas. De forma predeterminada, se genera desde el directorio de salida. (Disponible en EFCore 5.0.0 en adelante). |

## <a name="dotnet-ef-migrations-list"></a>lista de migraciones de dotnet EF

Muestra las migraciones disponibles.

## <a name="dotnet-ef-migrations-remove"></a>Migraciones de dotnet EF quitar

Quita la última migración (revierte los cambios de código que se realizaron para la migración).

Opciones:

| Opción                 | Short             | Descripción                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | Revertir la migración (revertir los cambios que se aplicaron a la base de datos). |

## <a name="dotnet-ef-migrations-script"></a>script de migraciones de dotnet EF

Genera un script SQL a partir de las migraciones.

Argumentos:

| Argumento              | Descripción                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | La migración inicial. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración*. El valor predeterminado es 0. |
| `<TO>`                | La migración final. Tiene como valor predeterminado la última migración.                                                                                                         |

Opciones:

| Opción                         | Short             | Descripción                                                        |
|:-------------------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | Archivo en el que se va a escribir el script.                                   |
| `--idempotent`                 | `-i`              | Generar un script que se puede usar en una base de datos en cualquier migración. |

En el ejemplo siguiente se crea un script para la migración de InitialCreate:

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

En el ejemplo siguiente se crea un script para todas las migraciones después de la migración de InitialCreate.

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>Recursos adicionales

* [Migraciones](xref:core/managing-schemas/migrations/index)
* [Ingeniería inversa](xref:core/managing-schemas/scaffolding)
