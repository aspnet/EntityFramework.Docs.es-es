---
title: Uso de Migrate. exe-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 989ea862-e936-4c85-926a-8cfbef5df5b8
ms.openlocfilehash: 34866ddbbf81f090a064af148a612dd354ae9401
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415674"
---
# <a name="using-migrateexe"></a>Uso de Migrate. exe
Migraciones de Code First se puede utilizar para actualizar una base de datos desde Visual Studio, pero también se puede ejecutar mediante la herramienta de línea de comandos Migrate. exe. Esta página proporcionará información general rápida sobre cómo usar Migrate. exe para ejecutar migraciones en una base de datos de.

> [!NOTE]
> En este artículo se supone que sabe cómo usar Migraciones de Code First en escenarios básicos. Si no lo hace, tendrá que leer [migraciones de Code First](~/ef6/modeling/code-first/migrations/index.md) antes de continuar.

## <a name="copy-migrateexe"></a>Copiar Migrate. exe

Al instalar Entity Framework mediante NuGet Migrate. exe estará dentro de la carpeta Tools del paquete descargado. En &lt;carpeta del proyecto&gt;\\paquetes\\EntityFramework.&lt;versión&gt;herramientas \\

Una vez que haya migrado. exe, debe copiarlo en la ubicación del ensamblado que contiene las migraciones.

Si su aplicación tiene como destino .NET 4, y no 4,5, tendrá que copiar también el **archivo Redirect. config** en la ubicación y cambiar el nombre de **Migrate. exe. config**. Esto es para que Migrate. exe obtenga las redirecciones de enlace correctas para poder encontrar el ensamblado de Entity Framework.

| .NET 4.5                                      | .NET 4.0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![Archivos .NET 4,5](~/ef6/media/net45files.png) | ![Archivos .NET 4,0](~/ef6/media/net40files.png) |

> [!NOTE]
> Migrate. exe no admite ensamblados x64.

Una vez que haya trasladado Migrate. exe a la carpeta correcta, debería poder usarlo para ejecutar migraciones en la base de datos. Todas las utilidades están diseñadas para ejecutar migraciones. No puede generar migraciones ni crear un script SQL.

## <a name="see-options"></a>Vea opciones

``` console
Migrate.exe /?
```

Lo anterior mostrará la página de ayuda asociada a esta utilidad. tenga en cuenta que deberá tener el archivo EntityFramework. dll en la misma ubicación en la que está ejecutando Migrate. exe para que funcione.

## <a name="migrate-to-the-latest-migration"></a>Migrar a la migración más reciente

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

Al ejecutar Migrate. exe, el único parámetro obligatorio es el ensamblado, que es el ensamblado que contiene las migraciones que está intentando ejecutar, pero utilizará todos los valores basados en convenciones si no especifica el archivo de configuración.

## <a name="migrate-to-a-specific-migration"></a>Migrar a una migración específica

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

Si desea ejecutar migraciones hasta una migración específica, puede especificar el nombre de la migración. Se ejecutarán todas las migraciones anteriores según sea necesario hasta llegar a la migración especificada.

## <a name="specify-working-directory"></a>Especificar el directorio de trabajo

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

Si el ensamblado tiene dependencias o lee archivos en relación con el directorio de trabajo, tendrá que establecer startupDirectory.

## <a name="specify-migration-configuration-to-use"></a>Especificar la configuración de migración que se va a usar

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

Si tiene varias clases de configuración de migración, las clases que heredan de DbMigrationConfiguration, debe especificar qué se va a usar para esta ejecución. Esto se especifica proporcionando el segundo parámetro opcional sin un modificador como el anterior.

## <a name="provide-connection-string"></a>Proporcionar una cadena de conexión

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

Si desea especificar una cadena de conexión en la línea de comandos, también debe proporcionar el nombre del proveedor. Si no se especifica el nombre del proveedor, se producirá una excepción.

