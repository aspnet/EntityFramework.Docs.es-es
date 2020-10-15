---
title: 'Asignaciones de función: proveedor de base de datos de Microsoft SQL Server-EF Core'
description: Asignaciones de función del proveedor de base de datos de Microsoft SQL Server
author: bricelam
ms.date: 10/07/2020
uid: core/providers/sql-server/functions
ms.openlocfilehash: 8eb66521b00f2f4879a098200239849c7219a095
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066575"
---
# <a name="function-mappings-of-the-microsoft-sql-server-provider"></a>Asignaciones de función del proveedor de Microsoft SQL Server

En esta página se muestran los miembros de .NET que se traducen en SQL Functions cuando se usa el proveedor de SQL Server.

## <a name="binary-functions"></a>Funciones binarias

.NET                         | SQL                           | Agregado en
---------------------------- | ----------------------------- | --------
bytes. Contains (valor)        | CHARINDEX ( @value , @bytes ) > 0 | EF Core 5.0
bytes. Longitud                 | DATALENGTH ( @bytes )            | EF Core 5.0
bytes. SequenceEqual (segundo)  | @bytes = @second              | EF Core 5.0
EF. Functions. DATALENGTH (Arg) | DATALENGTH ( @arg )              | EF Core 5.0

## <a name="conversion-functions"></a>Funciones de conversión

.NET                      | SQL                                    | Agregado en
------------------------- | -------------------------------------- | --------
bytes. ToString ()          | CONVERT (varchar (100), @bytes )
byteValue. ToString ()      | CONVERT (varchar (3), @byteValue )
charValue. ToString ()      | CONVERT (varchar (1), @charValue )
Convert. ToBoolean (valor)  | CONVERT (bit, @value )                   | EF Core 5.0
Convert. ToByte (valor)     | CONVERT (tinyint, @value )
Convert. ToDecimal (valor)  | CONVERT (decimal (18, 2), @value )
Convert. ToDouble (valor)   | CONVERT (float, @value )
Convert. ToInt16 (valor)    | CONVERT (smallint, @value )
Convert. ToInt32 (valor)    | CONVERT (int, @value )
Convert. ToInt64 (valor)    | CONVERT (BIGINT, @value )
Convert. ToString (valor)   | CONVERT (nvarchar (Max), @value )
dateTime. ToString ()       | CONVERT (varchar (100), @dateTime )
dateTimeOffset. ToString () | CONVERT (varchar (100), @dateTimeOffset )
decimalValue. ToString ()   | CONVERT (varchar (100), @decimalValue )
doubleValue. ToString ()    | CONVERT (varchar (100), @doubleValue )
floatValue. ToString ()     | CONVERT (varchar (100), @floatValue )
volumen. ToString ()           | CONVERT (varchar (36), @guid )
intValue. ToString ()       | CONVERT (varchar (11), @intValue )
longValue. ToString ()      | CONVERT (varchar (20), @longValue )
sbyteValue. ToString ()     | CONVERT (varchar (4), @sbyteValue )
shortValue. ToString ()     | CONVERT (varchar (6), @shortValue )
timeSpan. ToString ()       | CONVERT (varchar (100), @timeSpan )
uintValue. ToString ()      | CONVERT (varchar (10), @uintValue )
ulongValue. ToString ()     | CONVERT (varchar (19), @ulongValue )
ushortValue. ToString ()    | CONVERT (varchar (5), @ushortValue )

## <a name="date-and-time-functions"></a>Funciones de fecha y hora

