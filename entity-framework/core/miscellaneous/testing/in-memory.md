---
title: 'Prueba con la base de datos de EF en memoria: EF Core'
description: Uso de la base de datos de EF en memoria para probar una aplicación Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619401"
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
