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
# <a name="sqlite-ef-core-database-provider"></a>Proveedor de base de datos SQLite para EF Core

Este proveedor de base de datos permite usar Entity Framework Core con SQLite. Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

## <a name="install"></a>Instalar

Instale el [paquete NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a>Motores de base de datos compatibles

* SQLite (3.7 y superiores)

## <a name="limitations"></a>Limitaciones

Vea [Limitaciones de SQLite](xref:core/providers/sqlite/limitations) para conocer algunas limitaciones importantes del proveedor de SQLite.
