---
title: Proveedor de base de datos SQLite - EF Core
description: Información sobre el proveedor de base de datos SQLite de Entity Framework Core
author: bricelam
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: 2d221168c093271d220d4d8fbc7779c1a5aab701
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063990"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="b2143-103">Proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="b2143-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="b2143-104">Este proveedor de base de datos permite usar Entity Framework Core con SQLite.</span><span class="sxs-lookup"><span data-stu-id="b2143-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="b2143-105">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="b2143-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="b2143-106">Instalar</span><span class="sxs-lookup"><span data-stu-id="b2143-106">Install</span></span>

<span data-ttu-id="b2143-107">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="b2143-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b2143-108">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b2143-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="b2143-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2143-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="b2143-110">Motores de base de datos compatibles</span><span class="sxs-lookup"><span data-stu-id="b2143-110">Supported Database Engines</span></span>

* <span data-ttu-id="b2143-111">SQLite (3.7 y superiores)</span><span class="sxs-lookup"><span data-stu-id="b2143-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="b2143-112">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="b2143-112">Limitations</span></span>

<span data-ttu-id="b2143-113">Vea [Limitaciones de SQLite](xref:core/providers/sqlite/limitations) para conocer algunas limitaciones importantes del proveedor de SQLite.</span><span class="sxs-lookup"><span data-stu-id="b2143-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
