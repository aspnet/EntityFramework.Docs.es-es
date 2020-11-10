---
title: 'Uso de un proyecto de migración independiente: EF Core'
description: Uso de un proyecto de migración independiente para administrar esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 2d1c093e0bb307584e2bf19cb93deec98aa10692
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429811"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="83d01-103">Uso de un proyecto de migración independiente</span><span class="sxs-lookup"><span data-stu-id="83d01-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="83d01-104">Puede que desee almacenar las migraciones en un proyecto diferente del que contiene su `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="83d01-104">You may want to store your migrations in a different project than the one containing your `DbContext`.</span></span> <span data-ttu-id="83d01-105">También puede usar esta estrategia para mantener varios conjuntos de migraciones, por ejemplo, una para el desarrollo y otra para las actualizaciones de lanzamiento a lanzamiento.</span><span class="sxs-lookup"><span data-stu-id="83d01-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

> [!TIP]
> <span data-ttu-id="83d01-106">Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="83d01-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations).</span></span>

## <a name="steps"></a><span data-ttu-id="83d01-107">Pasos</span><span class="sxs-lookup"><span data-stu-id="83d01-107">Steps</span></span>

1. <span data-ttu-id="83d01-108">Cree una nueva biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="83d01-108">Create a new class library.</span></span>

2. <span data-ttu-id="83d01-109">Agregue una referencia al proyecto DbContext.</span><span class="sxs-lookup"><span data-stu-id="83d01-109">Add a reference to your DbContext project.</span></span>

3. <span data-ttu-id="83d01-110">Mueva las migraciones y los archivos de instantáneas de modelo a la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="83d01-110">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="83d01-111">Si no tiene ninguna migración existente, genere una en el proyecto que contiene el DbContext y muévala.</span><span class="sxs-lookup"><span data-stu-id="83d01-111">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="83d01-112">Esto es importante porque si el proyecto de migraciones no contiene una migración existente, el comando Add-Migration no podrá encontrar DbContext.</span><span class="sxs-lookup"><span data-stu-id="83d01-112">This is important because if the migrations project does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="83d01-113">Configure el ensamblado de migraciones:</span><span class="sxs-lookup"><span data-stu-id="83d01-113">Configure the migrations assembly:</span></span>

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. <span data-ttu-id="83d01-114">Agregue una referencia al proyecto de migraciones desde el proyecto de **Inicio** .</span><span class="sxs-lookup"><span data-stu-id="83d01-114">Add a reference to your migrations project from the **startup** project.</span></span>

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   <span data-ttu-id="83d01-115">Si esto provoca una dependencia circular, puede actualizar en su lugar la ruta de acceso de salida base del proyecto de **migraciones** :</span><span class="sxs-lookup"><span data-stu-id="83d01-115">If this causes a circular dependency, you can update the base output path of the **migrations** project instead:</span></span>

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

<span data-ttu-id="83d01-116">Si lo hizo todo correctamente, debería poder agregar nuevas migraciones al proyecto.</span><span class="sxs-lookup"><span data-stu-id="83d01-116">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="83d01-117">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="83d01-117">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="83d01-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d01-118">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
