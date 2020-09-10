---
title: 'Tabla de historial de migraciones personalizadas: EF Core'
description: Personalización de una tabla de historial que se usará para las migraciones con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: deed86b2023557036eb14ca5a66f04ab5bb4139f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617974"
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