## <a name="common-problems"></a>Problemas habituales

| Mensaje de error                                                                                                                                                                                                                                                                                                                      | Solución                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Excepción no controlada: System. IO. FileLoadException: no se pudo cargar el archivo o ensamblado ' EntityFramework, version = 5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089 ' o una de sus dependencias. La definición del manifiesto del ensamblado encontrado no coincide con la referencia de ensamblado. (Excepción de HRESULT: 0x80131040)         | Normalmente, esto significa que se ejecuta una aplicación .NET 4 sin el archivo Redirect. config. Debe copiar el archivo Redirect. config en la misma ubicación que Migrate. exe y cambiarle el nombre a Migrate. exe. config.                                                                                       |
| Excepción no controlada: System. IO. FileLoadException: no se pudo cargar el archivo o ensamblado ' EntityFramework, version = 4.4.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089 ' o una de sus dependencias. La definición del manifiesto del ensamblado encontrado no coincide con la referencia de ensamblado. (Excepción de HRESULT: 0x80131040)          | Esta excepción significa que está ejecutando una aplicación .NET 4,5 con el archivo Redirect. config copiado en la ubicación Migrate. exe. Si la aplicación es .NET 4,5, no es necesario tener el archivo de configuración con las redirecciones dentro de. Elimine el archivo Migrate. exe. config.                                    |
| ERROR: no se puede actualizar la base de datos para que coincida con el modelo actual porque hay cambios pendientes y la migración automática está deshabilitada. Escriba los cambios de modelo pendientes en una migración basada en código o habilite la migración automática. Establezca DbMigrationsConfiguration. AutomaticMigrationsEnabled en true para habilitar la migración automática. | Este error se produce si se ejecuta Migrate cuando no se ha creado una migración para hacer frente a los cambios realizados en el modelo y la base de datos no coincide con el modelo. La adición de una propiedad a una clase de modelo y, a continuación, la ejecución de Migrate. exe sin crear una migración para actualizar la base de datos es un ejemplo de esto. |
| ERROR: el tipo no se ha resuelto para el miembro ' System. Data. Entity. Migrations. Design. ToolingFacade + UpdateRunner, EntityFramework, version = 5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089 '.                                                                                                                                       | Este error puede deberse al especificar un directorio de inicio incorrecto. Debe ser la ubicación de Migrate. exe.                                                                                                                                                                                      |
| Excepción no controlada: System. NullReferenceException: referencia de objeto no establecida en una instancia de un objeto. <br/>   en System. Data. Entity. Migrations. Console. Program. Main (String [] args)                                                                                                                                             | Esto puede deberse a que no se especifica un parámetro necesario para un escenario que se está usando. Por ejemplo, si se especifica una cadena de conexión sin especificar el nombre del proveedor.                                                                                                                        |
| ERROR: se encontró más de un tipo de configuración de migraciones en el ensamblado ' ClassLibrary1 '. Especifique el nombre del que se va a usar.                                                                                                                                                                                                  | Como indica el error, hay más de una clase de configuración en el ensamblado especificado. Debe usar el modificador/configurationType para especificar cuál utilizar.                                                                                                                                           |
| ERROR: no se pudo cargar el archivo o ensamblado '&lt;assemblyName&gt;' o una de sus dependencias. El nombre de ensamblado o el código base especificados no son válidos. (Excepción de HRESULT: 0x80131047)                                                                                                                                                    | Esto puede deberse al hecho de especificar un nombre de ensamblado incorrectamente o no tener                                                                                                                                                                                                                          |
| ERROR: no se pudo cargar el archivo o ensamblado '&lt;assemblyName&gt;' o una de sus dependencias. Se ha intentado cargar un programa con un formato incorrecto.                                                                                                                                                                          | Esto sucede si está intentando ejecutar Migrate. exe en una aplicación x64. EF 5,0 y versiones anteriores solo funcionarán en x86.                                                                                                                                                                                |
