---
title: Escribir un proveedor de base de datos-EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 2d9e4a6cdfda80d7dfcfb6e7bf0480eb49f8e057
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414810"
---
# <a name="writing-a-database-provider"></a>Escritura de un proveedor de base de datos

Para obtener información sobre cómo escribir un proveedor de bases de datos de Entity Framework Core, consulte para [que pueda escribir un proveedor de EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) por [Arthur Vickers](https://github.com/ajcvickers).

> [!NOTE]
> Estos envíos no se han actualizado desde EF Core 1,1 y ha habido cambios significativos desde que se produjo el [error 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) al realizar un seguimiento de las actualizaciones de esta documentación.

La EF Core código base es de código abierto y contiene varios proveedores de bases de datos que se pueden utilizar como referencia. Puede encontrar el código fuente en <https://github.com/aspnet/EntityFrameworkCore>. También puede ser útil examinar el código para proveedores de terceros de uso frecuente, como [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)y [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact). En concreto, estos proyectos se configuran para extender desde y ejecutar pruebas funcionales que publicamos en NuGet. Se recomienda encarecidamente este tipo de instalación.

## <a name="keeping-up-to-date-with-provider-changes"></a>Mantenerse al día con los cambios del proveedor

A partir del trabajo después de la versión 2,1, hemos creado un [registro de cambios](provider-log.md) que pueden necesitar cambios correspondientes en el código del proveedor. Esto está pensado para ayudarle a actualizar un proveedor existente para que funcione con una nueva versión de EF Core.

Antes de 2,1, usamos las etiquetas [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) y [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) en nuestros problemas de Github y solicitudes de incorporación de cambios para un propósito similar. Vamos a usar estas etiquetas en los problemas para proporcionar una indicación de qué elementos de trabajo de una versión determinada también pueden requerir que el trabajo se realice en los proveedores. Una etiqueta de `providers-beware` normalmente significa que la implementación de un elemento de trabajo puede interrumpir los proveedores, mientras que una etiqueta de `providers-fyi` normalmente significa que los proveedores no se interrumpirán, pero puede que sea necesario cambiar el código, por ejemplo, para habilitar la nueva funcionalidad.

## <a name="suggested-naming-of-third-party-providers"></a>Nombres sugeridos de proveedores de terceros

Se recomienda usar la siguiente nomenclatura para los paquetes NuGet. Esto es coherente con los nombres de los paquetes proporcionados por el equipo de EF Core.

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

Por ejemplo:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
