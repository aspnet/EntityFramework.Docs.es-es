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
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="c7fd1-103">Uso de EF Core y EF6 en la misma aplicación</span><span class="sxs-lookup"><span data-stu-id="c7fd1-103">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="c7fd1-104">Es posible usar EF Core y EF6 en la misma biblioteca o aplicación al instalar ambos paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="c7fd1-104">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="c7fd1-105">Algunos tipos tienen los mismos nombres en EF Core y EF6 y solo difieren en el espacio de nombres, lo que puede complicar el uso de EF Core y EF6 en el mismo archivo de código.</span><span class="sxs-lookup"><span data-stu-id="c7fd1-105">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="c7fd1-106">La ambigüedad se puede eliminar fácilmente con directivas de alias de espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="c7fd1-106">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="c7fd1-107">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c7fd1-107">For example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="c7fd1-108">Si traslada una aplicación existente que tiene varios modelos de EF, puede elegir trasladar de manera selectiva algunos de ellos a EF Core y seguir usando EF6 para los demás.</span><span class="sxs-lookup"><span data-stu-id="c7fd1-108">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
