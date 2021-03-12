---
title: 'Migraciones con varios proveedores: EF Core'
description: Usar migraciones para administrar esquemas de base de datos cuando el destino es varios proveedores de bases de datos con Entity Framework Core
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: feed19abb188eebc473386b67fac62848e682d96
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024100"
---
# <a name="migrations-with-multiple-providers"></a>Migraciones con varios proveedores

Las [herramientas de EF Core](xref:core/cli/index) solo las migraciones de scaffolding para el proveedor activo. Sin embargo, a veces es posible que desee usar más de un proveedor (por ejemplo Microsoft SQL Server y SQLite) con DbContext. Para controlar esto, se mantienen varios conjuntos de migraciones, uno para cada proveedor, y se agrega una migración a cada uno de ellos para cada cambio de modelo.

## <a name="using-multiple-context-types"></a>Usar varios tipos de contexto

Una manera de crear varios conjuntos de migración es usar un tipo DbContext por proveedor.

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

Especifique el tipo de contexto al agregar nuevas migraciones.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> No es necesario especificar el directorio de salida para las migraciones posteriores, ya que se crean como elementos del mismo nivel que el último.

## <a name="using-one-context-type"></a>Usar un tipo de contexto

También es posible usar un tipo DbContext. Esto requiere actualmente mover las migraciones a un ensamblado independiente. Consulte uso de [un proyecto de migración independiente](xref:core/managing-schemas/migrations/projects) para obtener instrucciones sobre la configuración de los proyectos.

> [!TIP]
> Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/TwoProjectMigrations) de este artículo.

A partir de EF Core 5,0, puede pasar argumentos a la aplicación desde las herramientas. Esto puede habilitar un flujo de trabajo más simplificado, lo que evita tener que realizar cambios manuales en el proyecto cuando se ejecutan las herramientas.

Este es un patrón que funciona bien cuando se usa un [host genérico](/dotnet/core/extensions/generic-host).

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

Dado que el generador de hosts predeterminado lee la configuración de los argumentos de línea de comandos, puede especificar el proveedor al ejecutar las herramientas.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> El `--` token dirige `dotnet ef` para tratar todo lo que sigue como argumento y no intentar analizarlos como opciones. Los argumentos adicionales que no use `dotnet ef` se reenvían a la aplicación.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> La capacidad de especificar argumentos adicionales para la aplicación se agregó en EF Core 5,0. Si utiliza una versión anterior, especifique en su lugar valores de configuración con variables de entorno.
