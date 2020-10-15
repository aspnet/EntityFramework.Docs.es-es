---
title: Proveedor de base de datos de Microsoft SQL Server - EF Core
description: Documentación del proveedor de bases de datos que permite usar Entity Framework Core con Microsoft SQL Server.
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 344bf243577d39d2d5a03cf321f8d84c275d10d3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065334"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="012c6-103">Proveedor de base de datos de Microsoft SQL Server para EF Core</span><span class="sxs-lookup"><span data-stu-id="012c6-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="012c6-104">Este proveedor de base de datos permite usar Entity Framework Core con Microsoft SQL Server (incluido Azure SQL Database).</span><span class="sxs-lookup"><span data-stu-id="012c6-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="012c6-105">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="012c6-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="012c6-106">Instalar</span><span class="sxs-lookup"><span data-stu-id="012c6-106">Install</span></span>

<span data-ttu-id="012c6-107">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="012c6-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="012c6-108">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="012c6-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="012c6-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="012c6-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="012c6-110">A partir de la versión 3.0.0, el proveedor hace referencia a Microsoft.Data.SqlClient (las versiones anteriores dependían de System.Data.SqlClient).</span><span class="sxs-lookup"><span data-stu-id="012c6-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="012c6-111">Si su proyecto depende directamente de SqlClient, asegúrese de que haga referencia al paquete Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="012c6-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="012c6-112">Motores de base de datos compatibles</span><span class="sxs-lookup"><span data-stu-id="012c6-112">Supported Database Engines</span></span>

* <span data-ttu-id="012c6-113">Microsoft SQL Server (de 2012 en adelante)</span><span class="sxs-lookup"><span data-stu-id="012c6-113">Microsoft SQL Server (2012 onwards)</span></span>
