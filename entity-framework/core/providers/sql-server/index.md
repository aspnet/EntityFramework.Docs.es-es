---
title: Proveedor de base de datos de Microsoft SQL Server - EF Core
description: Documentación del proveedor de bases de datos que permite usar Entity Framework Core con Microsoft SQL Server.
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 4118cd5737ece1ad084bb85d409523d217065353
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430331"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="7dc41-103">Proveedor de base de datos de Microsoft SQL Server para EF Core</span><span class="sxs-lookup"><span data-stu-id="7dc41-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="7dc41-104">Este proveedor de base de datos permite usar Entity Framework Core con Microsoft SQL Server (incluido Azure SQL Database).</span><span class="sxs-lookup"><span data-stu-id="7dc41-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="7dc41-105">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="7dc41-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="7dc41-106">Instalar</span><span class="sxs-lookup"><span data-stu-id="7dc41-106">Install</span></span>

<span data-ttu-id="7dc41-107">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="7dc41-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7dc41-108">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="7dc41-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="7dc41-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dc41-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="7dc41-110">A partir de la versión 3.0.0, el proveedor hace referencia a Microsoft.Data.SqlClient (las versiones anteriores dependían de System.Data.SqlClient).</span><span class="sxs-lookup"><span data-stu-id="7dc41-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="7dc41-111">Si su proyecto depende directamente de SqlClient, asegúrese de que haga referencia al paquete Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="7dc41-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="7dc41-112">Motores de base de datos compatibles</span><span class="sxs-lookup"><span data-stu-id="7dc41-112">Supported Database Engines</span></span>

* <span data-ttu-id="7dc41-113">Microsoft SQL Server (de 2012 en adelante)</span><span class="sxs-lookup"><span data-stu-id="7dc41-113">Microsoft SQL Server (2012 onwards)</span></span>
