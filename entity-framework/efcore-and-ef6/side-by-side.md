---
title: 'EF6 y EF Core: uso en la misma aplicación'
description: Instrucciones sobre el uso de Entity Framework Core y Entity Framework 6 en la misma aplicación
author: ajcvickers
ms.date: 01/23/2019
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: a9a8ab0ec77acf0fb1d1b1408d5711a6c8fa6664
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073531"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>Uso de EF Core y EF6 en la misma aplicación

Es posible usar EF Core y EF6 en la misma biblioteca o aplicación al instalar ambos paquetes NuGet.

Algunos tipos tienen los mismos nombres en EF Core y EF6 y solo difieren en el espacio de nombres, lo que puede complicar el uso de EF Core y EF6 en el mismo archivo de código. La ambigüedad se puede eliminar fácilmente con directivas de alias de espacios de nombres. Por ejemplo:

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

Si traslada una aplicación existente que tiene varios modelos de EF, puede elegir trasladar de manera selectiva algunos de ellos a EF Core y seguir usando EF6 para los demás.
