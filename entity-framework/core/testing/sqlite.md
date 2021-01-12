---
title: Pruebas con SQLite-EF Core
description: Uso de SQLite para probar una aplicación Entity Framework Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: da2504cfe7997a10a5ee8c447b1c6ef00dd02369
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129062"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a>Uso de SQLite para probar una aplicación EF Core

> [!WARNING]
> El uso de SQLite puede ser una manera eficaz de probar una aplicación EF Core.
> Sin embargo, pueden surgir problemas en los que SQLite se comporta de forma diferente a otros sistemas de base de datos.
> Vea [código de prueba que usa EF Core](xref:core/testing/index) para obtener una explicación de los problemas y las ventajas.

Este documento se basa en los conceptos presentados en el [ejemplo que muestra cómo probar las aplicaciones que usan EF Core](xref:core/testing/testing-sample).
Los ejemplos de código que se muestran aquí provienen de este ejemplo.

## <a name="using-sqlite-in-memory-databases"></a>Usar bases de datos en memoria de SQLite

Normalmente, SQLite crea bases de datos como archivos simples y accede al archivo en proceso con la aplicación.
Esto es muy rápido, especialmente cuando se usa una [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)rápida.

SQLite también puede usar bases de datos creadas exclusivamente en memoria.
Esto es fácil de usar con EF Core siempre que comprenda la duración de la base de datos en memoria:

* La base de datos se crea cuando se abre la conexión con ella
* La base de datos se elimina cuando se cierra la conexión con ella.

EF Core usará una conexión que ya está abierta cuando se le proporcione una y nunca intentará cerrarla.
Por lo tanto, la clave para usar EF Core con una base de datos SQLite en memoria es abrir la conexión antes de pasarla a EF.

En el [ejemplo](xref:core/testing/testing-sample) se consigue con el código siguiente:

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

Aviso:

* El `CreateInMemoryDatabase` método crea una base de datos en memoria de SQLite y abre la conexión con ella.
* El creado `DbConnection` se extrae de `ContextOptions` y se guarda.
* La conexión se elimina cuando se elimina la prueba para que no se pierdan los recursos.

> [!NOTE]
> [Problema #16103](https://github.com/dotnet/efcore/issues/16103) es el seguimiento de las formas de facilitar esta administración de conexiones.
