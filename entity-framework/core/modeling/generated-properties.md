---
title: 'Valores generados: EF Core'
description: Cómo configurar la generación de valores para las propiedades al usar Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: 76fa4454c88a5ef7afb9864c2a4b1063ac75e37e
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983552"
---
# <a name="generated-values"></a><span data-ttu-id="9f8d2-103">Valores generados</span><span class="sxs-lookup"><span data-stu-id="9f8d2-103">Generated Values</span></span>

<span data-ttu-id="9f8d2-104">Las columnas de la base de datos pueden generar sus valores de varias maneras: las columnas de clave principal suelen ser enteros de incremento automático, otras columnas tienen valores predeterminados o calculados, etc. En esta página se detallan varios patrones para la generación de valores de configuración con EF Core.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-104">Database columns can have their values generated in various ways: primary key columns are frequently auto-incrementing integers, other columns have default or computed values, etc. This page details various patterns for configuration value generation with EF Core.</span></span>

## <a name="default-values"></a><span data-ttu-id="9f8d2-105">Valores predeterminados</span><span class="sxs-lookup"><span data-stu-id="9f8d2-105">Default values</span></span>

<span data-ttu-id="9f8d2-106">En las bases de datos relacionales, una columna se puede configurar con un valor predeterminado. Si se inserta una fila sin un valor para esa columna, se usará el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-106">On relational databases, a column can be configured with a default value; if a row is inserted without a value for that column, the default value will be used.</span></span>

<span data-ttu-id="9f8d2-107">Puede configurar un valor predeterminado en una propiedad:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-107">You can configure a default value on a property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

<span data-ttu-id="9f8d2-108">También puede especificar un fragmento de SQL que se usa para calcular el valor predeterminado:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-108">You can also specify a SQL fragment that is used to calculate the default value:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a><span data-ttu-id="9f8d2-109">Columnas calculadas</span><span class="sxs-lookup"><span data-stu-id="9f8d2-109">Computed columns</span></span>

<span data-ttu-id="9f8d2-110">En la mayoría de las bases de datos relacionales, una columna se puede configurar para que se calcule su valor en la base de datos, normalmente con una expresión que haga referencia a otras columnas:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-110">On most relational databases, a column can be configured to have its value computed in the database, typically with an expression referring to other columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

<span data-ttu-id="9f8d2-111">Lo anterior crea una columna calculada *virtual* , cuyo valor se calcula cada vez que se captura de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-111">The above creates a *virtual* computed column, whose value is computed every time it is fetched from the database.</span></span> <span data-ttu-id="9f8d2-112">También puede especificar que una columna calculada se *almacene* (a veces denominada *Persist*), lo que significa que se calcula en cada actualización de la fila y se almacena en el disco junto con las columnas normales:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-112">You may also specify that a computed column be *stored* (sometimes called *persisted*), meaning that it is computed on every update of the row, and is stored on disk alongside regular columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> <span data-ttu-id="9f8d2-113">La compatibilidad con la creación de columnas calculadas almacenadas se agregó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-113">Support for creating stored computed columns was added in EF Core 5.0.</span></span>

## <a name="primary-keys"></a><span data-ttu-id="9f8d2-114">Claves principales</span><span class="sxs-lookup"><span data-stu-id="9f8d2-114">Primary keys</span></span>

<span data-ttu-id="9f8d2-115">Por Convención, las claves principales no compuestas de tipo Short, int, Long o GUID están configuradas para que se generen valores para las entidades insertadas si la aplicación no proporciona un valor.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-115">By convention, non-composite primary keys of type short, int, long, or Guid are set up to have values generated for inserted entities if a value isn't provided by the application.</span></span> <span data-ttu-id="9f8d2-116">Normalmente, el proveedor de base de datos se encarga de la configuración necesaria. por ejemplo, una clave principal numérica en SQL Server se configura automáticamente para que sea una columna de identidad.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-116">Your database provider typically takes care of the necessary configuration; for example, a numeric primary key in SQL Server is automatically set up to be an IDENTITY column.</span></span>

