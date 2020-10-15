---
title: 'Herencia: EF Core'
description: Cómo configurar la herencia de tipos de entidad mediante Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 47aae0d57d7203f0e6da5868bdc082ad85d59620
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063873"
---
# <a name="inheritance"></a>Herencia

EF puede asignar una jerarquía de tipos .NET a una base de datos. Esto le permite escribir las entidades .NET en el código como de costumbre, con los tipos base y derivados, y hacer que EF cree sin problemas el esquema de base de datos adecuado, las consultas de problemas, etc. Los detalles reales de cómo se asigna una jerarquía de tipos dependen del proveedor; en esta página se describe la compatibilidad de herencia en el contexto de una base de datos relacional.

## <a name="entity-type-hierarchy-mapping"></a>Asignación de jerarquía de tipos de entidad

Por Convención, EF no buscará automáticamente tipos base o derivados; Esto significa que si desea que se asigne un tipo CLR en la jerarquía, debe especificar explícitamente ese tipo en el modelo. Por ejemplo, si solo se especifica el tipo base de una jerarquía, EF Core incluirá implícitamente todos sus subtipos.

En el ejemplo siguiente se expone un DbSet para `Blog` y su subclase `RssBlog` . Si `Blog` tiene cualquier otra subclase, no se incluirá en el modelo.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> Las columnas de la base de datos se convierten en NULL automáticamente según sea necesario al usar la asignación TPH. Por ejemplo, la `RssUrl` columna acepta valores NULL porque `Blog` las instancias normales no tienen esa propiedad.

Si no desea exponer un `DbSet` para una o más entidades de la jerarquía, también puede usar la API fluida para asegurarse de que se incluyen en el modelo.

> [!TIP]
> Si no se basa en las convenciones, puede especificar el tipo base explícitamente mediante `HasBaseType` . También puede usar `.HasBaseType((Type)null)` para quitar un tipo de entidad de la jerarquía.

## <a name="table-per-hierarchy-and-discriminator-configuration"></a>Configuración de tabla por jerarquía y discriminador

De forma predeterminada, EF asigna la herencia mediante el patrón *de tabla por jerarquía* (TPH). TPH usa una sola tabla para almacenar los datos de todos los tipos de la jerarquía y se usa una columna de discriminador para identificar qué tipo representa cada fila.

El modelo anterior se asigna al siguiente esquema de la base de datos (tenga en cuenta la columna creado implícitamente `Discriminator` , que identifica qué tipo de `Blog` se almacena en cada fila).

![imagen](_static/inheritance-tph-data.png)

Puede configurar el nombre y el tipo de la columna discriminadora y los valores que se usan para identificar cada tipo en la jerarquía:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

En los ejemplos anteriores, EF agregó el discriminador implícitamente como una [propiedad Shadow](xref:core/modeling/shadow-properties) en la entidad base de la jerarquía. Esta propiedad se puede configurar como cualquier otra:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

Por último, el discriminador también se puede asignar a una propiedad .NET normal en la entidad:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

### <a name="shared-columns"></a>Columnas compartidas

De forma predeterminada, cuando dos tipos de entidad del mismo nivel en la jerarquía tienen una propiedad con el mismo nombre, se asignarán a dos columnas independientes. Sin embargo, si su tipo es idéntico, se pueden asignar a la misma columna de base de datos:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a>Configuración de tabla por tipo

> [!NOTE]
> La tabla por tipo (TPT) es una nueva característica de EF Core 5,0. La tabla por tipo específico (TPC) es compatible con EF6, pero aún no es compatible con EF Core.

En el patrón de asignación de TPT, todos los tipos se asignan a tablas individuales. Las propiedades que pertenecen solamente a un tipo base o a un tipo derivado se almacenan en una tabla que se asigna a ese tipo. Las tablas que se asignan a tipos derivados también almacenan una clave externa que une la tabla derivada con la tabla base.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

EF creará el siguiente esquema de la base de datos para el modelo anterior.

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> Si se cambia el nombre de la restricción primary key, el nuevo nombre se aplicará a todas las tablas asignadas a la jerarquía, las versiones futuras de EF permitirán cambiar el nombre de la restricción solo para una tabla determinada cuando se corrija el [problema 19970](https://github.com/dotnet/efcore/issues/19970) .

Si está empleando la configuración masiva, puede recuperar el nombre de columna de una tabla concreta mediante una llamada a <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
