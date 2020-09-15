---
title: Proveedor de base de datos SQLite - EF Core
description: Información sobre el proveedor de base de datos SQLite de Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: ba537acdf537fa475378c08b8c6290930b29239d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071243"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="8e99d-103">Proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="8e99d-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="8e99d-104">Este proveedor de base de datos permite usar Entity Framework Core con SQLite.</span><span class="sxs-lookup"><span data-stu-id="8e99d-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="8e99d-105">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="8e99d-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="8e99d-106">Instalar</span><span class="sxs-lookup"><span data-stu-id="8e99d-106">Install</span></span>

<span data-ttu-id="8e99d-107">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="8e99d-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8e99d-108">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="8e99d-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="8e99d-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e99d-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="8e99d-110">Motores de base de datos compatibles</span><span class="sxs-lookup"><span data-stu-id="8e99d-110">Supported Database Engines</span></span>

* <span data-ttu-id="8e99d-111">SQLite (3.7 y superiores)</span><span class="sxs-lookup"><span data-stu-id="8e99d-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="8e99d-112">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="8e99d-112">Limitations</span></span>

<span data-ttu-id="8e99d-113">Vea [Limitaciones de SQLite](xref:core/providers/sqlite/limitations) para conocer algunas limitaciones importantes del proveedor de SQLite.</span><span class="sxs-lookup"><span data-stu-id="8e99d-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
