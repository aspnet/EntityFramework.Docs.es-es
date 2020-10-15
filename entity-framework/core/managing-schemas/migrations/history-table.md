---
title: 'Tabla de historial de migraciones personalizadas: EF Core'
description: Personalización de una tabla de historial que se usará para las migraciones con Entity Framework Core
author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 1f6dcb2fbb20e7dafed17160832ccec453839aa3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062313"
---
# <a name="custom-migrations-history-table"></a>Tabla de historial de migraciones personalizadas

De forma predeterminada, EF Core realiza un seguimiento de las migraciones que se han aplicado a la base de datos mediante su grabación en una tabla denominada `__EFMigrationsHistory` . Por varias razones, puede que desee personalizar esta tabla para satisfacer mejor sus necesidades.

> [!IMPORTANT]
> Si personaliza la tabla de historial de migraciones *después* de aplicar las migraciones, es responsable de actualizar la tabla existente en la base de datos.

## <a name="schema-and-table-name"></a>Esquema y nombre de tabla

Puede cambiar el nombre de esquema y de tabla mediante el `MigrationsHistoryTable()` método de `OnConfiguring()` (o `ConfigureServices()` en ASP.net Core). Este es un ejemplo del uso del proveedor de EF Core de SQL Server.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a>Otros cambios

Para configurar aspectos adicionales de la tabla, invalide y reemplace el servicio específico del proveedor `IHistoryRepository` . Este es un ejemplo de cómo cambiar el nombre de la columna MigrationId a *ID* en SQL Server.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> `SqlServerHistoryRepository` está dentro de un espacio de nombres interno y puede cambiar en futuras versiones.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
