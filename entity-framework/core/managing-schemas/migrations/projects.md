---
title: 'Uso de un proyecto de migración independiente: EF Core'
description: Uso de un proyecto de migración independiente para administrar esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 9a6b8977f9d7bcdae0fb9aea6966a7eb43e9e7db
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024152"
---
# <a name="using-a-separate-migrations-project"></a>Uso de un proyecto de migración independiente

Puede que desee almacenar las migraciones en un proyecto diferente del que contiene su `DbContext` . También puede usar esta estrategia para mantener varios conjuntos de migraciones, por ejemplo, una para el desarrollo y otra para las actualizaciones de lanzamiento a lanzamiento.

> [!TIP]
> Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations) de este artículo.

## <a name="steps"></a>Pasos

1. Cree una nueva biblioteca de clases.

2. Agregue una referencia al proyecto DbContext.

3. Mueva las migraciones y los archivos de instantáneas de modelo a la biblioteca de clases.
   > [!TIP]
   > Si no tiene ninguna migración existente, genere una en el proyecto que contiene el DbContext y muévala.
   > Esto es importante porque si el proyecto de migraciones no contiene una migración existente, el comando Add-Migration no podrá encontrar DbContext.

4. Configure el ensamblado de migraciones:

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. Agregue una referencia al proyecto de migraciones desde el proyecto de **Inicio** .

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   Si esto provoca una dependencia circular, puede actualizar en su lugar la ruta de acceso de salida base del proyecto de **migraciones** :

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

Si lo hizo todo correctamente, debería poder agregar nuevas migraciones al proyecto.

## <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
