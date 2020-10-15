---
title: 'Migraciones con varios proveedores: EF Core'
description: Usar migraciones para administrar esquemas de base de datos cuando el destino es varios proveedores de bases de datos con Entity Framework Core
author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: f44abb5156ea3a175c68c1a0ec23ff41a9d13452
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061988"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="38d7d-103">Migraciones con varios proveedores</span><span class="sxs-lookup"><span data-stu-id="38d7d-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="38d7d-104">Las [herramientas de EF Core][1] solo las migraciones de scaffolding para el proveedor activo.</span><span class="sxs-lookup"><span data-stu-id="38d7d-104">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="38d7d-105">Sin embargo, a veces es posible que desee usar más de un proveedor (por ejemplo Microsoft SQL Server y SQLite) con DbContext.</span><span class="sxs-lookup"><span data-stu-id="38d7d-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="38d7d-106">Hay dos formas de controlar esto con las migraciones.</span><span class="sxs-lookup"><span data-stu-id="38d7d-106">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="38d7d-107">Puede mantener dos conjuntos de migraciones, uno para cada proveedor, o combinarlos en un único conjunto que pueda funcionar en ambos.</span><span class="sxs-lookup"><span data-stu-id="38d7d-107">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="38d7d-108">Dos conjuntos de migración</span><span class="sxs-lookup"><span data-stu-id="38d7d-108">Two migration sets</span></span>

<span data-ttu-id="38d7d-109">En el primer enfoque, se generan dos migraciones para cada cambio de modelo.</span><span class="sxs-lookup"><span data-stu-id="38d7d-109">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="38d7d-110">Una manera de hacerlo es colocar cada conjunto [de migración en un ensamblado independiente][2] y cambiar manualmente el proveedor activo (y el ensamblado de migraciones) entre agregar las dos migraciones.</span><span class="sxs-lookup"><span data-stu-id="38d7d-110">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="38d7d-111">Otro enfoque que facilita el trabajo con las herramientas es crear un nuevo tipo que derive de su DbContext e invalide el proveedor activo.</span><span class="sxs-lookup"><span data-stu-id="38d7d-111">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="38d7d-112">Este tipo se utiliza en tiempo de diseño al agregar o aplicar migraciones.</span><span class="sxs-lookup"><span data-stu-id="38d7d-112">This type is used at design time when adding or applying migrations.</span></span>

```csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="38d7d-113">Dado que cada conjunto de migración usa sus propios tipos DbContext, este enfoque no requiere el uso de un ensamblado de migración independiente.</span><span class="sxs-lookup"><span data-stu-id="38d7d-113">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="38d7d-114">Al agregar una nueva migración, especifique los tipos de contexto.</span><span class="sxs-lookup"><span data-stu-id="38d7d-114">When adding new migration, specify the context types.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="38d7d-115">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="38d7d-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="38d7d-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38d7d-116">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="38d7d-117">No es necesario especificar el directorio de salida para las migraciones posteriores, ya que se crean como elementos del mismo nivel que el último.</span><span class="sxs-lookup"><span data-stu-id="38d7d-117">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="38d7d-118">Un conjunto de migración</span><span class="sxs-lookup"><span data-stu-id="38d7d-118">One migration set</span></span>

<span data-ttu-id="38d7d-119">Si no le gusta tener dos conjuntos de migraciones, puede combinarlas manualmente en un único conjunto que se puede aplicar a ambos proveedores.</span><span class="sxs-lookup"><span data-stu-id="38d7d-119">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="38d7d-120">Las anotaciones pueden coexistir ya que un proveedor omite cualquier anotación que no comprenda.</span><span class="sxs-lookup"><span data-stu-id="38d7d-120">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="38d7d-121">Por ejemplo, una columna de clave principal que funciona con Microsoft SQL Server y SQLite podría tener este aspecto.</span><span class="sxs-lookup"><span data-stu-id="38d7d-121">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

```csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="38d7d-122">Si las operaciones solo se pueden aplicar a un proveedor o son diferentes entre proveedores, utilice la `ActiveProvider` propiedad para determinar qué proveedor está activo:</span><span class="sxs-lookup"><span data-stu-id="38d7d-122">If operations can be applied only for one provider, or they're different between providers, use the `ActiveProvider` property to determine which provider is active:</span></span>

```csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