<span data-ttu-id="9f8d2-117">Para obtener más información, [consulte la documentación acerca de las claves](xref:core/modeling/keys).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-117">For more information, [see the documentation about keys](xref:core/modeling/keys).</span></span>

## <a name="explicitly-configuring-value-generation"></a><span data-ttu-id="9f8d2-118">Configurar explícitamente la generación de valores</span><span class="sxs-lookup"><span data-stu-id="9f8d2-118">Explicitly configuring value generation</span></span>

<span data-ttu-id="9f8d2-119">Vimos lo anterior que EF Core configura automáticamente la generación de valores para las claves principales, pero es posible que deseemos hacer lo mismo para las propiedades que no son de clave.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-119">We saw above that EF Core automatically sets up value generation for primary keys - but we may want to do the same for non-key properties.</span></span> <span data-ttu-id="9f8d2-120">Puede configurar cualquier propiedad para que se genere su valor para las entidades insertadas como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-120">You can configure any property to have its value generated for inserted entities as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="9f8d2-121">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="9f8d2-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="9f8d2-122">API fluida</span><span class="sxs-lookup"><span data-stu-id="9f8d2-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

<span data-ttu-id="9f8d2-123">Del mismo modo, se puede configurar una propiedad para que se genere su valor al agregar o actualizar:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-123">Similarly, a property can be configured to have its value generated on add or update:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="9f8d2-124">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="9f8d2-124">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="9f8d2-125">API fluida</span><span class="sxs-lookup"><span data-stu-id="9f8d2-125">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

<span data-ttu-id="9f8d2-126">\*\*_</span><span class="sxs-lookup"><span data-stu-id="9f8d2-126">\*\*_</span></span>

