---
title: 'Administración de migraciones: EF Core'
description: Agregar, quitar y administrar de otro modo migraciones de esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: 81f7cec54510d95b1e2432d56ff95110224fd9bf
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429861"
---
# <a name="managing-migrations"></a>Administrar migraciones

A medida que cambia el modelo, las migraciones se agregan y se quitan como parte del desarrollo normal y los archivos de migración se protegen en el control de código fuente del proyecto. Para administrar las migraciones, primero debe instalar las [herramientas de línea de comandos de EF Core](xref:core/cli/index).

> [!TIP]
> Si `DbContext` está en un ensamblado diferente al del proyecto de inicio, puede especificar de manera explícita los proyectos de destino e inicio tanto en las [herramientas de la Consola del Administrador de paquetes](xref:core/cli/powershell#target-and-startup-project) como en las [herramientas de la CLI de .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).

## <a name="add-a-migration"></a>Agregar una migración

Una vez cambiado el modelo, puede Agregar una migración para ese cambio:

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

El nombre de la migración se puede usar como mensaje de confirmación en un sistema de control de versiones. Por ejemplo, puede elegir un nombre como *AddBlogCreatedTimestamp* si el cambio es una nueva `CreatedTimestamp` propiedad en la `Blog` entidad.

Se agregan tres archivos al proyecto en el directorio **Migraciones** :

* **XXXXXXXXXXXXXX_AddCreatedTimestamp. CS** : el archivo de migraciones principal. Contiene las operaciones necesarias para aplicar la migración (en `Up`) y para revertirla (en `Down`).
* **XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. CS** : el archivo de metadatos de migraciones. Contiene información que usa EF.
* **MyContextModelSnapshot.cs** : instantánea del modelo actual. Se usa para determinar qué ha cambiado al agregar la siguiente migración.

La marca de tiempo del nombre del archivo ayuda a mantenerlos ordenados cronológicamente para que se pueda ver la progresión de cambios.

### <a name="namespaces"></a>Espacios de nombres

Puede mover los archivos de Migraciones y cambiar su espacio de nombres manualmente cuando quiera. Se crean nuevas migraciones como elementos del mismo nivel de la última migración. También puede especificar el directorio en el momento de la generación como se indica a continuación:

#### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> En EF Core 5,0, también puede cambiar el espacio de nombres independientemente del directorio mediante `--namespace` .

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> En EF Core 5,0, también puede cambiar el espacio de nombres independientemente del directorio mediante `-Namespace` .

**_

## <a name="customize-migration-code"></a>Personalizar el código de migración

Aunque en general EF Core crea migraciones precisas, siempre debe revisar el código y asegurarse de que se corresponde con el cambio deseado; en algunos casos, incluso es necesario hacerlo.

### <a name="column-renames"></a>Cambiar nombre de columna

Un ejemplo importante donde se requiere la personalización de las migraciones es al cambiar el nombre de una propiedad. Por ejemplo, si cambia el nombre de una propiedad de `Name` a `FullName` , EF Core generará la siguiente migración:

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

Por lo general, EF Core no puede saber si la intención es quitar una columna y crear una nueva (dos cambios independientes) y cuándo debe cambiarse el nombre de una columna. Si la migración anterior se aplica tal cual, se perderán todos los nombres de cliente. Para cambiar el nombre de una columna, reemplace la migración anterior generada por lo siguiente:

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> El proceso de scaffolding de la migración advierte si una operación puede ocasionar una pérdida de datos (como el borrado de una columna). Si aparece dicha advertencia, asegúrese especialmente de revisar el código de las migraciones para mayor precisión.

### <a name="adding-raw-sql"></a>Agregar SQL sin procesar

Aunque el cambio de nombre de una columna se puede lograr a través de una API integrada, en muchos casos no es posible. Por ejemplo, es posible que desee reemplazar `FirstName` `LastName` las propiedades y existentes por una sola `FullName` propiedad nueva. La migración generada por EF Core será la siguiente:

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

Como antes, esto provocaría una pérdida de datos no deseada. Para transferir los datos de las columnas anteriores, se reorganizan las migraciones y se introduce una operación SQL sin procesar de la siguiente manera:

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a>Cambios arbitrarios a través de SQL sin formato

SQL sin formato también se puede usar para administrar objetos de base de datos que EF Core no tienen en cuenta. Para ello, agregue una migración sin realizar ningún cambio en el modelo; se generará una migración vacía, que se puede rellenar con operaciones SQL sin procesar.

Por ejemplo, la siguiente migración crea un SQL Server procedimiento almacenado:

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

> [!TIP]
> `EXEC` se utiliza cuando una instrucción debe ser la primera o solo una en un lote de SQL. También se puede usar para solucionar los errores del analizador en scripts de migración idempotente que se pueden producir cuando las columnas a las que se hace referencia no existen actualmente en una tabla.

Se puede usar para administrar cualquier aspecto de la base de datos, incluidos:

_ Procedimientos almacenados
* Búsqueda de texto completo
* Funciones
* Desencadenadores
* Vistas

En la mayoría de los casos, EF Core ajustará automáticamente cada migración en su propia transacción al aplicar las migraciones. Desafortunadamente, algunas operaciones de migración no se pueden realizar en una transacción en algunas bases de datos. en estos casos, puede rechazar la transacción pasando `suppressTransaction: true` a `migrationBuilder.Sql` .

Si `DbContext` está en un ensamblado diferente al del proyecto de inicio, puede especificar de manera explícita los proyectos de destino e inicio tanto en las [herramientas de la Consola del Administrador de paquetes](xref:core/cli/powershell#target-and-startup-project) como en las [herramientas de la CLI de .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).

## <a name="remove-a-migration"></a>Quitar una migración

A veces uno agrega una migración y se da cuenta de que debe realizar cambios adicionales en el modelo de EF Core antes de aplicarla. Para quitar la última migración, use este comando.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Remove-Migration
```

***

Después de quitar la migración, puede realizar los cambios de modelo adicionales y volver a agregarla.

> [!WARNING]
> Tenga cuidado de no quitar las migraciones que ya se hayan aplicado a las bases de datos de producción. Si no lo hace, impedirá que pueda revertirla y puede romper las suposiciones realizadas por migraciones posteriores.

## <a name="listing-migrations"></a>Enumerar migraciones

Puede enumerar todas las migraciones existentes como se indica a continuación:

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

> [!NOTE]
> Este comando se agregó en EF Core 5,0.

```powershell
Get-Migration
```

**_

## <a name="resetting-all-migrations"></a>Restableciendo todas las migraciones

En algunos casos extremos, puede que sea necesario quitar todas las migraciones y empezar de nuevo. Esto se puede hacer fácilmente eliminando la carpeta _ *Migrations* * y quitando la base de datos. en ese momento, puede crear una nueva migración inicial, que contendrá todo el esquema actual.

También es posible restablecer todas las migraciones y crear una sola sin perder los datos. A veces, esto se denomina "" "en" ", y implica algún trabajo manual:

* Eliminación de la carpeta **Migrations**
* Cree una nueva migración y genere un script SQL para ella.
* En la base de datos, elimine todas las filas de la tabla de historial de migraciones
* Inserte una sola fila en el historial de migraciones para registrar que la primera migración ya se ha aplicado, ya que las tablas ya están allí. La instrucción INSERT SQL es la última operación del script SQL que se generó anteriormente.

> [!WARNING]
> Cualquier [código de migración personalizado](#customize-migration-code) se perderá cuando se elimine la carpeta **Migrations** .  Las personalizaciones deben aplicarse a la nueva migración inicial manualmente para que se conserven.
