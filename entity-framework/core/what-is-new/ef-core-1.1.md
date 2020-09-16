---
title: Novedades de EF Core 1.1 - EF Core
description: Cambios y mejoras en Entity Framework Core 1.1
author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 89dfe4b4772e9e6dc232860f38f07c94654af437
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618816"
---
# <a name="new-features-in-ef-core-11"></a>Características nuevas en EF Core 1.1

## <a name="modeling"></a>Modelado

### <a name="field-mapping"></a>Asignación de campos

Permite configurar un campo de respaldo para una propiedad. Puede resultar útil en las propiedades de solo lectura o en los datos que tienen métodos Get/Set en lugar de una propiedad.

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a>Asignación a tablas optimizadas para memoria en SQL Server

Puede especificar que la tabla a la que está asignada una entidad está optimizada para memoria. Cuando use EF Core para crear y mantener una base de datos basada en el modelo (ya sea con migraciones o `Database.EnsureCreated()`), se creará una tabla optimizada para memoria para estas entidades.

## <a name="change-tracking"></a>seguimiento de cambios

### <a name="additional-change-tracking-apis-from-ef6"></a>API adicionales de seguimiento de cambios de EF6

Como `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.

## <a name="query"></a>Consultar

### <a name="explicit-loading"></a>Carga explícita

Permite desencadenar el rellenado de una propiedad de navegación o una entidad que se cargó anteriormente a partir de la base de datos.

### <a name="dbsetfind"></a>DbSet.Find

Proporciona una manera sencilla de capturar una entidad en función de su valor de clave principal.

## <a name="other"></a>Otros

### <a name="connection-resiliency"></a>Resistencia de la conexión

Reintenta automáticamente los comandos de base de datos erróneos. Esto resulta especialmente útil cuando se realizan conexiones a SQL Azure, donde los errores transitorios son comunes.

### <a name="simplified-service-replacement"></a>Reemplazo de servicio simplificado

Facilita el reemplazo de servicios internos que EF usa.
