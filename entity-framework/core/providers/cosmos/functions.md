---
title: 'Asignaciones de función: proveedor de Azure Cosmos DB-EF Core'
description: Asignaciones de función del proveedor de EF Core de Azure Cosmos DB
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066537"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Asignaciones de función del proveedor de EF Core de Azure Cosmos DB

En esta página se muestran los miembros de .NET que se traducen en SQL Functions cuando se usa el proveedor de Azure Cosmos DB.

.NET                          | SQL                              | Agregado en
----------------------------- | -------------------------------- | --------
Collection. Contains (Item)     | @item DE @collection
Valorstring. Contains (valor)   | Contains ( @stringValue , @value )   | EF Core 5.0
Valorstring. EndsWith (valor)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
Valorstring. FirstOrDefault ()  | IZQUIERDA ( @stringValue , 1)            | EF Core 5.0
Valorstring. LastOrDefault ()   | RIGHT ( @stringValue , 1)           | EF Core 5.0
Valorstring. StartsWith (valor) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
