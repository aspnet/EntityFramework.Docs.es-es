---
title: 'Establecimiento de valores explícitos para propiedades generadas: EF Core'
description: Información sobre cómo establecer valores de forma explícita para las propiedades configuradas como generadas con Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: b3a31d8139b244bec72347cf20600b6c2b65c7d2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063002"
---
# <a name="setting-explicit-values-for-generated-properties"></a>Establecimiento de valores explícitos para propiedades generadas

Una propiedad generada es una propiedad cuyo valor se genera (ya sea por medio de EF o de la base de datos) cuando la entidad se agrega o actualiza. Consulte [Generated Properties](xref:core/modeling/generated-properties) (Propiedades generadas) para más información.

Puede haber situaciones en las que desea establecer un valor explícito para una propiedad generada en lugar de hacer que se genere uno.

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/ExplicitValuesGenerateProperties/) de este artículo en GitHub.

## <a name="the-model"></a>El modelo

El modelo que se usa en este artículo contiene una entidad `Employee` única.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>Guardado de un valor explícito durante la acción de agregar

La propiedad `Employee.EmploymentStarted` está configurada para tener valores generados por la base de datos para las entidades nuevas (con un valor predeterminado).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

El código siguiente inserta dos empleados en la base de datos.

* Para el primero, no se asigna ningún valor a la propiedad `Employee.EmploymentStarted`, por lo que permanece establecido en el valor predeterminado de CLR para `DateTime`.
* En el caso del segundo, se establece un valor explícito de `1-Jan-2000`.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

La salida muestra que la base de datos generó un valor para el primer empleado y que el valor explícito se usó para el segundo.

```output
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>Valores explícitos en columnas IDENTITY de SQL Server

Por convención, la propiedad `Employee.EmployeeId` es una columna `IDENTITY` generada por el almacén.

En la mayoría de los casos, el enfoque anterior funcionará para las propiedades clave. Sin embargo, para insertar valores explícitos en una columna `IDENTITY` de SQL Server, deberá habilitar `IDENTITY_INSERT` manualmente antes de llamar a `SaveChanges()`.

> [!NOTE]
> Tenemos una [solicitud de característica](https://github.com/aspnet/EntityFramework/issues/703) en el trabajo pendiente para hacer esto de manera automática dentro del proveedor de SQL Server.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

La salida muestra que los identificadores suministrados se guardaron en la base de datos.

```output
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>Establecimiento de un valor explícito durante la acción de actualización

La propiedad `Employee.LastPayRaise` está configurada para tener valores generados por la base de datos durante las actualizaciones.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]
> De manera predeterminada, EF Core generará una excepción si se intenta guardar un valor explícito para una propiedad que se haya configurado para generarse durante la actualización. Para evitar esto, deberá descender a la API de metadatos de nivel inferior y establecer `AfterSaveBehavior` (como se mostró anteriormente).

También hay un desencadenador en la base de datos para generar valores para la columna `LastPayRaise` durante las operaciones `UPDATE`.

[!code-sql[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

El código siguiente aumenta el salario de ambos empleados en la base de datos.

* Para el primero, no se asigna ningún valor a la propiedad `Employee.LastPayRaise`, por lo que sigue establecido en NULL.
* Para el segundo, establecemos un valor explícito de una semana atrás (se antedata el aumento salarial).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

La salida muestra que la base de datos generó un valor para el primer empleado y que el valor explícito se usó para el segundo.

```output
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