.NET                                                        | SQL                                                  | Agregado en
----------------------------------------------------------- | ---------------------------------------------------- | --------
DateTime.Now                                                | GETDATE ()
Fecha y hora. hoy                                              | CONVERT (fecha, GETDATE ())
DateTime.UtcNow                                             | GETUTCDATE ()
dateTime. AddDays (valor)                                     | DATEADD (día, @value , @dateTime )
dateTime. AddHours (valor)                                    | DATEADD (hora, @value , @dateTime )
dateTime. AddMilliseconds (valor)                             | DATEADD (milisegundo, @value , @dateTime )
dateTime. AddMinutes (valor)                                  | DATEADD (minuto, @value , @dateTime )
dateTime. AddMonths (meses)                                  | DATEADD (mes, @months , @dateTime )
dateTime. AddSeconds (valor)                                  | DATEADD (segundo, @value , @dateTime )
dateTime. AddYears (valor)                                    | DATEADD (año, @value , @dateTime )
Fecha y hora                                               | CONVERT (fecha, @dateTime )
dateTime. Day                                                | DATEPART (día, @dateTime )
dateTime. DayOfYear                                          | DATEPART (DayOfYear, @dateTime )
Fecha y hora                                               | DATEPART (hora, @dateTime )
dateTime. Milliseconds                                        | DATEPART (milisegundo, @dateTime )
dateTime. minute                                             | DATEPART (minuto, @dateTime )
dateTime. month                                              | DATEPART (mes, @dateTime )
dateTime. Second                                             | DATEPART (segundo, @dateTime )
dateTime. TimeOfDay                                          | CONVERTIR (tiempo, @dateTime )                              | EF Core 2.2
dateTime. Year                                               | DATEPART (año, @dateTime )
DateTimeOffset.Now                                          | SYSDATETIMEOFFSET ()
DateTimeOffset. UtcNow                                       | SYSUTCDATETIME ()
dateTimeOffset. AddDays (días)                                | DATEADD (día, @days , @dateTimeOffset )
dateTimeOffset. AddHours (horas)                              | DATEADD (hora, @hours , @dateTimeOffset )
dateTimeOffset. AddMilliseconds (milisegundos)                | DATEADD (milisegundo, @milliseconds , @dateTimeOffset )
dateTimeOffset. AddMinutes (minutos)                          | DATEADD (minuto, @minutes , @dateTimeOffset )
dateTimeOffset. AddMonths (meses)                            | DATEADD (mes, @months , @dateTimeOffset )
dateTimeOffset. AddSeconds (segundos)                          | DATEADD (segundo, @seconds , @dateTimeOffset )
dateTimeOffset. AddYears (años)                              | DATEADD (año, @years , @dateTimeOffset )
dateTimeOffset. Date                                         | CONVERT (fecha, @dateTimeOffset )
dateTimeOffset. Day                                          | DATEPART (día, @dateTimeOffset )
dateTimeOffset. DayOfYear                                    | DATEPART (DayOfYear, @dateTimeOffset )
dateTimeOffset. hour                                         | DATEPART (hora, @dateTimeOffset )
dateTimeOffset. milisegundos                                  | DATEPART (milisegundo, @dateTimeOffset )
dateTimeOffset. minute                                       | DATEPART (minuto, @dateTimeOffset )
dateTimeOffset. month                                        | DATEPART (mes, @dateTimeOffset )
dateTimeOffset. Second                                       | DATEPART (segundo, @dateTimeOffset )
dateTimeOffset. TimeOfDay                                    | CONVERTIR (tiempo, @dateTimeOffset )                        | EF Core 2.2
dateTimeOffset. Year                                         | DATEPART (año, @dateTimeOffset )
EF. Functions. DateDiffDay (Inicio, finalización)                        | DATEDIFF (día, @start , @end )
EF. Functions. DateDiffHour (Inicio, finalización)                       | DATEDIFF (hora, @start , @end )
EF. Functions. DateDiffMicrosecond (Inicio, finalización)                | DATEDIFF (microsegundo, @start , @end )
EF. Functions. DateDiffMillisecond (Inicio, finalización)                | DATEDIFF (milisegundo, @start , @end )
EF. Functions. DateDiffMinute (Inicio, finalización)                     | DATEDIFF (minuto, @start , @d2 )
EF. Functions. DateDiffMonth (Inicio, finalización)                      | DATEDIFF (mes, @start , @end )
EF. Functions. DateDiffNanosecond (Inicio, finalización)                 | DATEDIFF (nanosegundo, @start , @end )
EF. Functions. DateDiffSecond (Inicio, finalización)                     | DATEDIFF (segundo, @start , @end )
EF. Functions. DateDiffWeek (Inicio, finalización)                       | DATEDIFF (semana, @start , @end )                         | EF Core 5.0
EF. Functions. DateDiffYear (Inicio, finalización)                       | DATEDIFF (año, @start , @end )
EF. Functions. DateFromParts (año, mes, día)                | DATEFROMPARTS ( @year , @month , @day )                   | EF Core 5.0
EF. Functions. DateTime2FromParts (año, mes, día,...)      | DATETIME2FROMPARTS ( @year , @month , @day ,...)         | EF Core 5.0
EF. Functions. DateTimeFromParts (año, mes, día,...)       | DATETIMEFROMPARTS ( @year , @month , @day ,...)          | EF Core 5.0
EF. Functions. DateTimeOffsetFromParts (año, mes, día,...) | DATETIMEOFFSETFROMPARTS ( @year , @month , @day ,...)    | EF Core 5.0
EF. Functions. IsDate (expresión)                             | ISDATE ( @expression )                                  | EF Core 3.0
EF. Functions. SmallDateTimeFromParts (año, mes, día,...)  | SMALLDATETIMEFROMPARTS ( @year , @month , @day ,...)     | EF Core 5.0
EF. Functions. TimeFromParts (hora, minuto, segundo,...)       | TIMEFROMPARTS ( @hour , @minute , @second ,...)          | EF Core 5.0
timeSpan. hours                                              | DATEPART (hora, @timeSpan )                            | EF Core 5.0
timeSpan. Milliseconds                                       | DATEPART (milisegundo, @timeSpan )                     | EF Core 5.0
timeSpan. minutes                                            | DATEPART (minuto, @timeSpan )                          | EF Core 5.0
timeSpan. seconds                                            | DATEPART (segundo, @timeSpan )                          | EF Core 5.0

## <a name="numeric-functions"></a>Funciones numéricas

