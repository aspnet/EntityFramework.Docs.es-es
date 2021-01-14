---
title: Información general de Entity Framework Core - EF Core
description: Información general de Entity Framework Core
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: f2905fc55419e196a6f5b8883cf2a2a76b8a1ab2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128607"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core es una versión ligera, extensible, [de código abierto](https://github.com/dotnet/efcore) y multiplataforma de la popular tecnología de acceso a datos Entity Framework.

EF Core puede actuar como asignador relacional de objetos, que se encarga de lo siguiente:

* Permite a los desarrolladores de .NET trabajar con una base de datos usando objetos .NET.
* Permite prescindir de la mayor parte del código de acceso a datos que normalmente es necesario escribir.

EF Core es compatible con muchos motores de base de datos; vea [Proveedores de bases de datos](xref:core/providers/index) para más información.

## <a name="the-model"></a>El modelo

Con EF Core, el acceso a datos se realiza mediante un modelo. Un modelo se compone de clases de entidad y un objeto de contexto que representa una sesión con la base de datos. Este objeto de contexto permite consultar y guardar datos. Para más información, vea [Creación de un modelo](xref:core/modeling/index).

EF admite los siguientes métodos de desarrollo de modelos:

* Generar un modelo a partir de una base de datos existente.
* Codificar un modelo manualmente para que coincida con la base de datos.
* Una vez creado un modelo, usar [Migraciones de EF](xref:core/managing-schemas/migrations/index) para crear una base de datos a partir del modelo. Migraciones permite que la base de datos evolucione a medida que el modelo va cambiando.

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>Consultas

Las instancias de las clases de entidad se recuperan de la base de datos por medio de [Language Integrated Query (LINQ)](/dotnet/csharp/programming-guide/concepts/linq/). Para más información, vea [Consulta de datos](xref:core/querying/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>Guardado de datos

Los datos se crean, se eliminan y se modifican en la base de datos mediante instancias de las clases de entidad. Vea [Guardado de datos](xref:core/saving/index) para más información.

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a>Consideraciones de EF O/RM

Mientras que EF Core es bueno extrayendo muchos detalles de programación, existen algunos procedimientos recomendados válidos para cualquier O/RM que ayudan a evitar errores comunes en las aplicaciones de producción:

* Tener un conocimiento medio o superior del servidor de base de datos subyacente es esencial para generar perfiles, diseñar, depurar y migrar datos en aplicaciones de producción de alto rendimiento (por ejemplo, conocer las claves principales y externas, las restricciones, los índices, la normalización, las instrucciones DML y DDL, los tipos de datos, los perfiles, etc.).
* Pruebas funcionales y de integración.  Es importante replicar el entorno de producción de la forma más próxima posible para permitir lo siguiente:
  * Encontrar problemas en la aplicación que solo se revelan cuando se usa una edición o una versión específica del servidor de base de datos.
  * Detectar cambios importantes al actualizar EF Core y otras dependencias (por ejemplo, agregar o actualizar marcos como ASP.NET Core, OData o AutoMapper). Estas dependencias pueden afectar a EF Core de formas imprevistas.
* Pruebas de rendimiento y esfuerzo con cargas representativas. El uso irreflexivo de algunas características no escala bien. Por ejemplo, varias colecciones Includes, el uso intensivo de cargas diferidas, las consultas condicionales en columnas no indexadas, las inserciones y actualizaciones masivas con valores generados por el almacén, la falta de control de la simultaneidad, el uso de modelos grandes o unas directivas inadecuadas de almacenamiento en memoria caché.
* Revisión de seguridad: por ejemplo, el control de las cadenas de conexión y otros secretos, los permisos de base de datos para operaciones de no implementación, la validación de entradas para SQL sin procesar o el cifrado de datos confidenciales.
* Asegúrese de que las capacidades de registro y diagnóstico son suficientes y utilizables, por ejemplo, una configuración de registros adecuada, etiquetas de consulta y Application Insights.
* Recuperación de errores. Prepare contingencias para afrontar escenarios de error comunes, como reversiones de versión, servidores de reserva, escalabilidad horizontal y equilibrio de carga, la mitigación de DoS y copias de seguridad de datos.
* Implementación y migración de aplicaciones. Planee cómo se van a aplicar las migraciones durante la implementación, ya que hacerlo en el inicio de una aplicación puede derivar en problemas de simultaneidad y requiere permisos más elevados de lo necesario para lograr un funcionamiento normal. Use el almacenamiento provisional para facilitar la recuperación de errores irrecuperables durante la migración. Para más información, vea [Aplicación de migraciones](xref:core/managing-schemas/migrations/applying).
* Examen y prueba detallados de las migraciones generadas. Las migraciones se deben comprobar detenidamente antes de aplicarse a los datos de producción. La forma del esquema y los tipos de columna no son fáciles de cambiar una vez que las tablas contienen datos de producción. Por ejemplo, en SQL Server, `nvarchar(max)` y `decimal(18, 2)` no suelen ser los mejores tipos para las columnas asignadas a propiedades de cadena y decimal, pero son los valores predeterminados que EF emplea porque desconoce cuál es su escenario específico.

## <a name="next-steps"></a>Pasos siguientes

Para consultar tutoriales de introducción, vea [Introducción a Entity Framework Core](xref:core/get-started/overview/first-app).
