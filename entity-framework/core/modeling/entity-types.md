---
title: 'Tipos de entidad: EF Core'
description: Cómo configurar y asignar tipos de entidad mediante Entity Framework Core
author: roji
ms.date: 12/03/2019
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/entity-types
ms.openlocfilehash: b3d9ad753637d021d9aa52965da38091ae690f77
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502418"
---
# <a name="entity-types"></a>Tipos de entidad

La inclusión de un DbSet de un tipo en el contexto significa que se incluye en el modelo de EF Core. normalmente hacemos referencia a este tipo como una *entidad*. EF Core puede leer y escribir instancias de entidad desde y hacia la base de datos, y si está utilizando una base de datos relacional, EF Core puede crear tablas para las entidades a través de migraciones.

## <a name="including-types-in-the-model"></a>Incluir tipos en el modelo

Por Convención, los tipos que se exponen en las propiedades de DbSet en el contexto se incluyen en el modelo como entidades. También se incluyen los tipos de entidad que se especifican en el método `OnModelCreating`, al igual que los tipos que se encuentran al explorar de forma recursiva las propiedades de navegación de otros tipos de entidades detectadas.

En el ejemplo de código siguiente, se incluyen todos los tipos:

* `Blog` se incluye porque se expone en una propiedad DbSet en el contexto.
* `Post` se incluye porque se detecta a través de la propiedad de navegación `Blog.Posts`.
* `AuditEntry` porque se especifica en `OnModelCreating`.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>Excluir tipos del modelo

Si no desea incluir un tipo en el modelo, puede excluirlo:

### <a name="data-annotationstabdata-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a>Nombre de tabla

Por Convención, cada tipo de entidad se configurará para asignarse a una tabla de base de datos con el mismo nombre que la propiedad DbSet que expone la entidad. Si no existe ningún DbSet para la entidad especificada, se utiliza el nombre de clase.

Puede configurar manualmente el nombre de la tabla:

### <a name="data-annotationstabdata-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>Esquema de tabla

Al utilizar una base de datos relacional, las tablas se crean por Convención en el esquema predeterminado de la base de datos. Por ejemplo, Microsoft SQL Server usará el esquema de `dbo` (SQLite no admite esquemas).

Puede configurar las tablas que se van a crear en un esquema específico de la siguiente manera:

### <a name="data-annotationstabdata-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

En lugar de especificar el esquema de cada tabla, también puede definir el esquema predeterminado en el nivel de modelo con la API fluida:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

Tenga en cuenta que al establecer el esquema predeterminado también se verán afectados otros objetos de base de datos, como las secuencias.
