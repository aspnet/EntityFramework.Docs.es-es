---
title: 'Consultas únicas frente a consultas divididas: EF Core'
description: Traducción de consultas en consultas únicas y divididas en SQL con Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 1857e05d45979c5530a349b9c7470407e7574fdf
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983435"
---
# <a name="split-queries"></a>Consultas divididas

## <a name="single-queries"></a>Consultas únicas

En las bases de datos relacionales, todas las entidades relacionadas se cargan mediante la introducción de instrucciones JOIN en consultas únicas.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Si un blog típico tiene varias entradas relacionadas, las filas de estas entradas duplicarán la información del blog, lo que genera un problema conocido como "explosión cartesiana". A medida que se cargan más relaciones uno a varios, la cantidad de datos duplicados puede crecer y afectar negativamente al rendimiento de la aplicación.

## <a name="split-queries"></a>Consultas divididas

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 5.0. Solo funciona cuando se usa `Include`. [Este problema](https://github.com/dotnet/efcore/issues/21234) realiza un seguimiento de la compatibilidad con la consulta dividida al cargar datos relacionados en la proyección sin `Include`.

EF le permite especificar que una consulta LINQ determinada se debe *dividir* en varias consultas SQL. En lugar de instrucciones JOIN, las consultas divididas generan una consulta SQL adicional por cada navegación de colección incluida:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

Generará la consulta SQL siguiente:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> Las entidades relacionadas uno a uno se cargan siempre mediante instrucciones JOIN en la misma consulta, ya que esto no afecta al rendimiento.

## <a name="enabling-split-queries-globally"></a>Habilitación de consultas divididas globalmente

También puede configurar consultas divididas como el valor predeterminado para el contexto de la aplicación:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Cuando las consultas divididas están configuradas como valor predeterminado, todavía es posible configurar consultas específicas para que se ejecuten como consultas únicas:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

EF Core usa el modo de consulta única de forma predeterminada si no existe ninguna configuración. Dado que puede producir problemas de rendimiento, EF Core genera una advertencia cuando se cumplen las condiciones siguientes:

- EF Core detecta que la consulta carga varias colecciones.
- El usuario no ha configurado el modo de división de consultas globalmente.
- El usuario no ha usado el operador `AsSingleQuery`/`AsSplitQuery` en la consulta.

Para desactivar la advertencia, configure el modo de división de consultas globalmente o en el nivel de consulta en un valor adecuado.

## <a name="characteristics-of-split-queries"></a>Características de las consultas divididas

Si bien las consultas divididas evitan las incidencias de rendimiento asociadas a las instrucciones JOIN y la explosión cartesiana, también existen algunas desventajas:

- Aunque la mayoría de las bases de datos garantizan la coherencia de los datos en las consultas únicas, no sucede lo mismo en el caso de varias consultas. Si la base de datos se actualiza de forma simultánea al ejecutar las consultas, es posible que los datos resultantes no sean coherentes. Esto se puede mitigar mediante el encapsulado de las consultas en una transacción serializable o de instantáneas, aunque esto puede generar incidencias propias de rendimiento. Para obtener más información, vea la documentación de la base de datos.
- Cada consulta implica actualmente un ciclo adicional de ida y vuelta de red a la base de datos. Varios ciclos de ida y vuelta de red pueden degradar el rendimiento, sobre todo si la latencia en la base de datos es alta (por ejemplo, servicios en la nube).
- Aunque algunas bases de datos permiten el consumo de los resultados de varias consultas al mismo tiempo (SQL Server con MARS, SQLite), la mayoría de ellas solo permiten sola consulta activa en un momento dado. Por lo tanto, todos los resultados de las consultas anteriores deben almacenarse en búfer en la memoria de la aplicación antes de ejecutar consultas posteriores, lo que llevará a un aumento en los requisitos de memoria.

Por desgracia, no hay una estrategia para cargar entidades relacionadas que se ajuste a todos los escenarios. Tenga en cuenta las ventajas y desventajas de las consultas únicas y divididas, para seleccionar la que mejor se ajuste a sus necesidades.
