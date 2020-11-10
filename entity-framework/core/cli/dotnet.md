---
title: Referencia de herramientas de EF Core (CLI de .NET)-EF Core
description: Guía de referencia de las herramientas de CLI de .NET Core de Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dotnet
ms.openlocfilehash: 83989b8690236dbec3466cda78c204ab67fd10c4
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431358"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Referencia de herramientas de Entity Framework Core-CLI de .NET Core

Las herramientas de la interfaz de la línea de comandos (CLI) para Entity Framework Core realizar tareas de desarrollo en tiempo de diseño. Por ejemplo, se crean [migraciones](/aspnet/core/data/ef-mvc/migrations), se aplican migraciones y se genera código para un modelo basado en una base de datos existente. Los comandos son una extensión del comando [dotnet](/dotnet/core/tools) multiplataforma, que forma parte de la [SDK de .net Core](https://www.microsoft.com/net/core). Estas herramientas funcionan con proyectos de .NET Core.

Al usar Visual Studio, considere la posibilidad de usar las herramientas de la [consola del administrador de paquetes](xref:core/cli/powershell) en lugar de las herramientas de la CLI. Herramientas de la consola del administrador de paquetes automáticamente:

* Funciona con el proyecto actual seleccionado en la **consola del administrador de paquetes** sin necesidad de cambiar manualmente los directorios.
* Abre los archivos generados por un comando una vez completado el comando.
* Proporciona la finalización con tabulación de los comandos, los parámetros, los nombres de proyecto, los tipos de contexto y los nombres de migración.

## <a name="installing-the-tools"></a>Instalación de las herramientas

`dotnet ef` se puede instalar como una herramienta global o local. La mayoría de los desarrolladores prefieren instalar `dotnet ef` como herramienta global con el siguiente comando:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Para usarlo como herramienta local, restaure las dependencias de un proyecto que lo declare como dependencia de herramientas mediante un [archivo de manifiesto de herramientas](/dotnet/core/tools/global-tools#install-a-local-tool).

Actualice la herramienta de herramientas con el siguiente comando:

```dotnetcli
dotnet tool update --global dotnet-ef
```

Antes de poder usar las herramientas en un proyecto específico, deberá agregar el `Microsoft.EntityFrameworkCore.Design` paquete a ella.

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### <a name="verify-installation"></a>Comprobación de la instalación

Ejecute los siguientes comandos para comprobar que las herramientas de la CLI de EF Core están instaladas correctamente:

  ```dotnetcli
  dotnet ef
  ```

La salida del comando identifica la versión de las herramientas en uso:

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="update-the-tools"></a>Actualización de las herramientas

Use `dotnet tool update --global dotnet-ef` para actualizar las herramientas globales a la última versión disponible. Si tiene instaladas las herramientas localmente en el proyecto, use `dotnet tool update dotnet-ef` . Instale una versión específica anexando `--version <VERSION>` al comando. Consulte la sección [actualización](/dotnet/core/tools/dotnet-tool-update) de la documentación de la herramienta dotnet para obtener más detalles.

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

Para especificar [el entorno](/aspnet/core/fundamentals/environments) de ASP.net Core proyectos, establezca la variable de entorno **ASPNETCORE_ENVIRONMENT** antes de ejecutar los comandos.

A partir de EF Core 5,0, también se pueden pasar argumentos adicionales a Program. CreateHostBuilder, lo que le permite especificar el entorno en la línea de comandos:

```dotnetcli
dotnet ef database update -- --environment Production
```

> [!TIP]
> El `--` token dirige `dotnet ef` para tratar todo lo que sigue como argumento y no intentar analizarlos como opciones. Los argumentos adicionales que no use `dotnet ef` se reenvían a la aplicación.

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
| `--no-build`                                   |                   | No compile el proyecto. Diseñado para usarse cuando la compilación está actualizada.                                                                                                                                                                                    |
| `--help`                                       | `-h`              | Muestra información de ayuda.                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | Mostrar resultado detallado.                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | No colorear la salida.                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | Prefijo de salida con nivel.                                                                                                                                                                                                                                     |

A partir de EF Core 5,0, se pasan los argumentos adicionales a la aplicación.

## <a name="dotnet-ef-database-drop"></a>eliminación de base de datos de dotnet EF

Elimina la base de datos.

Opciones:

| Opción                   | Short             | Descripción                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | No confirme.                                           |
| <nobr>`--dry-run`</nobr> |                   | Mostrar la base de datos que se va a quitar, pero no quitarla. |

A continuación se enumeran las [opciones comunes](#common-options) .

## <a name="dotnet-ef-database-update"></a>actualización de la base de datos de dotnet EF

Actualiza la base de datos a la última migración o a una migración especificada.

Argumentos:

| Argumento                   | Descripción                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | La migración de destino. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración* y hace que se reviertan todas las migraciones. Si no se especifica ninguna migración, el comando toma como valor predeterminado la última migración. |

Opciones:

| Opción                                    | Descripción                                                                                                                      |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | La cadena de conexión a la base de datos. Tiene como valor predeterminado el especificado en `AddDbContext` o `OnConfiguring` . Agregado en EF Core 5,0. |

A continuación se enumeran las [opciones comunes](#common-options) .

En los siguientes ejemplos se actualiza la base de datos a una migración especificada. El primero usa el nombre de la migración y el segundo usa el identificador de migración y una conexión especificada:

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>información de dbcontext de dotnet EF

Obtiene información sobre un `DbContext` tipo.

A continuación se enumeran las [opciones comunes](#common-options) .

## <a name="dotnet-ef-dbcontext-list"></a>lista de dbcontext de dotnet EF

Enumera los `DbContext` tipos disponibles.

A continuación se enumeran las [opciones comunes](#common-options) .

## <a name="dotnet-ef-dbcontext-scaffold"></a>scaffold de dbcontext de dotnet EF

Genera código para los `DbContext` tipos de entidad y para una base de datos. Para que este comando genere un tipo de entidad, la tabla de base de datos debe tener una clave principal.

Argumentos:

| Argumento                    | Descripción                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | La cadena de conexión a la base de datos. En el caso de los proyectos de ASP.NET Core 2. x, el valor puede ser *name = \<name of connection string>*. En ese caso, el nombre procede de los orígenes de configuración que se configuran para el proyecto. |
| `<PROVIDER>`                | Proveedor que se va a usar. Normalmente, es el nombre del paquete NuGet, por ejemplo: `Microsoft.EntityFrameworkCore.SqlServer` .                                                                                           |

Opciones:

| Opción                                   | Short             | Descripción                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | Use los atributos para configurar el modelo (siempre que sea posible). Si se omite esta opción, solo se usa la API fluida.                                                                |
| `--context <NAME>`                       | `-c`              | Nombre de la `DbContext` clase que se va a generar.                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | Directorio en el que se va a colocar el `DbContext` archivo de clase. Las rutas de acceso son relativas al directorio del proyecto. Los espacios de nombres se derivan de los nombres de carpeta.                                 |
| `--context-namespace <NAMESPACE>`        |                   | Espacio de nombres que se va a utilizar para la clase generada `DbContext` . Nota: invalida `--namespace` . Agregado en EF Core 5,0.                                                                 |
| `--force`                                | `-f`              | Sobrescribe los archivos existentes.                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | Directorio en el que se colocarán los archivos de clase de entidad. Las rutas de acceso son relativas al directorio del proyecto.                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | Espacio de nombres que se va a usar para todas las clases generadas. De forma predeterminada, se genera a partir del espacio de nombres raíz y el directorio de salida. Agregado en EF Core 5,0.                                  |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | Esquemas de las tablas para las que se van a generar tipos de entidad. Para especificar varios esquemas, repita `--schema` cada uno de ellos. Si se omite esta opción, se incluyen todos los esquemas.          |
| `--table <TABLE_NAME>`...                | `-t`              | Tablas para las que se van a generar tipos de entidad. Para especificar varias tablas, repita `-t` o `--table` para cada una de ellas. Si se omite esta opción, se incluyen todas las tablas.                |
| `--use-database-names`                   |                   | Utilice nombres de tabla y columna exactamente como aparecen en la base de datos. Si se omite esta opción, se cambian los nombres de base de datos para que se ajusten mejor a las convenciones de estilo de nombre de C#. |
| `--no-onconfiguring`                     |                   | Suprime la generación del `OnConfiguring` método en la clase generada `DbContext` . Agregado en EF Core 5,0.                                                                  |
| `--no-pluralize`                         |                   | No use pluralizador. Agregado en EF Core 5,0                                                                                                                                 |

A continuación se enumeran las [opciones comunes](#common-options) .

En el ejemplo siguiente se scaffoldingan todos los esquemas y las tablas y se colocan los nuevos archivos en la carpeta *Models* .

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

En el ejemplo siguiente se scaffolding solo las tablas seleccionadas y se crea el contexto en una carpeta independiente con un nombre y un espacio de nombres especificados:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

En el ejemplo siguiente se lee la cadena de conexión del conjunto de configuración del proyecto mediante la [herramienta Administrador de secretos](/aspnet/core/security/app-secrets#secret-manager).

```dotnetcli
dotnet user-secrets set ConnectionStrings.Blogging "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Blogging"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Blogging Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="dotnet-ef-dbcontext-script"></a>script de dbcontext de dotnet EF

Genera un script SQL desde DbContext. Omite las migraciones. Agregado en EF Core 3,0.

Opciones:

| Opción                         | Short             | Descripción                      |
| ------------------------------ | ----------------- | -------------------------------- |
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | Archivo en el que se va a escribir el resultado. |

A continuación se enumeran las [opciones comunes](#common-options) .

## <a name="dotnet-ef-migrations-add"></a>Migraciones de dotnet EF Add

Agrega una nueva migración.

Argumentos:

| Argumento              | Descripción                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | El nombre de la migración. |

Opciones:

| Opción                                 | Short             | Descripción                                                                                                            |
|:---------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | El directorio que se usa para generar los archivos. Las rutas de acceso son relativas al directorio del proyecto de destino. El valor predeterminado es "migraciones".   |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | Espacio de nombres que se va a usar para las clases generadas. De forma predeterminada, se genera desde el directorio de salida. Agregado en EF Core 5,0. |

A continuación se enumeran las [opciones comunes](#common-options) .

## <a name="dotnet-ef-migrations-list"></a>lista de migraciones de dotnet EF

Muestra las migraciones disponibles.

Opciones:

| Opción                                   | Descripción                                                                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| <nobr>`--connection <CONNECTION>`</nobr> | La cadena de conexión a la base de datos. Tiene como valor predeterminado el especificado en AddDbContext o en alconfigure. Agregado en EF Core 5,0. |
| `--no-connect`                           | No se conecte a la base de datos. Agregado en EF Core 5,0.                                                                         |

A continuación se enumeran las [opciones comunes](#common-options) .

## <a name="dotnet-ef-migrations-remove"></a>Migraciones de dotnet EF quitar

Quita la última migración (revierte los cambios de código que se realizaron para la migración).

Opciones:

| Opción                 | Short             | Descripción                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | Revertir la migración (revertir los cambios que se aplicaron a la base de datos). |

A continuación se enumeran las [opciones comunes](#common-options) .

## <a name="dotnet-ef-migrations-script"></a>script de migraciones de dotnet EF

Genera un script SQL a partir de las migraciones.

Argumentos:

| Argumento              | Descripción                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | La migración inicial. Las migraciones pueden identificarse por nombre o por identificador. El número 0 es un caso especial que significa *antes de la primera migración*. El valor predeterminado es 0. |
| `<TO>`                | La migración final. Tiene como valor predeterminado la última migración.                                                                                                         |

Opciones:

| Opción                           | Short             | Descripción                                                        |
|:---------------------------------|:------------------|:-------------------------------------------------------------------|
| `--output <FILE>`                | <nobr>`-o`</nobr> | Archivo en el que se va a escribir el script.                                   |
| `--idempotent`                   | `-i`              | Generar un script que se puede usar en una base de datos en cualquier migración. |
| <nobr>`--no-transactions`</nobr> |                   | No genere instrucciones de transacciones de SQL. Agregado en EF Core 5,0.   |

A continuación se enumeran las [opciones comunes](#common-options) .

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
