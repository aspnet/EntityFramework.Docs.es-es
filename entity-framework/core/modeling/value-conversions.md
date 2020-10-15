---
title: Conversiones de valores-EF Core
description: Configuración de convertidores de valores en un modelo de Entity Framework Core
author: ajcvickers
ms.date: 02/19/2018
uid: core/modeling/value-conversions
ms.openlocfilehash: 221560a145fe25c2b7bf094839dd37791bc25955
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063964"
---
# <a name="value-conversions"></a><span data-ttu-id="867f7-103">Conversiones de valores</span><span class="sxs-lookup"><span data-stu-id="867f7-103">Value Conversions</span></span>

<span data-ttu-id="867f7-104">Los convertidores de valores permiten convertir los valores de propiedad al leer o escribir en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="867f7-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="867f7-105">Esta conversión puede ser de un valor a otro del mismo tipo (por ejemplo, cifrar cadenas) o de un valor de un tipo a un valor de otro tipo (por ejemplo, convertir valores de enumeración en cadenas en la base de datos y desde ellas).</span><span class="sxs-lookup"><span data-stu-id="867f7-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="867f7-106">Conceptos básicos</span><span class="sxs-lookup"><span data-stu-id="867f7-106">Fundamentals</span></span>

<span data-ttu-id="867f7-107">Los convertidores de valores se especifican en términos de `ModelClrType` y `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="867f7-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="867f7-108">El tipo de modelo es el tipo .NET de la propiedad en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="867f7-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="867f7-109">El tipo de proveedor es el tipo .NET que entiende el proveedor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="867f7-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="867f7-110">Por ejemplo, para guardar las enumeraciones como cadenas en la base de datos, el tipo de modelo es el tipo de la enumeración y el tipo de proveedor es `String` .</span><span class="sxs-lookup"><span data-stu-id="867f7-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="867f7-111">Estos dos tipos pueden ser iguales.</span><span class="sxs-lookup"><span data-stu-id="867f7-111">These two types can be the same.</span></span>

<span data-ttu-id="867f7-112">Las conversiones se definen mediante dos `Func` árboles de expresión: uno de `ModelClrType` a `ProviderClrType` y el otro de `ProviderClrType` a `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="867f7-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="867f7-113">Los árboles de expresión se usan para que se puedan compilar en el código de acceso a la base de datos para conversiones eficientes.</span><span class="sxs-lookup"><span data-stu-id="867f7-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="867f7-114">En las conversiones complejas, el árbol de expresión puede ser una llamada simple a un método que realiza la conversión.</span><span class="sxs-lookup"><span data-stu-id="867f7-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="867f7-115">Configurar un convertidor de valores</span><span class="sxs-lookup"><span data-stu-id="867f7-115">Configuring a value converter</span></span>

<span data-ttu-id="867f7-116">Las conversiones de valores se definen en las propiedades `OnModelCreating` del de `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="867f7-116">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="867f7-117">Por ejemplo, considere una enumeración y un tipo de entidad definidos como:</span><span class="sxs-lookup"><span data-stu-id="867f7-117">For example, consider an enum and entity type defined as:</span></span>

```csharp
public class Rider
{
    public int Id { get; set; }
    public EquineBeast Mount { get; set; }
}

public enum EquineBeast
{
    Donkey,
    Mule,
    Horse,
    Unicorn
}
```

<span data-ttu-id="867f7-118">A continuación, se pueden definir conversiones en `OnModelCreating` para almacenar los valores de enumeración como cadenas (por ejemplo, "Donkey", "Mule",...) en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="867f7-118">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]
> <span data-ttu-id="867f7-119">`null`Nunca se pasará un valor a un convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="867f7-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="867f7-120">Esto hace que la implementación de conversiones sea más sencilla y permite que se compartan entre propiedades que aceptan valores NULL y que no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="867f7-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="867f7-121">La clase ValueConverter</span><span class="sxs-lookup"><span data-stu-id="867f7-121">The ValueConverter class</span></span>

<span data-ttu-id="867f7-122">Al llamar a `HasConversion` como se muestra anteriormente, se creará una `ValueConverter` instancia de y se establecerá en la propiedad.</span><span class="sxs-lookup"><span data-stu-id="867f7-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="867f7-123">`ValueConverter`En su lugar, se puede crear explícitamente.</span><span class="sxs-lookup"><span data-stu-id="867f7-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="867f7-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="867f7-124">For example:</span></span>

```csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="867f7-125">Esto puede ser útil cuando varias propiedades usan la misma conversión.</span><span class="sxs-lookup"><span data-stu-id="867f7-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]
> <span data-ttu-id="867f7-126">Actualmente no hay ninguna manera de especificar en un lugar que cada propiedad de un tipo determinado debe usar el mismo convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="867f7-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="867f7-127">Esta característica se considerará para futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="867f7-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="867f7-128">Convertidores integrados</span><span class="sxs-lookup"><span data-stu-id="867f7-128">Built-in converters</span></span>

<span data-ttu-id="867f7-129">EF Core incluye un conjunto de clases predefinidas `ValueConverter` , que se encuentran en el `Microsoft.EntityFrameworkCore.Storage.ValueConversion` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="867f7-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="867f7-130">Dichos componentes son:</span><span class="sxs-lookup"><span data-stu-id="867f7-130">These are:</span></span>

