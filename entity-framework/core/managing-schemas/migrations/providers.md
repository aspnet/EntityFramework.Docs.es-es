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
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="4b2f0-103">Migraciones con varios proveedores</span><span class="sxs-lookup"><span data-stu-id="4b2f0-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="4b2f0-104">Las [herramientas de EF Core](xref:core/cli/index) solo las migraciones de scaffolding para el proveedor activo.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-104">The [EF Core Tools](xref:core/cli/index) only scaffold migrations for the active provider.</span></span> <span data-ttu-id="4b2f0-105">Sin embargo, a veces es posible que desee usar más de un proveedor (por ejemplo Microsoft SQL Server y SQLite) con DbContext.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="4b2f0-106">Para controlar esto, se mantienen varios conjuntos de migraciones, uno para cada proveedor, y se agrega una migración a cada uno de ellos para cada cambio de modelo.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-106">Handle this by maintaining multiple sets of migrations--one for each provider--and adding a migration to each for every model change.</span></span>

## <a name="using-multiple-context-types"></a><span data-ttu-id="4b2f0-107">Usar varios tipos de contexto</span><span class="sxs-lookup"><span data-stu-id="4b2f0-107">Using multiple context types</span></span>

<span data-ttu-id="4b2f0-108">Una manera de crear varios conjuntos de migración es usar un tipo DbContext por proveedor.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-108">One way to create multiple migration sets is to use one DbContext type per provider.</span></span>

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

<span data-ttu-id="4b2f0-109">Especifique el tipo de contexto al agregar nuevas migraciones.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-109">Specify the context type when adding new migrations.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="4b2f0-110">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4b2f0-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="4b2f0-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2f0-111">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="4b2f0-112">No es necesario especificar el directorio de salida para las migraciones posteriores, ya que se crean como elementos del mismo nivel que el último.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-112">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="using-one-context-type"></a><span data-ttu-id="4b2f0-113">Usar un tipo de contexto</span><span class="sxs-lookup"><span data-stu-id="4b2f0-113">Using one context type</span></span>

<span data-ttu-id="4b2f0-114">También es posible usar un tipo DbContext.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-114">It's also possible to use one DbContext type.</span></span> <span data-ttu-id="4b2f0-115">Esto requiere actualmente mover las migraciones a un ensamblado independiente.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-115">This currently requires moving the migrations into a separate assembly.</span></span> <span data-ttu-id="4b2f0-116">Consulte uso de [un proyecto de migración independiente](xref:core/managing-schemas/migrations/projects) para obtener instrucciones sobre la configuración de los proyectos.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-116">Please refer to [Using a Separate Migrations Project](xref:core/managing-schemas/migrations/projects) for instructions on setting up your projects.</span></span>

> [!TIP]
> <span data-ttu-id="4b2f0-117">Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/TwoProjectMigrations) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-117">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/TwoProjectMigrations).</span></span>

<span data-ttu-id="4b2f0-118">A partir de EF Core 5,0, puede pasar argumentos a la aplicación desde las herramientas.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-118">Starting in EF Core 5.0, you can pass arguments into the app from the tools.</span></span> <span data-ttu-id="4b2f0-119">Esto puede habilitar un flujo de trabajo más simplificado, lo que evita tener que realizar cambios manuales en el proyecto cuando se ejecutan las herramientas.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-119">This can enable a more streamlined workflow that avoids having to make manual changes to the project when running the tools.</span></span>

<span data-ttu-id="4b2f0-120">Este es un patrón que funciona bien cuando se usa un [host genérico](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="4b2f0-120">Here's one pattern that works well when using a [Generic Host](/dotnet/core/extensions/generic-host).</span></span>

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

<span data-ttu-id="4b2f0-121">Dado que el generador de hosts predeterminado lee la configuración de los argumentos de línea de comandos, puede especificar el proveedor al ejecutar las herramientas.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-121">Since the default host builder reads configuration from command-line arguments, you can specify the provider when running the tools.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="4b2f0-122">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4b2f0-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> <span data-ttu-id="4b2f0-123">El `--` token dirige `dotnet ef` para tratar todo lo que sigue como argumento y no intentar analizarlos como opciones.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-123">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="4b2f0-124">Los argumentos adicionales que no use `dotnet ef` se reenvían a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-124">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="4b2f0-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b2f0-125">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> <span data-ttu-id="4b2f0-126">La capacidad de especificar argumentos adicionales para la aplicación se agregó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-126">The ability to specify additional arguments for the app was added in EF Core 5.0.</span></span> <span data-ttu-id="4b2f0-127">Si utiliza una versión anterior, especifique en su lugar valores de configuración con variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="4b2f0-127">If you're using an older version, specify configuration values with environment variables instead.</span></span>
