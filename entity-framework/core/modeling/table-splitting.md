---
title: 'División de tablas: EF Core'
description: Cómo configurar la división de tablas mediante Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: 43261ea0dab57528ab14faf7f455c0ed72d1bfc3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023931"
---
# <a name="table-splitting"></a>División de tablas

EF Core permite asignar dos o más entidades a una sola fila. Esto se denomina _División de tablas_ o _uso compartido de tablas_.

## <a name="configuration"></a>Configuración

Para usar la división de tablas, los tipos de entidad deben asignarse a la misma tabla, tener las claves principales asignadas a las mismas columnas y al menos una relación configurada entre la clave principal de un tipo de entidad y otra en la misma tabla.

Un escenario común para la división de tablas es usar solo un subconjunto de las columnas de la tabla para un mayor rendimiento o encapsulación.

En este ejemplo `Order` representa un subconjunto de `DetailedOrder` .

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

Además de la configuración necesaria, llamamos `Property(o => o.Status).HasColumnName("Status")` a para asignarla `DetailedOrder.Status` a la misma columna que `Order.Status` .

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> Vea el [proyecto de ejemplo completo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/TableSplitting) para obtener más contexto.

## <a name="usage"></a>Uso

Guardar y consultar entidades mediante la división de tablas se realiza de la misma manera que otras entidades:

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>Entidad dependiente opcional

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 3.0.

Si todas las columnas utilizadas por una entidad dependiente están `NULL` en la base de datos, no se creará ninguna instancia para ella cuando se realice la consulta. Esto permite el modelado de una entidad dependiente opcional, donde la propiedad Relationship de la entidad de seguridad sería null. Tenga en cuenta que esto también ocurrirá si todas las propiedades del dependiente son opcionales y se establecen en `null` , lo que podría no ser el esperado.

## <a name="concurrency-tokens"></a>Tokens de simultaneidad

Si alguno de los tipos de entidad que comparten una tabla tiene un token de simultaneidad, también debe incluirse en todos los demás tipos de entidad. Esto es necesario para evitar un valor de token de simultaneidad obsoleto cuando solo se actualiza una de las entidades asignadas a la misma tabla.

Para evitar exponer el token de simultaneidad al código utilizado, es posible crear uno como una [propiedad de sombra](xref:core/modeling/shadow-properties):

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
