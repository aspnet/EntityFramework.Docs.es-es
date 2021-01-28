---
title: 'Proveedor de base de datos de Microsoft SQL Server: generación de valores-EF Core'
description: Patrones de generación de valores específicos del proveedor de bases de datos de SQL Server Entity Framework Core
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987143"
---
# <a name="sql-server-value-generation"></a>Generación de SQL Server valor

En esta página se detallan los patrones y la configuración de generación de valores específicos del proveedor de SQL Server. Se recomienda leer primero [la página general en la generación de valores](xref:core/modeling/generated-properties).

## <a name="identity-columns"></a>Columnas IDENTITY

Por Convención, las columnas numéricas que están configuradas para que los valores generados en Add se configuran como [SQL Server columnas de identidad](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).

### <a name="seed-and-increment"></a>Inicialización e incremento

De forma predeterminada, las columnas de identidad se inician en 1 (el valor de inicialización) y se incrementan en 1 cada vez que se agrega una fila (el incremento). Puede configurar una inicialización y un incremento diferentes como se indica a continuación:

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> La capacidad de configurar el incremento e inicialización de identidad se presentó en EF Core 3,0.

### <a name="inserting-explicit-values-into-identity-columns"></a>Insertar valores explícitos en columnas de identidad

De forma predeterminada, SQL Server no permite insertar valores explícitos en columnas de identidad. Para ello, debe habilitarlo manualmente `IDENTITY_INSERT` antes de llamar a `SaveChanges()` , como se indica a continuación:

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> Tenemos una [solicitud de característica](https://github.com/aspnet/EntityFramework/issues/703) en el trabajo pendiente para hacer esto de manera automática dentro del proveedor de SQL Server.

## <a name="sequences"></a>Secuencias

Como alternativa a las columnas de identidad, puede usar secuencias estándar. Esto puede ser útil en varios escenarios; por ejemplo, puede que desee que varias columnas dibuje sus valores predeterminados de una sola secuencia.

SQL Server permite crear secuencias y usarlas tal y como se detalla en [la página general en secuencias](xref:core/modeling/sequences). Depende de usted configurar las propiedades para usar secuencias a través de `HasDefaultValueSql()` .
