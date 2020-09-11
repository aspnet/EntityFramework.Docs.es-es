---
title: Proveedor de base de datos SQLite - EF Core
description: Información sobre el proveedor de base de datos SQLite de Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 8620f0e37825368cb3d7965a05118ab1297fb9e8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616558"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="c3063-103">Proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="c3063-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="c3063-104">Este proveedor de base de datos permite usar Entity Framework Core con SQLite.</span><span class="sxs-lookup"><span data-stu-id="c3063-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="c3063-105">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="c3063-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="c3063-106">Instalar</span><span class="sxs-lookup"><span data-stu-id="c3063-106">Install</span></span>

<span data-ttu-id="c3063-107">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="c3063-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c3063-108">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c3063-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="c3063-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3063-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="c3063-110">Motores de base de datos compatibles</span><span class="sxs-lookup"><span data-stu-id="c3063-110">Supported Database Engines</span></span>

* <span data-ttu-id="c3063-111">SQLite (3.7 y superiores)</span><span class="sxs-lookup"><span data-stu-id="c3063-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="c3063-112">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="c3063-112">Limitations</span></span>

<span data-ttu-id="c3063-113">Vea [Limitaciones de SQLite](xref:core/providers/sqlite/limitations) para conocer algunas limitaciones importantes del proveedor de SQLite.</span><span class="sxs-lookup"><span data-stu-id="c3063-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