> [!WARNING]
> <span data-ttu-id="9f8d2-127">A diferencia de los valores predeterminados o las columnas calculadas, no se especifica _how \* los valores se van a generar. depende del proveedor de base de datos que se utiliza.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-127">Unlike with default values or computed columns, we are not specifying _how\* the values are to be generated; that depends on the database provider being used.</span></span> <span data-ttu-id="9f8d2-128">Los proveedores de bases de datos pueden configurar automáticamente la generación de valores para algunos tipos de propiedad, pero otros pueden requerir que se configure manualmente cómo se genera el valor.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-128">Database providers may automatically set up value generation for some property types, but others may require you to manually set up how the value is generated.</span></span>
>
> <span data-ttu-id="9f8d2-129">Por ejemplo, en SQL Server, cuando se configura una propiedad GUID como valor generado en Add, el proveedor realiza automáticamente la generación de valores en el lado cliente, utilizando un algoritmo para generar valores GUID secuenciales óptimos.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-129">For example, on SQL Server, when a GUID property is configured as value generated on add, the provider automatically performs value generation client-side, using an algorithm to generate optimal sequential GUID values.</span></span> <span data-ttu-id="9f8d2-130">Sin embargo, especificar `ValueGeneratedOnAdd()` en una propiedad DateTime no tendrá ningún efecto ([consulte la sección siguiente para la generación de valores DATETIME](#datetime-value-generation)).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-130">However, specifying `ValueGeneratedOnAdd()` on a DateTime property will have no effect ([see the section below for DateTime value generation](#datetime-value-generation)).</span></span>
>
> <span data-ttu-id="9f8d2-131">Del mismo modo, las propiedades Byte [] configuradas como generadas en Add o Update y marcadas como tokens de simultaneidad se configuran con el tipo de datos rowversion, de modo que los valores se generan automáticamente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-131">Similarly, byte[] properties that are configured as generated on add or update and marked as concurrency tokens are set up with the rowversion data type, so that values are automatically generated in the database.</span></span> <span data-ttu-id="9f8d2-132">Sin embargo, si `ValueGeneratedOnAddOrUpdate()` se especifica, no tendrá ningún efecto.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-132">However, specifying `ValueGeneratedOnAddOrUpdate()` will again have no effect.</span></span>
>
> [!NOTE]
> <span data-ttu-id="9f8d2-133">Dependiendo del proveedor de base de datos que se use, es posible que se generen valores de cliente en EF o en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-133">Depending on the database provider being used, values may be generated client side by EF or in the database.</span></span> <span data-ttu-id="9f8d2-134">Si la base de datos genera el valor, EF puede asignar un valor temporal al agregar la entidad al contexto. Este valor temporal se reemplazará por el valor generado por la base de datos durante `SaveChanges()` .</span><span class="sxs-lookup"><span data-stu-id="9f8d2-134">If the value is generated by the database, then EF may assign a temporary value when you add the entity to the context; this temporary value will then be replaced by the database generated value during `SaveChanges()`.</span></span> <span data-ttu-id="9f8d2-135">Para obtener más información, [vea los documentos sobre valores temporales](xref:core/change-tracking/explicit-tracking#temporary-values).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-135">For more information, [see the docs on temporary values](xref:core/change-tracking/explicit-tracking#temporary-values).</span></span>

## <a name="datetime-value-generation"></a><span data-ttu-id="9f8d2-136">Generación de valores de fecha y hora</span><span class="sxs-lookup"><span data-stu-id="9f8d2-136">Date/time value generation</span></span>

<span data-ttu-id="9f8d2-137">Una solicitud común es tener una columna de base de datos que contenga la fecha y hora de la primera vez que se insertó la columna (valor generado al agregar) o para la última actualización (valor generado al agregar o actualizar).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-137">A common request is to have a database column which contains the date/time for when the column was first inserted (value generated on add), or for when it was last updated (value generated on add or update).</span></span> <span data-ttu-id="9f8d2-138">Como hay varias estrategias para hacerlo, los proveedores de EF Core no suelen configurar la generación de valores automáticamente para las columnas de fecha y hora, por lo que debe configurarla usted mismo.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-138">As there are various strategies to do this, EF Core providers usually don't set up value generation automatically for date/time columns - you have to configure this yourself.</span></span>

### <a name="creation-timestamp"></a><span data-ttu-id="9f8d2-139">Marca de tiempo de creación</span><span class="sxs-lookup"><span data-stu-id="9f8d2-139">Creation timestamp</span></span>

<span data-ttu-id="9f8d2-140">La configuración de una columna de fecha y hora para que tenga la marca de tiempo de creación de la fila suele ser cuestión de configurar un valor predeterminado con la función SQL adecuada.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-140">Configuring a date/time column to have the creation timestamp of the row is usually a matter of configuring a default value with the appropriate SQL function.</span></span> <span data-ttu-id="9f8d2-141">Por ejemplo, en SQL Server puede utilizar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-141">For example, on SQL Server you may use the following:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

<span data-ttu-id="9f8d2-142">Asegúrese de seleccionar la función adecuada, ya que pueden existir varias (por ejemplo, `GETDATE()` vs `GETUTCDATE()` ).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-142">Be sure to select the appropriate function, as several may exist (e.g. `GETDATE()` vs. `GETUTCDATE()`).</span></span>

### <a name="update-timestamp"></a><span data-ttu-id="9f8d2-143">Actualizar marca de tiempo</span><span class="sxs-lookup"><span data-stu-id="9f8d2-143">Update timestamp</span></span>

<span data-ttu-id="9f8d2-144">Aunque las columnas calculadas almacenadas parecen una buena solución para administrar las marcas de tiempo de la última actualización, las bases de datos no suelen permitir la especificación de funciones como `GETDATE()` en una columna calculada.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-144">Although stored computed columns seem like a good solution for managing last-updated timestamps, databases usually don't allow specifying functions such as `GETDATE()` in a computed column.</span></span> <span data-ttu-id="9f8d2-145">Como alternativa, puede configurar un desencadenador de base de datos para lograr el mismo efecto:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-145">As an alternative, you can set up a database trigger to achieve the same effect:</span></span>

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

<span data-ttu-id="9f8d2-146">Para obtener información sobre la creación de desencadenadores, [consulte la documentación sobre el uso de SQL sin procesar en las migraciones](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-146">For information on creating triggers, [see the documentation on using raw SQL in migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span></span>

## <a name="overriding-value-generation"></a><span data-ttu-id="9f8d2-147">Reemplazar la generación de valores</span><span class="sxs-lookup"><span data-stu-id="9f8d2-147">Overriding value generation</span></span>

<span data-ttu-id="9f8d2-148">Aunque una propiedad está configurada para la generación de valores, en muchos casos todavía puede especificar explícitamente un valor para ella.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-148">Although a property is configured for value generation, in many cases you may still explicitly specify a value for it.</span></span> <span data-ttu-id="9f8d2-149">El hecho de que esto funcione realmente depende del mecanismo de generación de valores específico que se ha configurado. Aunque puede especificar un valor explícito en lugar de usar el valor predeterminado de una columna, no se puede hacer lo mismo con las columnas calculadas.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-149">Whether this will actually work depends on the specific value generation mechanism that has been configured; while you may specify an explicit value instead of using a column's default value, the same cannot be done with computed columns.</span></span>

<span data-ttu-id="9f8d2-150">Para invalidar la generación de valores con un valor explícito, basta con establecer la propiedad en cualquier valor que no sea el valor predeterminado de CLR para el tipo de esa propiedad (para, para, `null` `string` `0` `int` `Guid.Empty` `Guid` etc.).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-150">To override value generation with an explicit value, simply set the property to any value that is not the CLR default value for that property's type (`null` for `string`, `0` for `int`, `Guid.Empty` for `Guid`, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="9f8d2-151">De forma predeterminada, se produce un error al intentar insertar valores explícitos en SQL Server identidad. [vea estos documentos para obtener una solución alternativa](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-151">Trying to insert explicit values into SQL Server IDENTITY fails by default; [see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span></span>

<span data-ttu-id="9f8d2-152">Para proporcionar un valor explícito para las propiedades que se han configurado como valor generado al agregar o actualizar, también debe configurar la propiedad como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-152">To provide an explicit value for properties that have been configured as value generated on add or update, you must also configure the property as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a><span data-ttu-id="9f8d2-153">Sin generación de valores</span><span class="sxs-lookup"><span data-stu-id="9f8d2-153">No value generation</span></span>

<span data-ttu-id="9f8d2-154">Además de los escenarios específicos, como los descritos anteriormente, las propiedades normalmente no tienen ninguna generación de valores configurada. Esto significa que depende de la aplicación proporcionar siempre un valor que se va a guardar en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-154">Apart from specific scenarios such as those described above, properties typically have no value generation configured; this means that it's up to the application to always supply a value to be saved to the database.</span></span> <span data-ttu-id="9f8d2-155">Este valor se debe asignar a las nuevas entidades antes de que se agreguen al contexto.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-155">This value must be assigned to new entities before they are added to the context.</span></span>

<span data-ttu-id="9f8d2-156">Sin embargo, en algunos casos puede que desee deshabilitar la generación de valores configurada por Convención.</span><span class="sxs-lookup"><span data-stu-id="9f8d2-156">However, in some cases you may want to disable value generation that has been set up by convention.</span></span> <span data-ttu-id="9f8d2-157">Por ejemplo, una clave principal de tipo int normalmente se configura de forma implícita como una columna de identidad generada por el valor (por ejemplo, la columna de identidad en SQL Server).</span><span class="sxs-lookup"><span data-stu-id="9f8d2-157">For example, a primary key of type int is usually implicitly configured as value-generated-on-add (e.g. identity column on SQL Server).</span></span> <span data-ttu-id="9f8d2-158">Puede deshabilitarlo a través de lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9f8d2-158">You can disable this via the following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="9f8d2-159">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="9f8d2-159">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="9f8d2-160">API fluida</span><span class="sxs-lookup"><span data-stu-id="9f8d2-160">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
