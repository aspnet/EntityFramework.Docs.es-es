---
title: Compartir bases de datos entre pruebas-EF Core
description: Ejemplo que muestra cómo compartir una base de datos entre varias pruebas
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564257"
---
# <a name="sharing-databases-between-tests"></a>Compartir bases de datos entre pruebas

En el [ejemplo de pruebas de EF Core](xref:core/miscellaneous/testing/testing-sample) se ha mostrado cómo probar aplicaciones en diferentes sistemas de base de datos.
Para ese ejemplo, cada prueba ha creado una nueva base de datos.
Este es un buen patrón al usar SQLite o la base de datos en memoria de EF, pero puede suponer una sobrecarga importante al usar otros sistemas de base de datos.

Este ejemplo se basa en el ejemplo anterior moviendo la creación de la base de datos a un accesorio de prueba.
Esto permite que una sola base de datos de SQL Server se cree y se inicialice solo una vez para todas las pruebas.

> [!TIP]
> Asegúrese de trabajar en el [ejemplo de prueba de EF Core](xref:core/miscellaneous/testing/testing-sample) antes de continuar.

No es difícil escribir varias pruebas en la misma base de datos.
El truco lo está haciendo de manera que las pruebas no viajen entre sí mientras se ejecutan.
Esto requiere comprender lo siguiente:
* Cómo compartir de forma segura objetos entre pruebas
* Cuando el marco de pruebas ejecuta pruebas en paralelo
* Cómo mantener la base de datos en un estado limpio para cada prueba  

## <a name="the-fixture"></a>El accesorio

Usaremos un accesorio de prueba para compartir objetos entre pruebas.
La [documentación de xUnit](https://xunit.net/docs/shared-context.html) indica que se debe usar un accesorio "Si desea crear un único contexto de prueba y compartirlo entre todas las pruebas de la clase y hacer que se limpie una vez finalizadas todas las pruebas de la clase".

> [!TIP]
> En este ejemplo se usa [xUnit](https://xunit.net/), pero existen conceptos similares en otros marcos de pruebas, incluido [NUnit](https://nunit.org/).

Esto significa que se debe trasladar la creación y propagación de la base de datos a una clase de accesorio.
Este es su aspecto:

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

Por ahora, observe cómo el constructor:
* Crea una conexión de base de datos única para la duración del accesorio.
* Crea y inicializa la base de datos llamando `Seed` al método. 

Omitir el bloqueo por ahora; volveremos a él más adelante.

> [!TIP]
> No es necesario que el código de creación y propagación sea asincrónico.
> Si se convierte en Async, se complicará el código y no se mejorará el rendimiento ni el rendimiento de las pruebas.

La base de datos se crea mediante la eliminación de cualquier base de datos existente y, a continuación, la creación de una nueva base de datos.
Esto garantiza que la base de datos coincida con el modelo EF actual incluso si se ha cambiado desde la última ejecución de pruebas.

> [!TIP]
> Puede ser más rápido "limpiar" la base de datos existente usando algo como [regenerar](https://jimmybogard.com/tag/respawn/) en lugar de volver a crearla cada vez.
> Sin embargo, debe tener cuidado para asegurarse de que el esquema de la base de datos esté actualizado con el modelo de EF al hacerlo.

La conexión a la base de datos se elimina cuando se desecha el accesorio.
También puede considerar la posibilidad de eliminar la base de datos de prueba en este momento.
Sin embargo, esto requerirá un recuento de referencias y bloqueo adicional si el accesorio se comparte con varias clases de prueba.
Además, a menudo resulta útil tener la base de datos de prueba todavía disponible para la depuración de pruebas con errores.  

## <a name="using-the-fixture"></a>Uso del accesorio

XUnit tiene un patrón común para asociar un accesorio de prueba con una clase de pruebas:

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

XUnit creará ahora una instancia de accesorio única y la pasará a cada instancia de la clase de prueba.
(Recuerde en el primer [ejemplo de prueba](xref:core/miscellaneous/testing/testing-sample) que xUnit crea una nueva instancia de clase de prueba cada vez que ejecuta una prueba). Esto significa que la base de datos se creará y se inicializará una vez y cada prueba utilizará esta base de datos.

Tenga en cuenta que las pruebas dentro de una sola clase no se ejecutarán en paralelo.
Esto significa que es seguro que cada prueba use la misma conexión de base de datos, aunque `DbConnection` el objeto no sea seguro para subprocesos.

## <a name="maintaining-database-state"></a>Mantenimiento de estado de base de datos

Las pruebas a menudo necesitan mutar los datos de prueba con inserciones, actualizaciones y eliminaciones.
Sin embargo, estos cambios afectarán a otras pruebas que esperan una base de datos limpia y propagada.

Esto se puede solucionar mediante la ejecución de pruebas mutadas dentro de una transacción.
Por ejemplo:

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

Observe que la transacción se crea cuando la prueba se inicia y se desecha cuando finaliza.
Al desechar la transacción, ésta se revierte, por lo que ninguna otra prueba verá ninguno de los cambios.

El método auxiliar para crear un contexto (consulte el código de accesorio anterior) acepta esta transacción y opta por que el DbContext la use. 

## <a name="sharing-the-fixture"></a>Uso compartido del accesorio

Es posible que haya observado el bloqueo del código en torno a la creación y propagación de bases de datos.
Esto no es necesario para este ejemplo, ya que solo una clase de pruebas usa el accesorio, por lo que solo se crea una instancia de accesorio.

Sin embargo, puede que desee usar el mismo accesorio con varias clases de pruebas.
XUnit creará una instancia de accesorio para cada una de estas clases.
Estos pueden ser usados por diferentes subprocesos que ejecutan pruebas en paralelo.
Por lo tanto, es importante tener un bloqueo adecuado para asegurarse de que solo un subproceso realiza la creación y propagación de la base de datos.

> [!TIP]
> Aquí está `lock` bien un sencillo.
> No es necesario intentar nada más complejo, como los patrones sin bloqueos.
