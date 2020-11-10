---
title: 'Valores generados: EF Core'
description: Cómo configurar la generación de valores para las propiedades al usar Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: 347cedbf5fdebc985d75c6cad3c28f17d1344993
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429629"
---
# <a name="generated-values"></a>Valores generados

## <a name="value-generation-patterns"></a>Patrones de generación de valores

Hay tres patrones de generación de valores que se pueden usar para las propiedades:

* Sin generación de valores
* Valor generado al agregar
* Valor generado al agregar o actualizar

### <a name="no-value-generation"></a>Sin generación de valores

Ninguna generación de valores significa que siempre se proporcionará un valor válido que se va a guardar en la base de datos. Este valor válido se debe asignar a las nuevas entidades antes de que se agreguen al contexto.

### <a name="value-generated-on-add"></a>Valor generado al agregar

El valor generado al agregar significa que se genera un valor para las nuevas entidades.

Dependiendo del proveedor de base de datos que se use, es posible que se generen valores de cliente en EF o en la base de datos. Si la base de datos genera el valor, EF puede asignar un valor temporal al agregar la entidad al contexto. Este valor temporal se reemplazará por el valor generado por la base de datos durante `SaveChanges()` .

Si agrega una entidad al contexto que tiene un valor asignado a la propiedad, EF intentará insertar ese valor en lugar de generar uno nuevo. Se considera que una propiedad tiene un valor asignado si no se le asigna el valor predeterminado de CLR ( `null` para, `string` para, `0` `int` `Guid.Empty` `Guid` etc.). Para obtener más información, vea [valores explícitos para las propiedades generadas](xref:core/saving/explicit-values-generated-properties).

> [!WARNING]
> La forma en que se genera el valor para las entidades agregadas dependerá del proveedor de base de datos que se use. Los proveedores de bases de datos pueden configurar automáticamente la generación de valores para algunos tipos de propiedad, pero otros pueden requerir que se configure manualmente cómo se genera el valor.
>
> Por ejemplo, al usar SQL Server, los valores se generarán automáticamente para `GUID` las propiedades (mediante el algoritmo GUID secuencial SQL Server). Sin embargo, si especifica que `DateTime` se genere una propiedad en Add, debe configurar una manera de que se generen los valores. Una manera de hacerlo es configurar un valor predeterminado de `GETDATE()` , vea [valores predeterminados](#default-values).

### <a name="value-generated-on-add-or-update"></a>Valor generado al agregar o actualizar

El valor generado al agregar o actualizar significa que se genera un nuevo valor cada vez que se guarda el registro (Insert o Update).

Al igual que `value generated on add` , si especifica un valor para la propiedad en una instancia recién agregada de una entidad, se insertará ese valor en lugar de un valor que se va a generar. También es posible establecer un valor explícito al actualizar. Para obtener más información, vea [valores explícitos para las propiedades generadas](xref:core/saving/explicit-values-generated-properties).

> [!WARNING]
> La forma en que se genera el valor para las entidades agregadas y actualizadas dependerá del proveedor de base de datos que se use. Los proveedores de bases de datos pueden configurar automáticamente la generación de valores para algunos tipos de propiedades, mientras que otros requerirán que se configure manualmente cómo se genera el valor.
>
> Por ejemplo, al usar SQL Server, `byte[]` las propiedades que se establecen tal y como se generan al agregar o actualizar y marcadas como tokens de simultaneidad, se configurarán con el `rowversion` tipo de datos, de modo que los valores se generarán en la base de datos. Sin embargo, si especifica que `DateTime` se genere una propiedad al agregar o actualizar, debe configurar una manera de que se generen los valores. Una forma de hacerlo consiste en configurar un valor predeterminado de `GETDATE()` (vea [valores predeterminados](#default-values)) para generar valores para las nuevas filas. Después, puede usar un desencadenador de base de datos para generar valores durante las actualizaciones (como el siguiente desencadenador de ejemplo).
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="value-generated-on-add"></a>Valor generado al agregar

Por Convención, las claves principales no compuestas de tipo Short, int, Long o GUID están configuradas para que tengan valores generados para las entidades insertadas, si la aplicación no proporciona un valor. Normalmente, el proveedor de base de datos se encarga de la configuración necesaria. por ejemplo, una clave principal numérica en SQL Server se configura automáticamente para que sea una columna de identidad.

Puede configurar cualquier propiedad para que se genere su valor para las entidades insertadas como se indica a continuación:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

> [!WARNING]
> Esto solo permite que EF sepa que los valores se generan para las entidades agregadas, no garantiza que EF configurará el mecanismo real para generar valores. Vea la sección [valor generado al agregar](#value-generated-on-add) para obtener más detalles.

### <a name="default-values"></a>Valores predeterminados

En las bases de datos relacionales, una columna se puede configurar con un valor predeterminado. Si se inserta una fila sin un valor para esa columna, se usará el valor predeterminado.

Puede configurar un valor predeterminado en una propiedad:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

También puede especificar un fragmento de SQL que se usa para calcular el valor predeterminado:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

Si se especifica un valor predeterminado, se configurará implícitamente la propiedad como valor generado al agregar.

## <a name="value-generated-on-add-or-update"></a>Valor generado al agregar o actualizar

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

**_

> [!WARNING]
> Esto solo permite que EF sepa que se generan valores para entidades agregadas o actualizadas, no garantiza que EF configurará el mecanismo real para generar valores. Vea la sección [valor generado al agregar o actualizar](#value-generated-on-add-or-update) para obtener más detalles.

### <a name="computed-columns"></a>Columnas calculadas

En la mayoría de las bases de datos relacionales, una columna se puede configurar para que se calcule su valor en la base de datos, normalmente con una expresión que haga referencia a otras columnas:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn)]

Lo anterior crea una columna calculada de _virtual *, cuyo valor se calcula cada vez que se captura de la base de datos. También puede especificar que una columna calculada se *almacene* (a veces denominada *Persist* ), lo que significa que se calcula en cada actualización de la fila y se almacena en el disco junto con las columnas normales:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn)]

> [!NOTE]
> La compatibilidad con la creación de columnas calculadas almacenadas se agregó en EF Core 5,0.

## <a name="no-value-generation"></a>Sin generación de valores

Normalmente, es necesario deshabilitar la generación de valores en una propiedad si una Convención la configura para la generación de valores. Por ejemplo, si tiene una clave principal de tipo int, se establecerá implícitamente configurada como valor generado al agregar; puede deshabilitarlo a través de lo siguiente:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
