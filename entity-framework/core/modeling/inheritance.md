---
title: 'Herencia: EF Core'
description: Cómo configurar la herencia de tipos de entidad mediante Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664057"
---
# <a name="inheritance"></a>Herencia

EF puede asignar una jerarquía de tipos .NET a una base de datos. Esto le permite escribir las entidades .NET en el código como de costumbre, con los tipos base y derivados, y hacer que EF cree sin problemas el esquema de base de datos adecuado, las consultas de problemas, etc. Los detalles reales de cómo se asigna una jerarquía de tipos dependen del proveedor; en esta página se describe la compatibilidad de herencia en el contexto de una base de datos relacional.

En este momento, EF Core solo admite el patrón de tabla por jerarquía (TPH). TPH usa una sola tabla para almacenar los datos de todos los tipos de la jerarquía y se usa una columna de discriminador para identificar qué tipo representa cada fila.

> [!NOTE]
> Los EF Core no admiten la tabla por tipo (TPT) y la tabla por tipo específico (TPC), que son compatibles con EF6. TPT es una característica importante planeada para EF Core 5,0.

## <a name="entity-type-hierarchy-mapping"></a>Asignación de jerarquía de tipos de entidad

Por Convención, EF no buscará automáticamente tipos base o derivados; Esto significa que si desea que se asigne un tipo CLR en la jerarquía, debe especificar explícitamente ese tipo en el modelo. Por ejemplo, si solo se especifica el tipo base de una jerarquía, EF Core incluirá implícitamente todos sus subtipos.

En el ejemplo siguiente se expone un DbSet para `Blog` y su subclase `RssBlog` . Si `Blog` tiene cualquier otra subclase, no se incluirá en el modelo.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

Este modelo se asigna al siguiente esquema de la base de datos (tenga en cuenta la columna *discriminadora* creada implícitamente, que identifica qué tipo de *blog* se almacena en cada fila):

![imagen](_static/inheritance-tph-data.png)

>[!NOTE]
> Las columnas de la base de datos se convierten en NULL automáticamente según sea necesario al usar la asignación TPH. Por ejemplo, la columna *RssUrl* admite valores NULL porque las instancias de *blog* normales no tienen esa propiedad.

Si no desea exponer un DbSet para una o varias entidades de la jerarquía, también puede usar la API fluida para asegurarse de que se incluyen en el modelo.

> [!TIP]
> Si no se basa en las convenciones, puede especificar el tipo base explícitamente mediante `HasBaseType` . También puede usar `.HasBaseType((Type)null)` para quitar un tipo de entidad de la jerarquía.

## <a name="discriminator-configuration"></a>Configuración de discriminador

Puede configurar el nombre y el tipo de la columna discriminadora y los valores que se usan para identificar cada tipo en la jerarquía:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

En los ejemplos anteriores, EF agregó el discriminador implícitamente como una [propiedad Shadow](xref:core/modeling/shadow-properties) en la entidad base de la jerarquía. Esta propiedad se puede configurar como cualquier otra:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

Por último, el discriminador también se puede asignar a una propiedad .NET normal en la entidad:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a>Columnas compartidas

De forma predeterminada, cuando dos tipos de entidad del mismo nivel en la jerarquía tienen una propiedad con el mismo nombre, se asignarán a dos columnas independientes. Sin embargo, si su tipo es idéntico, se pueden asignar a la misma columna de base de datos:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
