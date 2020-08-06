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
# <a name="asynchronous-saving"></a>Guardado asincrónico

El guardado asincrónico evita bloquear un subproceso mientras se escriben los cambios en la base de datos. Esto puede resultar útil para evitar la inmovilización de la interfaz de usuario de una aplicación cliente pesada. Las operaciones asincrónicas también pueden aumentar el rendimiento de una aplicación web, donde se puede liberar el subproceso para atender otras solicitudes mientras se completa la operación de la base de datos. Para más información, consulte [Programación asincrónica en C#](/dotnet/csharp/async).

> [!WARNING]  
> EF Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de contexto. Siempre debe esperar que se complete una operación antes de iniciar la siguiente. Habitualmente, para esto se usa la palabra clave `await` en cada una de las operaciones asincrónicas.

Entity Framework Core proporciona `DbContext.SaveChangesAsync()` como una alternativa asincrónica para `DbContext.SaveChanges()`.

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
