---
title: 'Asignaciones de función: proveedor de base de datos de SQLite-EF Core'
description: Asignaciones de función del proveedor de base de datos de SQLite EF Core
author: bricelam
ms.date: 1/26/2021
uid: core/providers/sqlite/functions
ms.openlocfilehash: 66fb24219465b8c34407d279450b7d501e7a572d
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543216"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>Asignaciones de función del proveedor de EF Core de SQLite

En esta página se muestran los miembros de .NET que se traducen en SQL Functions cuando se usa el proveedor de SQLite.

## <a name="binary-functions"></a>Funciones binarias

.NET                                           | SQL                                  | Agregado en
---------------------------------------------- | ------------------------------------ | --------
bytes. Contains (valor)                          | instr ( @bytes , Char ( @value )) > 0      | EF Core 5.0
bytes. Longitud                                   | longitud ( @bytes )                       | EF Core 5.0
bytes. SequenceEqual (segundo)                    | @bytes = @second                     | EF Core 5.0
EF. Functions. hex (bytes)                        | Hex ( @bytes )                          | EF Core 6.0
EF. Functions. substr (bytes, startIndex)         | substr ( @bytes , @startIndex )          | EF Core 6.0
EF. Functions. substr (bytes, startIndex, longitud) | substr ( @bytes , @startIndex , @length ) | EF Core 6.0

## <a name="conversion-functions"></a>Funciones de conversión

.NET                      | SQL                           | Agregado en
------------------------- | ----------------------------- | --------
boolValue. ToString ()      | CAST ( @boolValue como texto)      | EF Core 6.0
byteValue. ToString ()      | CAST ( @byteValue como texto)      | EF Core 6.0
bytes. ToString ()          | CAST ( @bytes como texto)          | EF Core 6.0
charValue. ToString ()      | CAST ( @charValue como texto)      | EF Core 6.0
dateTime. ToString ()       | CAST ( @dateTime como texto)       | EF Core 6.0
dateTimeOffset. ToString () | CAST ( @dateTimeOffset como texto) | EF Core 6.0
decimalValue. ToString ()   | CAST ( @decimalValue como texto)   | EF Core 6.0
doubleValue. ToString ()    | CAST ( @doubleValue como texto)    | EF Core 6.0
floatValue. ToString ()     | CAST ( @floatValue como texto)     | EF Core 6.0
volumen. ToString ()           | CAST ( @guid como texto)           | EF Core 6.0
intValue. ToString ()       | CAST ( @intValue como texto)       | EF Core 6.0
longValue. ToString ()      | CAST ( @longValue como texto)      | EF Core 6.0
sbyteValue. ToString ()     | CAST ( @sbyteValue como texto)     | EF Core 6.0
shortValue. ToString ()     | CAST ( @shortValue como texto)     | EF Core 6.0
timeSpan. ToString ()       | CAST ( @timeSpan como texto)       | EF Core 6.0
uintValue. ToString ()      | CAST ( @uintValue como texto)      | EF Core 6.0
ushortValue. ToString ()    | CAST ( @ushortValue como texto)    | EF Core 6.0

## <a name="date-and-time-functions"></a>Funciones de fecha y hora

.NET                            | SQL                                                                      | Agregado en
------------------------------- | ------------------------------------------------------------------------ | --------
DateTime.Now                    | DateTime (' Now ', ' localtime ')
Fecha y hora. hoy                  | DateTime (' Now ', ' localtime ', ' Start of Day ')
DateTime.UtcNow                 | DateTime (' Now ')
dateTime. AddDays (valor)         | DateTime ( @dateTime , @value \| \| ' Days ')
dateTime. AddHours (valor)        | DateTime ( @dateTime , @d \| \| ' Hours ')
dateTime. AddMilliseconds (valor) | DateTime ( @dateTime , ( @value /1000,0) \| \| ' seconds ')                   | EF Core 2.2
dateTime. AddMinutes (valor)      | DateTime ( @dateTime , @value \| \| ' minutes ')
dateTime. AddMonths (meses)      | DateTime ( @dateTime , @months \| \| ' months ')
dateTime. AddSeconds (valor)      | DateTime ( @dateTime , @value \| \| ' seconds ')
dateTime. AddTicks (valor)        | DateTime ( @dateTime , ( @value /10000000,0) \| \| ' seconds ')               | EF Core 2.2
dateTime. AddYears (valor)        | DateTime ( @dateTime , @value \| \| ' years ')
Fecha y hora                   | DateTime ( @dateTime , ' Start of Day ')
dateTime. Day                    | strftime ('% d ', @dateTime )
dateTime. DayOfWeek              | strftime ('% w ', @dateTime )                                                | EF Core 2.2
dateTime. DayOfYear              | strftime ('% j ', @dateTime )
Fecha y hora                   | strftime ('% H ', @dateTime )
dateTime. Milliseconds            | (strftime ('% f ', @dateTime ) * 1000) %1000
dateTime. minute                 | strftime ('% M ', @dateTime )
dateTime. month                  | strftime ('% m ', @dateTime )
dateTime. Second                 | strftime ('% S ', @dateTime )
dateTime. Ticks                  | (julianday ( @dateTime )-julianday (' 0001-01-01 00:00:00 ')) * 864 mil millones | EF Core 2.2
dateTime. TimeOfDay              | hora ( @dateTime )                                                          | EF Core 3.0
dateTime. Year                   | strftime ('% Y ', @dateTime )

> [!NOTE]
> Algunos SQL se han simplificado con fines ilustrativos. El SQL real es más complejo para controlar un rango más amplio de valores.

## <a name="numeric-functions"></a>Funciones numéricas

