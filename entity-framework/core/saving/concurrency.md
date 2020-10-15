---
title: 'Administración de los conflictos de simultaneidad: EF Core'
description: Administración de conflictos cuando los mismos datos se actualizan de forma simultánea con Entity Framework Core
author: ajcvickers
ms.date: 03/03/2018
uid: core/saving/concurrency
ms.openlocfilehash: b596a99db431331bb12a28fc6ddc06f1c941b67c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063028"
---
# <a name="handling-concurrency-conflicts"></a>Administrar los conflictos de simultaneidad

> [!NOTE]
> En esta página se documenta cómo funciona la simultaneidad en EF Core y cómo administrar los conflictos de simultaneidad en la aplicación. Consulte [Concurrency Tokens](xref:core/modeling/concurrency) (Tokens de simultaneidad) para detalles sobre cómo configurar los tokens de simultaneidad en el modelo.

> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/) de este artículo en GitHub.

La _simultaneidad de base de datos_ se refiere a las situaciones en las que varios procesos o usuarios acceden o cambian los mismos datos de una base de datos al mismo tiempo. El _control de simultaneidad_ se refiere a los mecanismos específicos que se usan para garantizar la coherencia de los datos en presencia de cambios simultáneos.

EF Core implementa el _control de simultaneidad optimista_, lo que significa que permitirá que varios procesos o usuarios hagan cambios de manera independiente sin la sobrecarga que implica la sincronización o el bloqueo. En la situación ideal, estos cambios no interferirán entre sí y, por tanto, se realizarán correctamente. En el peor escenario, dos o más procesos intentarán hacer cambios conflictivos y solo uno de ellos se completará correctamente.

## <a name="how-concurrency-control-works-in-ef-core"></a>Funcionamiento del control de simultaneidad en EF Core

Las propiedades configuradas como tokens de simultaneidad se usan para implementar el control de simultaneidad optimista: cada vez que se realiza una operación de actualización o eliminación durante `SaveChanges`, el valor del token de simultaneidad en la base de datos se compara con el valor original leído por EF Core.

- Si los valores coinciden, la operación se puede completar.
- Si no coinciden, EF Core supone que otro usuario realizó una operación en conflicto y anula la transacción actual.

La situación en que otro usuario realizó una operación que entra en conflicto con la operación actual se conoce como _conflicto de simultaneidad_.

Los proveedores de base de datos son responsable de implementar la comparación de los valores de los tokens de simultaneidad.

En las bases de datos relacionales, EF Core incluye una comprobación del valor del token de simultaneidad en la cláusula `WHERE` de cualquier instrucción `UPDATE` o `DELETE`. Después de ejecutar las instrucciones, EF Core lee el número de filas que se vieron afectadas.

Si no se afectó ninguna fila, se detecta un conflicto de simultaneidad y EF Core genera la excepción `DbUpdateConcurrencyException`.

Por ejemplo, queremos configurar `LastName` en `Person` como token de simultaneidad. Luego, toda operación de actualización en Person incluirá la comprobación de la simultaneidad en la cláusula `WHERE`:

```sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>Resolución de los conflictos de simultaneidad

Siguiendo con el ejemplo anterior, si un usuario intenta guardar algunos cambios en una `Person` pero otro usuario ya cambió `LastName`, se generará una excepción.

En este punto, la aplicación simplemente podría informar al usuario que la actualización no se realizó correctamente debido a cambios en conflicto y siguió adelante. Pero podría ser conveniente pedirle al usuario que se asegure de que este registro sigue representando a la misma persona real y que reintente la operación.

Este proceso es un ejemplo de cómo _resolver un conflicto de simultaneidad_.

Resolver un conflicto de simultaneidad implica combinar los cambios pendientes del `DbContext` actual con los valores de la base de datos. Los valores que se van a combinar variarán en función de la aplicación y los puede dirigir el mismo usuario.

**Existen tres conjuntos de valores disponibles para ayudar a resolver un conflicto de simultaneidad:**

- Los **valores actuales** son los valores que la aplicación intentó escribir en la base de datos.
- Los **valores originales** son los valores que se recuperaron originalmente de la base de datos, antes de realizar cualquier edición.
- Los **valores de base de datos** son los valores actualmente almacenados en la base de datos.

El enfoque general para controlar un conflicto de simultaneidad es:

1. Detecte `DbUpdateConcurrencyException` durante `SaveChanges`.
2. Use `DbUpdateConcurrencyException.Entries` para preparar un nuevo conjunto de cambios para las entidades afectadas.
3. Actualice los valores originales del token de simultaneidad para reflejar los valores actuales en la base de datos.
4. Reintente el proceso hasta que no haya ningún conflicto.

En el ejemplo siguiente, `Person.FirstName` y `Person.LastName` están configurados como tokens de simultaneidad. Hay un comentario `// TODO:` en la ubicación donde se incluye la lógica específica de la aplicación para elegir el valor que se guardará.

[!code-csharp[Main](../../../samples/core/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=33-34)]
