---
title: 'Uso de un proyecto de migración independiente: EF Core'
description: Uso de un proyecto de migración independiente para administrar esquemas de base de datos con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: a3f0ed96c6a8e3e8629d9a4bb1610fcbfe6ca043
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617908"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="73611-103">Uso de un proyecto de migración independiente</span><span class="sxs-lookup"><span data-stu-id="73611-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="73611-104">Es posible que desee almacenar las migraciones en un ensamblado diferente del que contiene el `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="73611-104">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="73611-105">También puede usar esta estrategia para mantener varios conjuntos de migraciones, por ejemplo, una para el desarrollo y otra para las actualizaciones de lanzamiento a lanzamiento.</span><span class="sxs-lookup"><span data-stu-id="73611-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="73611-106">Para...</span><span class="sxs-lookup"><span data-stu-id="73611-106">To do this...</span></span>

1. <span data-ttu-id="73611-107">Cree una nueva biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="73611-107">Create a new class library.</span></span>

2. <span data-ttu-id="73611-108">Agregue una referencia al ensamblado DbContext.</span><span class="sxs-lookup"><span data-stu-id="73611-108">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="73611-109">Mueva las migraciones y los archivos de instantáneas de modelo a la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="73611-109">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="73611-110">Si no tiene ninguna migración existente, genere una en el proyecto que contiene el DbContext y muévala.</span><span class="sxs-lookup"><span data-stu-id="73611-110">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="73611-111">Esto es importante porque si el ensamblado de migraciones no contiene una migración existente, el comando Add-Migration no podrá encontrar DbContext.</span><span class="sxs-lookup"><span data-stu-id="73611-111">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="73611-112">Configure el ensamblado de migraciones:</span><span class="sxs-lookup"><span data-stu-id="73611-112">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="73611-113">Agregue una referencia al ensamblado de migraciones desde el ensamblado de inicio.</span><span class="sxs-lookup"><span data-stu-id="73611-113">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="73611-114">Si esto produce una dependencia circular, actualice la ruta de acceso de salida de la biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="73611-114">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="73611-115">Si lo hizo todo correctamente, debería poder agregar nuevas migraciones al proyecto.</span><span class="sxs-lookup"><span data-stu-id="73611-115">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="73611-116">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="73611-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="73611-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73611-117">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
