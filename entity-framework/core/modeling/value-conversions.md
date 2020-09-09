---
title: Conversiones de valores-EF Core
description: Configuración de convertidores de valores en un modelo de Entity Framework Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 79e54392bf5503b4b651f25ce6e5fc63d418df90
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616662"
---
# <a name="value-conversions"></a>Conversiones de valores

> [!NOTE]  
> Esta característica es nueva en EF Core 2.1.

Los convertidores de valores permiten convertir los valores de propiedad al leer o escribir en la base de datos. Esta conversión puede ser de un valor a otro del mismo tipo (por ejemplo, cifrar cadenas) o de un valor de un tipo a un valor de otro tipo (por ejemplo, convertir valores de enumeración en cadenas en la base de datos y desde ellas).

## <a name="fundamentals"></a>Aspectos básicos

Los convertidores de valores se especifican en términos de `ModelClrType` y `ProviderClrType` . El tipo de modelo es el tipo .NET de la propiedad en el tipo de entidad. El tipo de proveedor es el tipo .NET que entiende el proveedor de base de datos. Por ejemplo, para guardar las enumeraciones como cadenas en la base de datos, el tipo de modelo es el tipo de la enumeración y el tipo de proveedor es `String` . Estos dos tipos pueden ser iguales.

Las conversiones se definen mediante dos `Func` árboles de expresión: uno de `ModelClrType` a `ProviderClrType` y el otro de `ProviderClrType` a `ModelClrType` . Los árboles de expresión se usan para que se puedan compilar en el código de acceso a la base de datos para conversiones eficientes. En las conversiones complejas, el árbol de expresión puede ser una llamada simple a un método que realiza la conversión.

## <a name="configuring-a-value-converter"></a>Configurar un convertidor de valores

Las conversiones de valores se definen en las propiedades `OnModelCreating` del de `DbContext` . Por ejemplo, considere una enumeración y un tipo de entidad definidos como:

``` csharp
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

A continuación, se pueden definir conversiones en `OnModelCreating` para almacenar los valores de enumeración como cadenas (por ejemplo, "Donkey", "Mule",...) en la base de datos:

``` csharp
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
> `null`Nunca se pasará un valor a un convertidor de valores. Esto hace que la implementación de conversiones sea más sencilla y permite que se compartan entre propiedades que aceptan valores NULL y que no aceptan valores NULL.

## <a name="the-valueconverter-class"></a>La clase ValueConverter

Al llamar a `HasConversion` como se muestra anteriormente, se creará una `ValueConverter` instancia de y se establecerá en la propiedad. `ValueConverter`En su lugar, se puede crear explícitamente. Por ejemplo:

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Esto puede ser útil cuando varias propiedades usan la misma conversión.

> [!NOTE]  
> Actualmente no hay ninguna manera de especificar en un lugar que cada propiedad de un tipo determinado debe usar el mismo convertidor de valores. Esta característica se considerará para futuras versiones.

## <a name="built-in-converters"></a>Convertidores integrados

EF Core incluye un conjunto de clases predefinidas `ValueConverter` , que se encuentran en el `Microsoft.EntityFrameworkCore.Storage.ValueConversion` espacio de nombres. Dichos componentes son:

* `BoolToZeroOneConverter` -Bool a cero y uno
* `BoolToStringConverter` -Bool a cadenas como "Y" y "N"
* `BoolToTwoValuesConverter` -Bool a dos valores cualesquiera
* `BytesToStringConverter` -Matriz de bytes a cadena codificada en Base64
* `CastingConverter` -Conversiones que requieren solo una conversión de tipo
* `CharToStringConverter` -Char a cadena de carácter único
* `DateTimeOffsetToBinaryConverter` -DateTimeOffset a código binario codificado de 64 bits
* `DateTimeOffsetToBytesConverter` -DateTimeOffset a la matriz de bytes
* `DateTimeOffsetToStringConverter` -DateTimeOffset a cadena
* `DateTimeToBinaryConverter` -DateTime al valor de 64 bits, incluido DateTimeKind
* `DateTimeToStringConverter` -DateTime a cadena
* `DateTimeToTicksConverter` : Fecha y hora en pasos
* `EnumToNumberConverter` -Enum al número subyacente
* `EnumToStringConverter` -Enum a cadena
* `GuidToBytesConverter` -GUID a matriz de bytes
* `GuidToStringConverter` -GUID a cadena
* `NumberToBytesConverter` -Cualquier valor numérico a matriz de bytes
* `NumberToStringConverter` -Cualquier valor numérico a cadena
* `StringToBytesConverter` -Cadena en bytes UTF8
* `TimeSpanToStringConverter` -TimeSpan a cadena
* `TimeSpanToTicksConverter` -Intervalo de tiempo a pasos

Tenga en cuenta que `EnumToStringConverter` se incluye en esta lista. Esto significa que no es necesario especificar la conversión explícitamente, como se muestra anteriormente. En su lugar, use simplemente el convertidor integrado:

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Tenga en cuenta que todos los convertidores integrados no tienen estado y, por tanto, una sola instancia puede compartirse de forma segura con varias propiedades.

## <a name="pre-defined-conversions"></a>Conversiones predefinidas

En el caso de las conversiones comunes para las que existe un convertidor integrado, no es necesario especificar el convertidor explícitamente. En su lugar, solo tiene que configurar el tipo de proveedor que se debe usar y EF usará automáticamente el convertidor integrado adecuado. La enumeración de las conversiones de cadenas se usa como ejemplo anterior, pero EF lo hará automáticamente si se configura el tipo de proveedor:

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

Lo mismo se puede lograr especificando explícitamente el tipo de columna. Por ejemplo, si el tipo de entidad se define de la manera siguiente:

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

A continuación, los valores de enumeración se guardarán como cadenas en la base de datos sin ninguna configuración adicional en `OnModelCreating` .

## <a name="limitations"></a>Limitaciones

Hay algunas limitaciones actuales conocidas del sistema de conversión de valores:

* Como se indicó anteriormente, `null` no se puede convertir.
* Actualmente no hay ninguna manera de distribuir una conversión de una propiedad a varias columnas o viceversa.
* El uso de conversiones de valores puede afectar a la capacidad de EF Core para traducir expresiones a SQL. Se registrará una advertencia para dichos casos.
La eliminación de estas limitaciones se está considerando en una versión futura.
