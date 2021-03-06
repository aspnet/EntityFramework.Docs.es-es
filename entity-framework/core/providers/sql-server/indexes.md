---
title: 'Proveedor de base de datos de Microsoft SQL Server: índices EF Core'
description: Características de índice específicas del proveedor de SQL Server de Entity Framework Core
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129166"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a>Características de índice específicas del proveedor de SQL Server de Entity Framework Core

En esta página se detallan las opciones de configuración del índice que son específicas del proveedor de SQL Server.

## <a name="clustering"></a>Agrupación en clústeres

Los índices clúster ordenan y almacenan las filas de los datos de la tabla o vista de acuerdo con los valores de la clave del índice. La creación del índice clúster adecuado para la tabla puede mejorar significativamente la velocidad de las consultas, ya que los datos ya están dispuestos en el orden óptimo. Solo puede haber un índice clúster por cada tabla, porque las filas de datos solo pueden estar almacenadas de una forma. Para obtener más información, consulte [la documentación de SQL Server sobre los índices agrupados y no agrupados](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).

De forma predeterminada, la columna de clave principal de una tabla está respaldada implícitamente por un índice clúster y el resto de los índices no están agrupados.

Puede configurar un índice o una clave para agruparlos como se indica a continuación:

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> SQL Server solo admite un índice clúster por tabla y la clave principal está agrupada de forma predeterminada. Si desea tener un índice clúster en una columna que no sea de clave, debe hacer explícitamente que la clave no esté en clúster.

## <a name="fill-factor"></a>Factor de relleno

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 5.0.

La opción de factor de relleno de índice se proporciona para ajustar el rendimiento y el almacenamiento de datos de índice. Para obtener más información, consulte [la documentación de SQL Server sobre el factor de relleno](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).

Puede configurar el factor de relleno de un índice como se indica a continuación:

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a>Creación en línea

La opción ONLINE permite el acceso simultáneo de los usuarios a los datos de la tabla subyacente o del índice clúster, así como a los índices no clúster asociados durante la creación del índice, de modo que los usuarios puedan seguir actualizando y consultando los datos subyacentes. Al realizar operaciones DDL (lenguaje de definición de datos) sin conexión, como generar o volver a generar un índice clúster, estas operaciones mantienen bloqueos exclusivos de los datos subyacentes y los índices asociados. Para obtener más información, consulte [la documentación de SQL Server en la opción de índice en línea](/sql/relational-databases/indexes/perform-index-operations-online).

Puede configurar un índice con la opción ONLINE de la siguiente manera:

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
