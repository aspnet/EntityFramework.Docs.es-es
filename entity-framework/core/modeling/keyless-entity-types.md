---
title: 'Tipos de entidad sin llave: EF Core'
description: Configuración de tipos de entidad sin entrada mediante Entity Framework Core
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 488b21767993474d3e90677ee1e480a970b09edb
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023944"
---
# <a name="keyless-entity-types"></a>Tipos de entidad sin llave

> [!NOTE]
> Esta característica se ha agregado bajo el nombre de los tipos de consulta. En EF Core 3,0 se cambió el nombre del concepto a tipos de entidad sin entrada. La `[Keyless]` anotación de datos estuvo disponible en EFCore 5,0.

Además de los tipos de entidad normales, un modelo de EF Core puede contener _tipos de entidad_ sin clave, que se pueden usar para realizar consultas de base de datos con datos que no contengan valores de clave.

## <a name="defining-keyless-entity-types"></a>Definición de tipos de entidad sin entrada

Los tipos de entidad sin llave pueden definirse mediante la anotación de datos o la API fluida:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Características de tipos de entidad sin llave

Los tipos de entidad sin llave admiten muchas de las mismas capacidades de asignación que los tipos de entidad normales, como las propiedades de navegación y asignación de herencia. En almacenes relacionales, pueden configurar los objetos y las columnas de la base de datos de destino mediante métodos de API fluida o anotaciones de datos.

Sin embargo, son diferentes de los tipos de entidad normales en que:

- No se puede definir una clave.
- Nunca se realiza un seguimiento de los cambios en _DbContext_ y, por lo tanto, nunca se insertan, actualizan o eliminan en la base de datos.
- Nunca se detectan por Convención.
- Solo admite un subconjunto de capacidades de asignación de navegación, en concreto:
  - Nunca pueden actuar como el extremo principal de una relación.
  - Puede que no tengan navegaciones a entidades propiedad
  - Solo pueden contener propiedades de navegación de referencia que apunten a entidades normales.
  - Las entidades no pueden contener propiedades de navegación a tipos de entidad sin llave.
- Debe configurarse con una `[Keyless]` anotación de datos o una `.HasNoKey()` llamada al método.
- Se puede asignar a una _consulta de definición_. Una consulta de definición es una consulta declarada en el modelo que actúa como origen de datos para un tipo de entidad sin entrada.

## <a name="usage-scenarios"></a>Escenarios de uso

Algunos de los escenarios de uso principales de los tipos de entidad sin llave son:

- Actúa como el tipo de valor devuelto para [las consultas SQL sin procesar](xref:core/querying/raw-sql).
- Asignación a vistas de base de datos que no contienen una clave principal.
- Asignación a tablas que no tienen una clave principal definida.
- Asignación a las consultas definidas en el modelo.

## <a name="mapping-to-database-objects"></a>Asignar a objetos de base de datos

La asignación de un tipo de entidad sin llave a un objeto de base de datos se consigue mediante el `ToTable` o la `ToView` API fluida. Desde la perspectiva de EF Core, el objeto de base de datos especificado en este método es una _vista_, lo que significa que se trata como un origen de consulta de solo lectura y no puede ser el destino de las operaciones de actualización, inserción o eliminación. Sin embargo, esto no significa que el objeto de base de datos sea realmente necesario para ser una vista de base de datos. Como alternativa, puede tratarse de una tabla de base de datos que se tratará como de solo lectura. Por el contrario, en el caso de los tipos de entidad normales, EF Core supone que un objeto de base de datos especificado en el `ToTable` método se puede tratar como una _tabla_, lo que significa que se puede usar como origen de la consulta, pero también como destino de las operaciones de actualización, eliminación e inserción. De hecho, puede especificar el nombre de una vista de base de datos en `ToTable` y todo debería funcionar bien siempre que la vista esté configurada para ser actualizable en la base de datos.

> [!NOTE]
> `ToView` supone que el objeto ya existe en la base de datos y que no lo crearán las migraciones.

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar los tipos de entidad sin entrada para consultar una vista de base de datos.

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/KeylessEntityTypes) de este artículo en GitHub.

En primer lugar, definimos un blog y un modelo post sencillos:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

A continuación, se define una vista de base de datos simple que nos permitirá consultar el número de publicaciones asociadas a cada blog:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

A continuación, se define una clase para que contenga el resultado de la vista de base de datos:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

A continuación, configuraremos el tipo de entidad sin llave en _OnModelCreating_ con la `HasNoKey` API.
Usamos la API de configuración fluida para configurar la asignación para el tipo de entidad sin llave:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

A continuación, configuramos el `DbContext` para incluir `DbSet<T>` :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Por último, podemos consultar la vista de base de datos de la manera estándar:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Nota también hemos definido una propiedad de consulta de nivel de contexto (DbSet) para que actúe como raíz para las consultas en este tipo.

> [!TIP]
> Para probar los tipos de entidad sin entrada asignados a las vistas mediante el proveedor en memoria, asígnelo a una consulta a través de `ToInMemoryQuery` . Vea un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) mediante esta técnica para obtener más detalles.
