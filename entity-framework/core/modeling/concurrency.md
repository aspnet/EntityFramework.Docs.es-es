---
title: 'Tokens de simultaneidad: EF Core'
description: Configuración de tokens de simultaneidad para el control de simultaneidad optimista en un modelo de Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: 57718a1b0146f437bb4126524889c81ae0dfd895
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617544"
---
# <a name="concurrency-tokens"></a>Tokens de simultaneidad

> [!NOTE]
> En esta página se documenta cómo configurar los tokens de simultaneidad. Vea [controlar los conflictos de simultaneidad](xref:core/saving/concurrency) para obtener una explicación detallada de cómo funciona el control de simultaneidad en EF Core y ejemplos de cómo controlar los conflictos de simultaneidad en la aplicación.

Las propiedades configuradas como tokens de simultaneidad se usan para implementar el control de simultaneidad optimista.

## <a name="configuration"></a>Configuración

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Marca de tiempo/rowversion

Timestamp/rowversion es una propiedad para la cual la base de datos genera automáticamente un nuevo valor cada vez que se inserta o se actualiza una fila. La propiedad también se trata como un token de simultaneidad, lo que garantiza que se obtiene una excepción si una fila que se está actualizando ha cambiado desde que se realizó la consulta. Los detalles precisos dependen del proveedor de base de datos utilizado; por SQL Server, normalmente se utiliza una propiedad *Byte []* , que se configurará como una columna *ROWVERSION* en la base de datos.

Puede configurar una propiedad para que sea una marca de tiempo o rowversion como se indica a continuación:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***
