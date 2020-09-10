---
title: 'Migraciones con varios proveedores: EF Core'
description: Usar migraciones para administrar esquemas de base de datos cuando el destino es varios proveedores de bases de datos con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: df38af6ac700a530894b98e1f29bbe804831bad5
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619184"
---
# <a name="migrations-with-multiple-providers"></a>Migraciones con varios proveedores

Las [herramientas de EF Core][1] solo las migraciones de scaffolding para el proveedor activo. Sin embargo, a veces es posible que desee usar más de un proveedor (por ejemplo Microsoft SQL Server y SQLite) con DbContext. Hay dos formas de controlar esto con las migraciones. Puede mantener dos conjuntos de migraciones, uno para cada proveedor, o combinarlos en un único conjunto que pueda funcionar en ambos.

## <a name="two-migration-sets"></a>Dos conjuntos de migración

En el primer enfoque, se generan dos migraciones para cada cambio de modelo.

Una manera de hacerlo es colocar cada conjunto [de migración en un ensamblado independiente][2] y cambiar manualmente el proveedor activo (y el ensamblado de migraciones) entre agregar las dos migraciones.

Otro enfoque que facilita el trabajo con las herramientas es crear un nuevo tipo que derive de su DbContext e invalide el proveedor activo. Este tipo se utiliza en tiempo de diseño al agregar o aplicar migraciones.

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> Dado que cada conjunto de migración usa sus propios tipos DbContext, este enfoque no requiere el uso de un ensamblado de migración independiente.

Al agregar una nueva migración, especifique los tipos de contexto.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> No es necesario especificar el directorio de salida para las migraciones posteriores, ya que se crean como elementos del mismo nivel que el último.

## <a name="one-migration-set"></a>Un conjunto de migración

Si no le gusta tener dos conjuntos de migraciones, puede combinarlas manualmente en un único conjunto que se puede aplicar a ambos proveedores.

Las anotaciones pueden coexistir ya que un proveedor omite cualquier anotación que no comprenda. Por ejemplo, una columna de clave principal que funciona con Microsoft SQL Server y SQLite podría tener este aspecto.

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

Si las operaciones solo se pueden aplicar a un proveedor o son diferentes entre proveedores, utilice la `ActiveProvider` propiedad para determinar qué proveedor está activo:

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
