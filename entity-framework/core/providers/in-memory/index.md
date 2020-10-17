---
title: Proveedor de base de datos InMemory - EF Core
description: Información sobre el proveedor de base de datos InMemory de Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1857ebbfa0eded1572220825a5b0d75961bcf3dd
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064016"
---
# <a name="ef-core-in-memory-database-provider"></a>Proveedor de base de datos InMemory para EF Core

Este proveedor de base de datos permite usar Entity Framework Core con una base de datos en memoria. La base de datos en memoria puede resultar útil para realizar pruebas, aunque es posible que el proveedor SQLite del modo en memoria sea un reemplazo de pruebas más adecuado para bases de datos relacionales. La base de datos en memoria está diseñada únicamente para realizar pruebas. Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

## <a name="install"></a>Instalar

Instale el [paquete NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a>Introducción

Los siguientes recursos le ayudarán a empezar a trabajar con este proveedor.

* [Pruebas con InMemory](xref:core/miscellaneous/testing/in-memory)
* [Pruebas de la aplicación de ejemplo UnicornStore](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>Motores de base de datos compatibles

Base de datos de memoria en proceso, diseñada únicamente para realizar pruebas.
