---
title: 'Guardado asincrónico: EF Core'
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: aa1fbfdc44895eace04354063435f98370aed2c4
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526919"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="ecb2f-102">Guardado asincrónico</span><span class="sxs-lookup"><span data-stu-id="ecb2f-102">Asynchronous Saving</span></span>

<span data-ttu-id="ecb2f-103">El guardado asincrónico evita bloquear un subproceso mientras se escriben los cambios en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecb2f-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="ecb2f-104">Esto puede resultar útil para evitar la inmovilización de la interfaz de usuario de una aplicación cliente pesada.</span><span class="sxs-lookup"><span data-stu-id="ecb2f-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="ecb2f-105">Las operaciones asincrónicas también pueden aumentar el rendimiento de una aplicación web, donde se puede liberar el subproceso para atender otras solicitudes mientras se completa la operación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ecb2f-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="ecb2f-106">Para más información, consulte [Programación asincrónica en C#](/dotnet/csharp/async).</span><span class="sxs-lookup"><span data-stu-id="ecb2f-106">For more information, see [Asynchronous Programming in C#](/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="ecb2f-107">EF Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="ecb2f-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="ecb2f-108">Siempre debe esperar que se complete una operación antes de iniciar la siguiente.</span><span class="sxs-lookup"><span data-stu-id="ecb2f-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="ecb2f-109">Habitualmente, para esto se usa la palabra clave `await` en cada una de las operaciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="ecb2f-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="ecb2f-110">Entity Framework Core proporciona `DbContext.SaveChangesAsync()` como una alternativa asincrónica para `DbContext.SaveChanges()`.</span><span class="sxs-lookup"><span data-stu-id="ecb2f-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