.NET                  | SQL                                   | Agregado en
--------------------- | ------------------------------------- | --------
-decimalValue         | ef_negate ( @decimalValue )              | EF Core 5.0
decimalValue-d      | ef_add ( @decimalValue , ef_negate ( @d ))  | EF Core 5.0
decimalValue * d      | ef_multiply ( @decimalValue , @d )        | EF Core 5.0
decimalValue/d      | ef_divide ( @decimalValue , @d )          | EF Core 5.0
decimalValue% d      | ef_mod ( @decimalValue , @d )             | EF Core 5.0
decimalValue + d      | ef_add ( @decimalValue , @d )             | EF Core 5.0
decimalValue < d      | ef_compare ( @decimalValue , @d ) < 0     | EF Core 5.0
<decimalValue = d     | ef_compare ( @decimalValue , @d ) <= 0    | EF Core 5.0
decimalValue > d      | ef_compare ( @decimalValue , @d ) > 0     | EF Core 5.0
>decimalValue = d     | ef_compare ( @decimalValue , @d ) >= 0    | EF Core 5.0
doubleValue% d       | ef_mod ( @doubleValue , @d )              | EF Core 5.0
EF. Functions. RANDOM () | ABS (Random ()/9223372036854780000.0) | EF Core 6.0
floatValue% d        | ef_mod ( @floatValue , @d )               | EF Core 5.0
Math. ABS (valor)       | ABS ( @value )
Math. Max (val1, val2)  | Max ( @val1 , @val2 )
Math. min (val1, val2)  | min ( @val1 , @val2 )
Math. round (d)         | Round ( @d )
Math. round (d, digits) | Round ( @d , @digits )

> [!TIP]
> Las funciones SQL con el prefijo *EF* se crean mediante EF Core.

## <a name="string-functions"></a>Funciones de cadena

.NET                                                         | SQL                                                    | Agregado en
------------------------------------------------------------ | ------------------------------------------------------ | --------
Juegos. ToLower (c)                                              | Lower ( @c )                                              | EF Core 6.0
Juegos. ToUpper (c)                                              | Upper ( @c )                                              | EF Core 6.0
EF. Functions. COLLATE (operando, intercalación)                     | @operand COLLATE @collation                            | EF Core 5.0
EF. Functions. glob (matchExpression, Pattern)                  | Glob ( @pattern , @matchExpression )                       | EF Core 6.0
EF. Functions. like (matchExpression, Pattern)                  | @matchExpression FORMA @pattern
EF. Functions. like (matchExpression, Pattern, escapeCharacter) | @matchExpression COMO @pattern escape @escapeCharacter
Regex. IsMatch (entrada, patrón)                                | RegExp ( @pattern , @input )                               | EF Core 6.0
String. Compare (strA, strB)                                   | CASE WHEN @strA = @strB 0... EXTREMO
String@. Concat (str0, Str1)                                    | @str0 \|\| @str1
String@. IsNullOrEmpty (valor)                                  | @value ES NULL o @value = ' '
String@. IsNullOrWhiteSpace (valor)                             | @value ES NULL o Trim ( @value ) = ' '
Valorstring. CompareTo (strB)                                  | CASE WHEN @stringValue = @strB 0... EXTREMO
Valorstring. Contains (valor)                                  | instr ( @stringValue , @value ) > 0
Valorstring. EndsWith (valor)                                  | @stringValueLIKE '% ' \| \|@value
Valorstring. FirstOrDefault ()                                 | substr ( @stringValue , 1,1)                             | EF Core 5.0
Valorstring. IndexOf (valor)                                   | instr ( @stringValue , @value )-1
Valorstring. LastOrDefault ()                                  | substr ( @stringValue , longitud ( @stringValue ), 1)          | EF Core 5.0
Valorstring. length                                           | longitud ( @stringValue )
Valorstring. Replace (oldValue, newValue)                      | reemplazar ( @stringValue , @oldValue , @newValue )
Valorstring. StartsWith (valor)                                | @stringValueLIKE @value \| \| '% '
Valorstring. substring (startIndex, longitud)                    | substr ( @stringValue , @startIndex + 1, @length )
Valorstring. ToLower ()                                        | Lower ( @stringValue )
Valorstring. ToUpper ()                                        | Upper ( @stringValue )
Valorstring. Trim ()                                           | Trim ( @stringValue )
Valorstring. Trim (trimChar)                                   | Trim ( @stringValue , @trimChar )
Valorstring. TrimEnd ()                                        | RTRIM ( @stringValue )
Valorstring. TrimEnd (trimChar)                                | RTRIM ( @stringValue , @trimChar )
Valorstring. TrimStart ()                                      | ltrim ( @stringValue )
Valorstring. TrimStart (trimChar)                              | ltrim ( @stringValue , @trimChar )

> [!NOTE]
> Algunos SQL se han simplificado con fines ilustrativos. El SQL real es más complejo para controlar un rango más amplio de valores.

## <a name="miscellaneous-functions"></a>Funciones misceláneas

.NET                                     | SQL                                | Agregado en
---------------------------------------- | ---------------------------------- | --------
Collection. Contains (Item)                | @item DE @collection               | EF Core 3.0
enumValue. HasFlag (marca)                  | @enumValue & @flag = @flag
acepta valores NULL. GetValueOrDefault ()             | Coalesce ( @nullable , 0)
acepta valores NULL. GetValueOrDefault (defaultValue) | Coalesce ( @nullable , @defaultValue )

## <a name="see-also"></a>Consulte también

* [Asignaciones de funciones espaciales](xref:core/providers/sqlite/spatial#spatial-function-mappings)
