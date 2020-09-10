---
title: 'Ingeniería inversa: EF Core'
description: Aplicar ingeniería inversa a un modelo a partir de una base de datos existente mediante Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: e88be834aa1f2f3be41657f07bc7ace3e34c58aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619217"
---
# <a name="reverse-engineering"></a> Ingeniería inversa

La ingeniería inversa es el proceso de scaffolding de las clases de tipo de entidad y una clase DbContext basada en un esquema de base de datos. Puede realizarse mediante el `Scaffold-DbContext` comando de EF Core herramientas de la consola del administrador de paquetes (PMC) o el `dotnet ef dbcontext scaffold` comando de las herramientas de la interfaz de la línea de comandos (CLI) de .net.

## <a name="installing"></a>Instalación de

Antes de la ingeniería inversa, deberá instalar las herramientas de [PMC](xref:core/miscellaneous/cli/powershell) (solo en Visual Studio) o las [herramientas](xref:core/miscellaneous/cli/dotnet)de la CLI. Vea los vínculos para obtener más información.

También necesitará instalar un [proveedor de base de datos](xref:core/providers/index) adecuado para el esquema de la base de datos al que desea aplicar ingeniería inversa.

## <a name="connection-string"></a>Cadena de conexión

El primer argumento del comando es una cadena de conexión a la base de datos. Las herramientas usarán esta cadena de conexión para leer el esquema de la base de datos.