* <span data-ttu-id="867f7-131">`BoolToZeroOneConverter` -Bool a cero y uno</span><span class="sxs-lookup"><span data-stu-id="867f7-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="867f7-132">`BoolToStringConverter` -Bool a cadenas como "Y" y "N"</span><span class="sxs-lookup"><span data-stu-id="867f7-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="867f7-133">`BoolToTwoValuesConverter` -Bool a dos valores cualesquiera</span><span class="sxs-lookup"><span data-stu-id="867f7-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="867f7-134">`BytesToStringConverter` -Matriz de bytes a cadena codificada en Base64</span><span class="sxs-lookup"><span data-stu-id="867f7-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="867f7-135">`CastingConverter` -Conversiones que requieren solo una conversión de tipo</span><span class="sxs-lookup"><span data-stu-id="867f7-135">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="867f7-136">`CharToStringConverter` -Char a cadena de carácter único</span><span class="sxs-lookup"><span data-stu-id="867f7-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="867f7-137">`DateTimeOffsetToBinaryConverter` -DateTimeOffset a código binario codificado de 64 bits</span><span class="sxs-lookup"><span data-stu-id="867f7-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="867f7-138">`DateTimeOffsetToBytesConverter` -DateTimeOffset a la matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="867f7-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="867f7-139">`DateTimeOffsetToStringConverter` -DateTimeOffset a cadena</span><span class="sxs-lookup"><span data-stu-id="867f7-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="867f7-140">`DateTimeToBinaryConverter` -DateTime al valor de 64 bits, incluido DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="867f7-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="867f7-141">`DateTimeToStringConverter` -DateTime a cadena</span><span class="sxs-lookup"><span data-stu-id="867f7-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="867f7-142">`DateTimeToTicksConverter` : Fecha y hora en pasos</span><span class="sxs-lookup"><span data-stu-id="867f7-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="867f7-143">`EnumToNumberConverter` -Enum al número subyacente</span><span class="sxs-lookup"><span data-stu-id="867f7-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="867f7-144">`EnumToStringConverter` -Enum a cadena</span><span class="sxs-lookup"><span data-stu-id="867f7-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="867f7-145">`GuidToBytesConverter` -GUID a matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="867f7-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="867f7-146">`GuidToStringConverter` -GUID a cadena</span><span class="sxs-lookup"><span data-stu-id="867f7-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="867f7-147">`NumberToBytesConverter` -Cualquier valor numérico a matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="867f7-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="867f7-148">`NumberToStringConverter` -Cualquier valor numérico a cadena</span><span class="sxs-lookup"><span data-stu-id="867f7-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="867f7-149">`StringToBytesConverter` -Cadena en bytes UTF8</span><span class="sxs-lookup"><span data-stu-id="867f7-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="867f7-150">`TimeSpanToStringConverter` -TimeSpan a cadena</span><span class="sxs-lookup"><span data-stu-id="867f7-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="867f7-151">`TimeSpanToTicksConverter` -Intervalo de tiempo a pasos</span><span class="sxs-lookup"><span data-stu-id="867f7-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="867f7-152">Tenga en cuenta que `EnumToStringConverter` se incluye en esta lista.</span><span class="sxs-lookup"><span data-stu-id="867f7-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="867f7-153">Esto significa que no es necesario especificar la conversión explícitamente, como se muestra anteriormente.</span><span class="sxs-lookup"><span data-stu-id="867f7-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="867f7-154">En su lugar, use simplemente el convertidor integrado:</span><span class="sxs-lookup"><span data-stu-id="867f7-154">Instead, just use the built-in converter:</span></span>

```csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="867f7-155">Tenga en cuenta que todos los convertidores integrados no tienen estado y, por tanto, una sola instancia puede compartirse de forma segura con varias propiedades.</span><span class="sxs-lookup"><span data-stu-id="867f7-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="867f7-156">Conversiones predefinidas</span><span class="sxs-lookup"><span data-stu-id="867f7-156">Pre-defined conversions</span></span>

<span data-ttu-id="867f7-157">En el caso de las conversiones comunes para las que existe un convertidor integrado, no es necesario especificar el convertidor explícitamente.</span><span class="sxs-lookup"><span data-stu-id="867f7-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="867f7-158">En su lugar, solo tiene que configurar el tipo de proveedor que se debe usar y EF usará automáticamente el convertidor integrado adecuado.</span><span class="sxs-lookup"><span data-stu-id="867f7-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="867f7-159">La enumeración de las conversiones de cadenas se usa como ejemplo anterior, pero EF lo hará automáticamente si se configura el tipo de proveedor:</span><span class="sxs-lookup"><span data-stu-id="867f7-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

```csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="867f7-160">Lo mismo se puede lograr especificando explícitamente el tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="867f7-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="867f7-161">Por ejemplo, si el tipo de entidad se define de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="867f7-161">For example, if the entity type is defined like so:</span></span>

```csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="867f7-162">A continuación, los valores de enumeración se guardarán como cadenas en la base de datos sin ninguna configuración adicional en `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="867f7-162">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="867f7-163">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="867f7-163">Limitations</span></span>

<span data-ttu-id="867f7-164">Hay algunas limitaciones actuales conocidas del sistema de conversión de valores:</span><span class="sxs-lookup"><span data-stu-id="867f7-164">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="867f7-165">Como se indicó anteriormente, `null` no se puede convertir.</span><span class="sxs-lookup"><span data-stu-id="867f7-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="867f7-166">Actualmente no hay ninguna manera de distribuir una conversión de una propiedad a varias columnas o viceversa.</span><span class="sxs-lookup"><span data-stu-id="867f7-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="867f7-167">El uso de conversiones de valores puede afectar a la capacidad de EF Core para traducir expresiones a SQL.</span><span class="sxs-lookup"><span data-stu-id="867f7-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="867f7-168">Se registrará una advertencia para dichos casos.</span><span class="sxs-lookup"><span data-stu-id="867f7-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="867f7-169">La eliminación de estas limitaciones se está considerando en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="867f7-169">Removal of these limitations is being considered for a future release.</span></span>
