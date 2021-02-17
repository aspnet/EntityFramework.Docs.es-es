---
title: 'Valores generados: EF Core'
description: Cómo configurar la generación de valores para las propiedades al usar Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: a9e43f3b755bf028bc76581135988e831a42d0d1
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543346"
---
# <a name="generated-values"></a>Valores generados

Las columnas de la base de datos pueden generar sus valores de varias maneras: las columnas de clave principal suelen ser enteros de incremento automático, otras columnas tienen valores predeterminados o calculados, etc. En esta página se detallan varios patrones para la generación de valores de configuración con EF Core.

## <a name="default-values"></a>Valores predeterminados

En las bases de datos relacionales, una columna se puede configurar con un valor predeterminado. Si se inserta una fila sin un valor para esa columna, se usará el valor predeterminado.

Puede configurar un valor predeterminado en una propiedad:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

También puede especificar un fragmento de SQL que se usa para calcular el valor predeterminado:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a>Columnas calculadas

En la mayoría de las bases de datos relacionales, una columna se puede configurar para que se calcule su valor en la base de datos, normalmente con una expresión que haga referencia a otras columnas:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

Lo anterior crea una columna calculada *virtual* , cuyo valor se calcula cada vez que se captura de la base de datos. También puede especificar que una columna calculada se *almacene* (a veces denominada *Persist*), lo que significa que se calcula en cada actualización de la fila y se almacena en el disco junto con las columnas normales:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> La compatibilidad con la creación de columnas calculadas almacenadas se agregó en EF Core 5,0.

## <a name="primary-keys"></a>Claves principales

Por Convención, las claves principales no compuestas de tipo Short, int, Long o GUID están configuradas para que se generen valores para las entidades insertadas si la aplicación no proporciona un valor. Normalmente, el proveedor de base de datos se encarga de la configuración necesaria. por ejemplo, una clave principal numérica en SQL Server se configura automáticamente para que sea una columna de identidad.

Para obtener más información, [consulte la documentación acerca de las claves](xref:core/modeling/keys).

## <a name="explicitly-configuring-value-generation"></a>Configurar explícitamente la generación de valores

Vimos lo anterior que EF Core configura automáticamente la generación de valores para las claves principales, pero es posible que deseemos hacer lo mismo para las propiedades que no son de clave. Puede configurar cualquier propiedad para que se genere su valor para las entidades insertadas como se indica a continuación:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=6)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

Del mismo modo, se puede configurar una propiedad para que se genere su valor al agregar o actualizar:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=6)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> A diferencia de los valores predeterminados o las columnas calculadas, no se especifica *Cómo* se van a generar los valores. depende del proveedor de base de datos que se utiliza. Los proveedores de bases de datos pueden configurar automáticamente la generación de valores para algunos tipos de propiedad, pero otros pueden requerir que se configure manualmente cómo se genera el valor.
>
> Por ejemplo, en SQL Server, cuando se configura una propiedad GUID como valor generado en Add, el proveedor realiza automáticamente la generación de valores en el lado cliente, utilizando un algoritmo para generar valores GUID secuenciales óptimos. Sin embargo, especificar `ValueGeneratedOnAdd()` en una propiedad DateTime no tendrá ningún efecto ([consulte la sección siguiente para la generación de valores DATETIME](#datetime-value-generation)).
>
> Del mismo modo, las propiedades Byte [] configuradas como generadas en Add o Update y marcadas como tokens de simultaneidad se configuran con el tipo de datos rowversion, de modo que los valores se generan automáticamente en la base de datos. Sin embargo, si `ValueGeneratedOnAddOrUpdate()` se especifica, no tendrá ningún efecto.
>
> [!NOTE]
> Dependiendo del proveedor de base de datos que se use, es posible que se generen valores de cliente en EF o en la base de datos. Si la base de datos genera el valor, EF puede asignar un valor temporal al agregar la entidad al contexto. Este valor temporal se reemplazará por el valor generado por la base de datos durante `SaveChanges()` . Para obtener más información, [vea los documentos sobre valores temporales](xref:core/change-tracking/explicit-tracking#temporary-values).

## <a name="datetime-value-generation"></a>Generación de valores de fecha y hora

Una solicitud común es tener una columna de base de datos que contenga la fecha y hora de la primera vez que se insertó la columna (valor generado al agregar) o para la última actualización (valor generado al agregar o actualizar). Como hay varias estrategias para hacerlo, los proveedores de EF Core no suelen configurar la generación de valores automáticamente para las columnas de fecha y hora, por lo que debe configurarla usted mismo.

### <a name="creation-timestamp"></a>Marca de tiempo de creación

La configuración de una columna de fecha y hora para que tenga la marca de tiempo de creación de la fila suele ser cuestión de configurar un valor predeterminado con la función SQL adecuada. Por ejemplo, en SQL Server puede utilizar lo siguiente:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

Asegúrese de seleccionar la función adecuada, ya que pueden existir varias (por ejemplo, `GETDATE()` vs `GETUTCDATE()` ).

### <a name="update-timestamp"></a>Actualizar marca de tiempo

Aunque las columnas calculadas almacenadas parecen una buena solución para administrar las marcas de tiempo de la última actualización, las bases de datos no suelen permitir la especificación de funciones como `GETDATE()` en una columna calculada. Como alternativa, puede configurar un desencadenador de base de datos para lograr el mismo efecto:

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

Para obtener información sobre la creación de desencadenadores, [consulte la documentación sobre el uso de SQL sin procesar en las migraciones](xref:core/managing-schemas/migrations/managing#adding-raw-sql).

## <a name="overriding-value-generation"></a>Reemplazar la generación de valores

Aunque una propiedad está configurada para la generación de valores, en muchos casos todavía puede especificar explícitamente un valor para ella. El hecho de que esto funcione realmente depende del mecanismo de generación de valores específico que se ha configurado. Aunque puede especificar un valor explícito en lugar de usar el valor predeterminado de una columna, no se puede hacer lo mismo con las columnas calculadas.

Para invalidar la generación de valores con un valor explícito, basta con establecer la propiedad en cualquier valor que no sea el valor predeterminado de CLR para el tipo de esa propiedad (para, para, `null` `string` `0` `int` `Guid.Empty` `Guid` etc.).

> [!NOTE]
> De forma predeterminada, se produce un error al intentar insertar valores explícitos en SQL Server identidad. [vea estos documentos para obtener una solución alternativa](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).

Para proporcionar un valor explícito para las propiedades que se han configurado como valor generado al agregar o actualizar, también debe configurar la propiedad como se indica a continuación:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a>Sin generación de valores

Además de los escenarios específicos, como los descritos anteriormente, las propiedades normalmente no tienen ninguna generación de valores configurada. Esto significa que depende de la aplicación proporcionar siempre un valor que se va a guardar en la base de datos. Este valor se debe asignar a las nuevas entidades antes de que se agreguen al contexto.

Sin embargo, en algunos casos puede que desee deshabilitar la generación de valores configurada por Convención. Por ejemplo, una clave principal de tipo int normalmente se configura de forma implícita como una columna de identidad generada por el valor (por ejemplo, la columna de identidad en SQL Server). Puede deshabilitarlo a través de lo siguiente:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