La forma de citar y escapar de la cadena de conexión depende del shell que use para ejecutar el comando. Consulte la documentación de su shell para obtener información específica. Por ejemplo, PowerShell requiere que se escape el `$` carácter, pero no `\` .

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a>Configuración y secretos de usuario

Si tiene un proyecto de ASP.NET Core, puede usar la `Name=<connection-string>` sintaxis para leer la cadena de conexión de la configuración.

Esto funciona bien con la [herramienta de administración de secretos](/aspnet/core/security/app-secrets#secret-manager) para mantener la contraseña de la base de datos separada del código base.

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>Nombre del proveedor

El segundo argumento es el nombre del proveedor. El nombre del proveedor suele ser el mismo que el nombre del paquete NuGet del proveedor.

## <a name="specifying-tables"></a>Especificar tablas

De forma predeterminada, se aplica ingeniería inversa a todas las tablas del esquema de la base de datos en tipos de entidad. Puede limitar las tablas a las que se aplica ingeniería inversa mediante la especificación de esquemas y tablas.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

La `--schema` opción se puede usar para incluir todas las tablas de un esquema, mientras `--table` que se puede usar para incluir tablas específicas.

Para incluir varias tablas, especifique la opción varias veces:

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

La `-Schemas` opción se puede usar para incluir todas las tablas de un esquema, mientras `-Tables` que se puede usar para incluir tablas específicas.

Para incluir varias tablas, use una matriz:

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a>Conservar nombres

Los nombres de tablas y columnas se han corregido para que coincidan mejor con las convenciones de nomenclatura de .NET para tipos y propiedades de forma predeterminada. Al especificar el `-UseDatabaseNames` modificador en PMC o la `--use-database-names` opción en el CLI de .net Core, se deshabilitará este comportamiento conservando los nombres de las bases de datos originales lo máximo posible. Los identificadores de .NET no válidos seguirán siendo fijos y los nombres sintetizados, como las propiedades de navegación, seguirán conforme a las convenciones de nomenclatura de .NET.

## <a name="fluent-api-or-data-annotations"></a>Anotaciones de datos o API fluidas

Los tipos de entidad se configuran mediante la API fluida de forma predeterminada. Especifique `-DataAnnotations` (PMC) o `--data-annotations` (CLI de .net Core) para usar anotaciones de datos siempre que sea posible.

Por ejemplo, el uso de la API fluida le aplicará esta técnica:

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

Aunque el uso de anotaciones de datos es scaffolding:

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>Nombre de DbContext

El nombre de la clase DbContext con scaffolding será el nombre de la base de datos con sufijo de *contexto* de forma predeterminada. Para especificar otro, use `-Context` en PMC y `--context` en el CLI de .net Core.

## <a name="directories-and-namespaces"></a>Directorios y espacios de nombres

Las clases de entidad y una clase DbContext se scaffolding en el directorio raíz del proyecto y usan el espacio de nombres predeterminado del proyecto.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

Puede especificar el directorio en el que se usan las clases scaffolding usando `--output-dir` y `--context-dir` se puede usar para aplicar scaffolding a la clase DbContext en un directorio independiente de las clases de tipo de entidad:

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

De forma predeterminada, el espacio de nombres será el espacio de nombres raíz más los nombres de los subdirectorios del directorio raíz del proyecto. Sin embargo, desde EFCore 5,0 en adelante, puede invalidar el espacio de nombres para todas las clases de salida mediante `--namespace` . También puede invalidar el espacio de nombres solo para la clase DbContext mediante `--context-namespace` :

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Puede especificar el directorio en el que se usan las clases scaffolding usando `-OutputDir` y `-ContextDir` se puede usar para aplicar scaffolding a la clase DbContext en un directorio independiente de las clases de tipo de entidad:

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

De forma predeterminada, el espacio de nombres será el espacio de nombres raíz más los nombres de los subdirectorios del directorio raíz del proyecto. Sin embargo, desde EFCore 5,0 en adelante, puede invalidar el espacio de nombres para todas las clases de salida mediante `-Namespace` . También puede invalidar el espacio de nombres solo para la clase DbContext mediante `-ContextNamespace` .

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a>Funcionamiento

La ingeniería inversa comienza leyendo el esquema de la base de datos. Lee información acerca de las tablas, columnas, restricciones e índices.

A continuación, usa la información de esquema para crear un modelo de EF Core. Las tablas se usan para crear tipos de entidad. las columnas se usan para crear propiedades; y las claves externas se utilizan para crear relaciones.

Por último, el modelo se usa para generar código. Las clases de tipo de entidad, la API fluida y las anotaciones de datos correspondientes son scaffolding para volver a crear el mismo modelo desde la aplicación.

## <a name="limitations"></a>Limitaciones

* No todo lo relacionado con un modelo se puede representar mediante un esquema de la base de datos. Por ejemplo, la información sobre las [**jerarquías de herencia**](xref:core/modeling/inheritance), los [**tipos de propiedad**](xref:core/modeling/owned-entities)y la [**División de tablas**](xref:core/modeling/table-splitting) no están presentes en el esquema de la base de datos. Por este motivo, estas construcciones nunca se aplicarán a ingeniería inversa.
* Además, es posible que **algunos tipos de columna** no sean compatibles con el proveedor de EF Core. Estas columnas no se incluirán en el modelo.
* Puede definir [**tokens de simultaneidad**](xref:core/modeling/concurrency)en un modelo de EF Core para evitar que dos usuarios actualicen la misma entidad al mismo tiempo. Algunas bases de datos tienen un tipo especial para representar este tipo de columna (por ejemplo, rowversion en SQL Server), en cuyo caso se puede aplicar ingeniería inversa a esta información; sin embargo, no se aplicarán ingeniería inversa a otros tokens de simultaneidad.
* [La característica de tipo de referencia que acepta valores NULL de C# 8](/dotnet/csharp/tutorials/nullable-reference-types) no se admite actualmente en técnicas de ingeniería inversa: EF Core siempre genera código C# que supone que la característica está deshabilitada. Por ejemplo, las columnas de texto que aceptan valores NULL se scaffolding como una propiedad con `string` el tipo, no `string?` , con la API fluida o las anotaciones de datos que se usan para configurar si una propiedad es obligatoria o no. Puede editar el código con scaffolding y reemplazarlo con anotaciones de nulabilidad de C#. El seguimiento de la compatibilidad con scaffolding para tipos de referencia que aceptan valores NULL se realiza mediante el problema [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).

## <a name="customizing-the-model"></a>Personalización del modelo

El código generado por EF Core es el código. No dude en cambiarlo. Solo se regenerará si vuelve a aplicar ingeniería inversa al mismo modelo. El código con scaffolding representa *un* modelo que se puede utilizar para tener acceso a la base de datos, pero ciertamente no es el *único* modelo que se puede usar.

Personalice las clases de tipo de entidad y la clase DbContext para que se adapte a sus necesidades. Por ejemplo, puede elegir cambiar el nombre de tipos y propiedades, introducir jerarquías de herencia o dividir una tabla en varias entidades. También puede quitar índices no únicos, secuencias sin usar y propiedades de navegación, propiedades escalares opcionales y nombres de restricción del modelo.

También puede Agregar constructores, métodos, propiedades, etc. adicionales. usar otra clase parcial en un archivo independiente. Este enfoque funciona incluso cuando se desea volver a aplicar ingeniería inversa al modelo.

## <a name="updating-the-model"></a>Actualizar el modelo

Después de realizar cambios en la base de datos, puede que tenga que actualizar el modelo de EF Core para reflejar los cambios. Si los cambios en la base de datos son sencillos, puede que sea más fácil realizar los cambios manualmente en el modelo de EF Core. Por ejemplo, cambiar el nombre de una tabla o columna, quitar una columna o actualizar el tipo de una columna son cambios triviales que se deben realizar en el código.

Sin embargo, los cambios más significativos no son tan sencillos como los que se realizan de forma manual. Un flujo de trabajo común consiste en volver a aplicar ingeniería inversa del modelo de la base de datos mediante `-Force` (PMC) o `--force` (CLI) para sobrescribir el modelo existente con uno actualizado.

Otra característica solicitada comúnmente es la posibilidad de actualizar el modelo de la base de datos a la vez que se conserva la personalización, como cambiar el nombre, las jerarquías de tipos, etc. Use el [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) de problemas para realizar el seguimiento del progreso de esta característica.

> [!WARNING]
> Si vuelve a aplicar ingeniería inversa al modelo desde la base de datos, se perderán los cambios realizados en los archivos.
