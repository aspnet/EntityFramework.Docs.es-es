---
title: 'EF6 y EF Core: uso en la misma aplicación'
description: Instrucciones sobre el uso de Entity Framework Core y Entity Framework 6 en la misma aplicación
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: 9bd3d837a333eb23be4cb3095b7f387ad303376d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619545"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="6ac57-103">Uso de EF Core y EF6 en la misma aplicación</span><span class="sxs-lookup"><span data-stu-id="6ac57-103">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="6ac57-104">Es posible usar EF Core y EF6 en la misma biblioteca o aplicación al instalar ambos paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="6ac57-104">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="6ac57-105">Algunos tipos tienen los mismos nombres en EF Core y EF6 y solo difieren en el espacio de nombres, lo que puede complicar el uso de EF Core y EF6 en el mismo archivo de código.</span><span class="sxs-lookup"><span data-stu-id="6ac57-105">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="6ac57-106">La ambigüedad se puede eliminar fácilmente con directivas de alias de espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="6ac57-106">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="6ac57-107">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6ac57-107">For example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="6ac57-108">Si traslada una aplicación existente que tiene varios modelos de EF, puede elegir trasladar de manera selectiva algunos de ellos a EF Core y seguir usando EF6 para los demás.</span><span class="sxs-lookup"><span data-stu-id="6ac57-108">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