.NET                    | SQL
----------------------- | ---
Math. ABS (valor)         | ABS ( @value )
Math. ACOS (d)            | ACOS ( @d )
Math. Asin (d)            | ASIN ( @d )
Math. atan (d)            | ATAN ( @d )
Math. Atan2 (y, x)        | ATN2 ( @y , @x )
Math. Ceiling (d)         | CEILING ( @d )
Math. cos (d)             | COS ( @d )
Math. exp (d)             | EXP ( @d )
Math. Floor (d)           | FLOOR ( @d )
Math. log (d)             | REGISTRO ( @d )
Math. log (a, newBase)    | REGISTRO ( @a , @newBase )
Math. Log10 (d)           | LOG10 ( @d )
Math. Pow (x, y)          | ALIMENTACIÓN ( @x , @y )
Math. round (d)           | ROUND ( @d ,0)
Math. round (d, decimales) | ROUND ( @d , @decimals )
Math. Sign (valor)        | FIRMAR ( @value )
Math. sin (a)             | SIN ( @a )
Math. sqrt (d)            | SQRT ( @d )
Math. tan (a)             | TAN ( @a )
Math. TRUNCATE (d)        | ROUND ( @d , 0, 1)

## <a name="string-functions"></a>Funciones de cadena

.NET                                                                    | SQL                                                                    | Agregado en
----------------------------------------------------------------------- | ---------------------------------------------------------------------- | --------
EF. Functions. COLLATE (operando, intercalación)                                | @operand COLLATE @collation                                            | EF Core 5.0
EF. Functions. Contains (propertyReference, searchCondition)               | Contains ( @propertyReference , @searchCondition )                         | EF Core 2.2
EF. Functions. Contains (propertyReference, searchCondition, languageTerm) | Contains ( @propertyReference , @searchCondition , Language @languageTerm ) | EF Core 2.2
EF. Functions. FreeText (propertyReference, freeText)                      | FREETEXT ( @propertyReference , @freeText )
EF. Functions. FreeText (propertyReference, freeText, languageTerm)        | FREETEXT ( @propertyReference , @freeText , idioma @languageTerm )
EF. Functions. like (matchExpression, Pattern)                             | @matchExpression FORMA @pattern
EF. Functions. like (matchExpression, Pattern, escapeCharacter)            | @matchExpression COMO @pattern escape @escapeCharacter
String. Compare (strA, strB)                                              | CASE WHEN @strA = @strB 0... EXTREMO
String@. Concat (str0, Str1)                                               | @str0 + @str1
String@. IsNullOrEmpty (valor)                                             | @value ES NULL o @value = N ' '
String@. IsNullOrWhiteSpace (valor)                                        | @value ES NULL o LTRIM (RTRIM ( @value )) = N ' '
Valorstring. CompareTo (strB)                                             | CASE WHEN @stringValue = @strB 0... EXTREMO
Valorstring. Contains (valor)                                             | @stringValue COMO N '% ' + @value + n '% '
Valorstring. EndsWith (valor)                                             | @stringValue COMO N '% ' + @value
Valorstring. FirstOrDefault ()                                            | Substring ( @stringValue , 1,1)                                          | EF Core 5.0
Valorstring. IndexOf (valor)                                              | CHARINDEX ( @value , @stringValue )-1
Valorstring. LastOrDefault ()                                             | Substring ( @stringValue , Len ( @stringValue ), 1)                          | EF Core 5.0
Valorstring. length                                                      | LEN ( @stringValue )
Valorstring. Replace ( @oldValue , @newValue )                               | REEMPLAZAR ( @stringValue , @oldValue , @newValue )
Valorstring. StartsWith (valor)                                           | @stringValue LIKE @value + N '% '
Valorstring. substring (startIndex, longitud)                               | Substring ( @stringValue , @startIndex + 1, @length )
Valorstring. ToLower ()                                                   | LOWER ( @stringValue )
Valorstring. ToUpper ()                                                   | UPPER ( @stringValue )
Valorstring. Trim ()                                                      | LTRIM (RTRIM ( @stringValue ))
Valorstring. TrimEnd ()                                                   | RTRIM ( @stringValue )
Valorstring. TrimStart ()                                                 | LTRIM ( @stringValue )

## <a name="miscellaneous-functions"></a>Funciones misceláneas

.NET                                     | SQL                                | Agregado en
---------------------------------------- | ---------------------------------- | --------
Collection. Contains (Item)                | @item DE @collection               | EF Core 3.0
enumValue. HasFlag (marca)                  | @enumValue & @flag = @flag
Guid.NewGuid ()                           | NEWID()
acepta valores NULL. GetValueOrDefault ()             | Coalesce ( @nullable , 0)
acepta valores NULL. GetValueOrDefault (defaultValue) | Coalesce ( @nullable , @defaultValue )

> [!NOTE]
> Algunos SQL se han simplificado con fines ilustrativos. El SQL real es más complejo para controlar un rango más amplio de valores.

## <a name="see-also"></a>Consulta también

* [Asignaciones de funciones espaciales](xref:core/providers/sql-server/spatial#spatial-function-mappings)
