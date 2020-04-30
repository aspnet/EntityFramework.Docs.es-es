---
title: 'Prueba con la base de datos de EF en memoria: EF Core'
author: ajcvickers
description: Uso de la base de datos de EF en memoria para probar una aplicación EF Core
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538347"
---
# <a name="testing-with-the-ef-in-memory-database"></a>Pruebas con la base de datos en memoria de EF

> [!WARNING]
> La base de datos en memoria de EF a menudo se comporta de forma diferente a las bases de datos relacionales.
> Use la base de datos en memoria de EF solo después de comprender totalmente los problemas y las ventajas y desventajas que conlleva, como se describe en [probar el código que usa EF Core](xref:core/miscellaneous/testing/index).  

> [!TIP]
> SQLite es un proveedor relacional y también puede usar bases de datos en memoria.
> Considere la posibilidad de utilizarlo para realizar pruebas con el fin de encontrar más coincidencia con los comportamientos comunes de la base de datos
> Esto se trata en el [uso de SQLite para probar una aplicación EF Core](xref:core/miscellaneous/testing/sqlite).   

La información de esta página se encuentra ahora en otras ubicaciones:
* Vea [código de prueba que usa EF Core](xref:core/miscellaneous/testing/index) para obtener información general sobre las pruebas con la base de datos en memoria de EF.
* Vea el [ejemplo que muestra cómo probar las aplicaciones que usan EF Core](xref:core/miscellaneous/testing/testing-sample) para obtener un ejemplo que usa la base de datos en memoria de EF.
* Consulte [el proveedor de base de datos de EF en memoria](xref:core/providers/in-memory/index) para obtener información general sobre la base de datos en memoria de EF.
