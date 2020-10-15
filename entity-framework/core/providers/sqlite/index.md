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
# <a name="sqlite-ef-core-database-provider"></a>Proveedor de base de datos SQLite para EF Core

Este proveedor de base de datos permite usar Entity Framework Core con SQLite. Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

## <a name="install"></a>Instalar

Instale el [paquete NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a>Motores de base de datos compatibles

* SQLite (3.7 y superiores)

## <a name="limitations"></a>Limitaciones

Vea [Limitaciones de SQLite](xref:core/providers/sqlite/limitations) para conocer algunas limitaciones importantes del proveedor de SQLite.
