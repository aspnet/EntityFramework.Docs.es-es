---
title: Novedades de EF Core 1.1 - EF Core
description: Cambios y mejoras en Entity Framework Core 1.1
author: ajcvickers
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: a865270d0b1b690ed2596e7ed550463ab0877bca
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063470"
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
