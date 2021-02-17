---
title: 'Actualización eficaz: EF Core'
description: Guía de rendimiento para una actualización eficaz mediante Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: f8e222991af52cd7cae6089e95ad6634b6b949f8
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543138"
---
# <a name="efficient-updating"></a>Actualización eficaz

## <a name="batching"></a>Lotes

EF Core ayuda a minimizar los viajes de ida y vuelta agrupando automáticamente todas las actualizaciones en un solo viaje de ida y vuelta. Tenga en cuenta lo siguiente.

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

Lo anterior carga un blog de la base de datos, cambia su dirección URL y, a continuación, agrega dos blogs nuevos; para aplicar esto, se envían a la base de datos dos instrucciones INSERT de SQL y una instrucción UPDATE. En lugar de enviarlos uno a uno, a medida que se agregan instancias de blog, EF Core realiza un seguimiento de estos cambios internamente y los ejecuta en un solo ida y vuelta cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a.

El número de instrucciones que EF procesa por lotes en un único viaje de ida y vuelta depende del proveedor de base de datos utilizado. Por ejemplo, el análisis de rendimiento ha mostrado que el procesamiento por lotes sea menos eficaz para SQL Server cuando hay menos de 4 instrucciones implicadas. Del mismo modo, las ventajas del procesamiento por lotes se degradan después de alrededor de 40 instrucciones para SQL Server, por lo que EF Core solo ejecutará de forma predeterminada hasta 42 instrucciones en un único lote y ejecutará instrucciones adicionales en viajes de ida y vuelta independientes.

Los usuarios también pueden retocar estos umbrales para lograr un rendimiento potencialmente superior, pero con cuidado antes de modificarlos:

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a>Actualizaciones masivas

Supongamos que desea dar una elevación a todos los empleados. Una implementación típica para esto en EF Core sería similar a la siguiente:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

Aunque se trata de código perfectamente válido, vamos a analizar lo que hace desde la perspectiva del rendimiento:

* Se realiza un ida y vuelta de base de datos para cargar todos los empleados pertinentes. Tenga en cuenta que esto pone todos los datos de fila de los empleados en el cliente, incluso si solo se necesita el salario.
* El seguimiento de cambios de EF Core crea instantáneas al cargar las entidades y, a continuación, compara esas instantáneas con las instancias para averiguar qué propiedades han cambiado.
* Se realiza un segundo viaje de base de datos para guardar todos los cambios. Aunque todos los cambios se realizan en un único viaje de ida y vuelta, el procesamiento por lotes EF Core sigue enviando una instrucción UPDATE por empleado, que debe ser ejecutada por la base de datos.

Las bases de datos relacionales también admiten *actualizaciones masivas*, por lo que lo anterior podría volver a escribirse como la siguiente instrucción SQL:

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

Esto realiza la operación completa en un único ciclo de ida y vuelta, sin cargar ni enviar ningún dato real a la base de datos, y sin hacer uso de la maquinaria de seguimiento de cambios de EF, que impone una sobrecarga adicional.

Desafortunadamente, EF no proporciona actualmente las API para realizar actualizaciones masivas. Hasta que se introduzcan, puede usar SQL sin procesar para realizar la operación en la que el rendimiento es sensible:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
