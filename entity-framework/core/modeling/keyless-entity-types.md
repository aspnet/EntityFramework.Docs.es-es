---
title: Tipos de entidad sin clave - EF Core
description: Cómo configurar tipos de entidad sin clave mediante Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434219"
---
# <a name="keyless-entity-types"></a>Tipos de entidad sin llave

> [!NOTE]
> Esta característica se agregó en EF Core 2.1 bajo el nombre de tipos de consulta. En EF Core 3.0, el concepto se renombró a tipos de entidad sin clave.

Además de los tipos de entidad normales, un modelo de EF Core puede contener tipos de _entidad sin clave,_ que se pueden usar para realizar consultas de base de datos con datos que no contienen valores de clave.

## <a name="defining-keyless-entity-types"></a>Definición de tipos de entidad sin clave

Los tipos de entidad sin clave se pueden definir mediante la anotación de datos o la API fluida:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Características de los tipos de entidad sin clave

Los tipos de entidad sin clave admiten muchas de las mismas capacidades de asignación que los tipos de entidad normales, como las propiedades de navegación y asignación de herencia. En los almacenes relacionales, pueden configurar los objetos y columnas de la base de datos de destino a través de métodos de API fluidos o anotaciones de datos.

Sin embargo, son diferentes de los tipos de entidad normales en que:

- No se puede tener una clave definida.
- Nunca se realiza un seguimiento de los cambios en _DbContext_ y, por lo tanto, nunca se insertan, actualizan o eliminan en la base de datos.
- Nunca son descubiertos por convención.
- Solo admite un subconjunto de capacidades de asignación de navegación, específicamente:
  - Puede que nunca actúen como el final principal de una relación.
  - Es posible que no tengan navegaciones a entidades de propiedad
  - Solo pueden contener propiedades de navegación de referencia que apunten a entidades regulares.
  - Las entidades no pueden contener propiedades de navegación a tipos de entidad sin clave.
- Es necesario configurarlo `[Keyless]` con una `.HasNoKey()` anotación de datos o una llamada de método.
- Se puede asignar a una _consulta de definición._ Una consulta de definición es una consulta declarada en el modelo que actúa como origen de datos para un tipo de entidad sin clave.

## <a name="usage-scenarios"></a>Escenarios de uso

Algunos de los principales escenarios de uso para tipos de entidad sin clave son:

- Servir como el tipo de valor devuelto para [consultas SQL sin procesar](xref:core/querying/raw-sql).
- Asignación a vistas de base de datos que no contienen una clave principal.
- Asignación a tablas que no tienen definida una clave principal.
- Asignación a consultas definidas en el modelo.

## <a name="mapping-to-database-objects"></a>Asignación a objetos de base de datos

La asignación de un tipo de entidad `ToTable` sin clave a un objeto de base de datos se logra mediante la API o `ToView` fluida. Desde la perspectiva de EF Core, el objeto de base de datos especificado en este método es una _vista,_ lo que significa que se trata como un origen de consulta de solo lectura y no puede ser el destino de las operaciones de actualización, inserción o eliminación. Sin embargo, esto no significa que el objeto de base de datos realmente sea necesario para ser una vista de base de datos. También puede ser una tabla de base de datos que se tratará como de solo lectura. Por el contrario, para los tipos de entidad normales, `ToTable` EF Core supone que un objeto de base de datos especificado en el método se puede tratar como una _tabla,_ lo que significa que se puede usar como origen de consulta, pero también se destina a las operaciones de actualización, eliminación e inserción. De hecho, puede especificar el nombre `ToTable` de una vista de base de datos y todo debe funcionar bien siempre que la vista esté configurada para que se pueda actualizar en la base de datos.

> [!NOTE]
> `ToView`se supone que el objeto ya existe en la base de datos y no se creará mediante migraciones.

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar tipos de entidad sin clave para consultar una vista de base de datos.

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) de este artículo en GitHub.

En primer lugar, definimos un modelo simple blog y publicación:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

A continuación, definimos una vista de base de datos simple que nos permitirá consultar el número de publicaciones asociadas a cada blog:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

A continuación, definimos una clase para contener el resultado de la vista de base de datos:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

A continuación, configuramos el tipo de entidad `HasNoKey` sin clave en _OnModelCreating_ mediante la API.
Usamos una API de configuración fluida para configurar la asignación para el tipo de entidad sin clave:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

A continuación, `DbContext` configuramos `DbSet<T>`el para incluir el :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Por último, podemos consultar la vista de base de datos de la manera estándar:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Tenga en cuenta que también hemos definido una propiedad de consulta de nivel de contexto (DbSet) para que actúe como raíz para las consultas en este tipo.
