---
title: Programación asincrónica-EF Core
description: Consulta y almacenamiento de datos de forma asincrónica con Entity Framework Core
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: cefbe32b34a38ed6d749ef3ddfff210d5db12332
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071905"
---
# <a name="asynchronous-programming"></a>Programación asincrónica

Las operaciones asincrónicas evitan el bloqueo de un subproceso mientras la consulta se ejecuta en la base de datos. Las operaciones asincrónicas son importantes para mantener una interfaz de usuario receptiva en las aplicaciones cliente enriquecidas y también pueden aumentar el rendimiento en las aplicaciones web donde liberan el subproceso para atender otras solicitudes en aplicaciones Web.

A continuación de .NET Standard, EF Core proporciona homólogos asincrónicos a todos los métodos sincrónicos que realizan operaciones de e/s. Tienen los mismos efectos que los métodos de sincronización y se pueden usar con las `async` `await` palabras clave y C#. Por ejemplo, en lugar de usar DbContext. SaveChanges, que bloqueará un subproceso mientras se realiza la e/s de la base de datos, se puede usar DbContext. SaveChangesAsync:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

Para obtener más información, vea [los documentos generales de programación asincrónica de C#](/dotnet/csharp/async).

> [!WARNING]
> EF Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de contexto. Siempre debe esperar que se complete una operación antes de iniciar la siguiente. Habitualmente, para esto se usa la palabra clave `await` en cada una de las operaciones asincrónicas.

> [!NOTE]
> EF Core pasa los tokens de cancelación al proveedor de base de datos subyacente en uso (por ejemplo, Microsoft. Data. SqlClient). Estos tokens pueden o no ser respetados; consulte la documentación del proveedor de la base de datos.  

## <a name="async-linq-operators"></a>Operadores LINQ asincrónicos

Para permitir la ejecución de consultas LINQ de forma asincrónica, EF Core proporciona un conjunto de métodos de extensión Async que ejecutan la consulta y devuelven resultados. Estos homólogos a los operadores de LINQ estándar y sincrónicos son ToListAsync, SingleAsync, AsAsyncEnumerable, etc.:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

Tenga en cuenta que no hay versiones asincrónicas de algunos operadores LINQ, como Where o OrderBy, porque solo compilan el árbol de expresión LINQ y no hacen que la consulta se ejecute en la base de datos. Solo los operadores que causan la ejecución de la consulta tienen homólogos asincrónicos.

> [!IMPORTANT]
> Los métodos de extensión asincrónicos de EF Core se define en el espacio de nombres `Microsoft.EntityFrameworkCore`. Es necesario importar este espacio de nombres para que los métodos estén disponibles.

## <a name="client-side-async-linq-operators"></a>Operadores LINQ asincrónicos del lado cliente

Los operadores LINQ Async descritos anteriormente solo se pueden usar en consultas EF: no se pueden usar con consultas LINQ to Objects del lado cliente. Para realizar operaciones LINQ asincrónicas en el lado cliente fuera de EF, use el [paquete System. Interactive. Async](https://www.nuget.org/packages/System.Interactive.Async); Este paquete puede ser especialmente útil para realizar operaciones en el cliente que no se pueden traducir para su evaluación en el servidor.

Desafortunadamente, la referencia a System. Interactive. Async produce errores de compilación de invocación ambigua en los operadores de LINQ aplicados al DbSets de EF; Esto hace que sea difícil usar tanto EF como System. Interactive. Async en el mismo proyecto. Para solucionar este problema, agregue la función de consulta a su DbSet:

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
