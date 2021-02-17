---
title: 'Asignaciones de función: proveedor de Azure Cosmos DB-EF Core'
description: Asignaciones de función del proveedor de EF Core de Azure Cosmos DB
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543593"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Asignaciones de función del proveedor de EF Core de Azure Cosmos DB

En esta página se muestran los miembros de .NET que se traducen en SQL Functions cuando se usa el proveedor de Azure Cosmos DB.

.NET                          | SQL                              | Agregado en
----------------------------- | -------------------------------- | --------
Collection. Contains (Item)     | @item DE @collection
EF. Functions. RANDOM ()         | RAND ()                           | EF Core 6.0
Valorstring. Contains (valor)   | Contains ( @stringValue , @value )   | EF Core 5.0
Valorstring. EndsWith (valor)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
Valorstring. FirstOrDefault ()  | IZQUIERDA ( @stringValue , 1)            | EF Core 5.0
Valorstring. LastOrDefault ()   | RIGHT ( @stringValue , 1)           | EF Core 5.0
Valorstring. StartsWith (valor) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
