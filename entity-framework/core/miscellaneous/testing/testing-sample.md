---
title: 'Ejemplo de prueba de EF Core: EF Core'
description: Ejemplo que muestra cómo probar las aplicaciones que usan Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: 07cde296f07a883ba6abf45f94a31e072fb6d6cb
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063054"
---
# <a name="ef-core-testing-sample"></a>Ejemplo de prueba de EF Core

> [!TIP]
> El código de este documento se puede encontrar en GitHub como un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).
> Tenga en cuenta que se espera que algunas de estas pruebas **produzcan un error**. Los motivos para ello se explican a continuación.

Este documento le guía a través de un ejemplo para probar el código que usa EF Core.

## <a name="the-application"></a>Aplicación

El [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contiene dos proyectos:

- ItemsWebApi: una [API Web muy sencilla respaldada por ASP.net Core](/aspnet/core/tutorials/first-web-api) con un solo controlador
- Pruebas: un proyecto de prueba de [xUnit](https://xunit.net/) para probar el controlador

### <a name="the-model-and-business-rules"></a>El modelo y las reglas de negocios

El modelo de respaldo de esta API tiene dos tipos Items de entidad: y Tags .

- Items tienen un nombre que distingue entre mayúsculas y minúsculas y una colección de Tags .
- Cada Tag una tiene una etiqueta y un recuento que representa el número de veces que se ha aplicado a Item .
- Cada Item una de ellas solo debe tener una Tag con una etiqueta determinada.
  - Si un elemento se etiqueta con la misma etiqueta más de una vez, se incrementa el recuento de la etiqueta existente con esa etiqueta en lugar de crear una nueva etiqueta.
- La eliminación de Item debe eliminar todos los asociados Tags .

#### <a name="the-no-locitem-entity-type"></a>El Item tipo de entidad

El `Item` tipo de entidad:

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

Y su configuración en `DbContext.OnModelCreating` :

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

Observe que el tipo de entidad restringe la manera en que se puede usar para reflejar el modelo de dominio y las reglas de negocios. En concreto:

- La clave principal se asigna directamente al `_id` campo y no se expone públicamente.
  - EF detecta y usa el constructor privado que acepta el valor y el nombre de la clave principal.
- La `Name` propiedad es de solo lectura y se establece solo en el constructor.
- Tags se exponen como `IReadOnlyList<Tag>` para evitar modificaciones arbitrarias.
  - EF asocia el `Tags` nombre de la propiedad con el `_tags` campo de respaldo.
  - El `AddTag` método toma una etiqueta de etiqueta e implementa la regla de negocios que se ha descrito anteriormente.
    Es decir, solo se agrega una etiqueta para las etiquetas nuevas.
    En caso contrario, se incrementa el recuento de una etiqueta existente.
- La `Tags` propiedad de navegación se configura para una relación de varios a uno
  - No es necesario que una propiedad de navegación de Tag a Item , por lo que no se incluye.
  - Además, no Tag define una propiedad de clave externa.
    En su lugar, EF creará y administrará una propiedad en el estado de sombra.

#### <a name="the-no-loctag-entity-type"></a>El Tag tipo de entidad

El `Tag` tipo de entidad:

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

Y su configuración en `DbContext.OnModelCreating` :

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

De forma similar a Item , Tag oculta su clave principal y hace que la `Label` propiedad sea de solo lectura.

### <a name="the-no-locitemscontroller"></a>El Items controlador

El controlador de API Web es bastante básico.
Obtiene un `DbContext` del contenedor de inserción de dependencias a través de la inserción de constructores:

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

Tiene métodos para obtener todos Items o un Item con un nombre determinado:

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

Tiene un método para agregar un nuevo Item :

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

Un método para etiquetar un Item con una etiqueta:

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

Y un método para eliminar un Item y todos los asociados Tags :

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

La mayoría de la validación y el control de errores se han quitado para reducir la confusión.

## <a name="the-tests"></a>Las pruebas

Las pruebas se organizan para ejecutarse con varias configuraciones de proveedor de base de datos:

- Proveedor de SQL Server, que es el proveedor que usa la aplicación.
- Proveedor de SQLite
- Proveedor de SQLite que usa bases de datos de SQLite en memoria
- El proveedor de base de datos de EF en memoria

Esto se logra colocando todas las pruebas en una clase base y, a continuación, heredando de esta para probar con cada proveedor.

> [!TIP]
> Tendrá que cambiar el SQL Server cadena de conexión si no está usando LocalDB.
> Consulte [pruebas con SQLite](xref:core/miscellaneous/testing/sqlite) para obtener instrucciones sobre el uso de SQLite para realizar pruebas en memoria.

Se espera que se produzcan errores en las dos pruebas siguientes:

- `Can_remove_item_and_all_associated_tags` al ejecutar con el proveedor de base de datos de EF en memoria
- `Can_add_item_differing_only_by_case` al ejecutar con el proveedor de SQL Server

Esto se describe con más detalle a continuación.

### <a name="setting-up-and-seeding-the-database"></a>Configurar y propagar la base de datos

XUnit, al igual que la mayoría de los marcos de pruebas, creará una nueva instancia de clase de prueba para cada serie de pruebas.
Además, XUnit no ejecutará en paralelo las pruebas de una clase de prueba determinada.
Esto significa que se puede instalar y configurar la base de datos en el constructor de prueba y que estará en un estado conocido para cada prueba.

> [!TIP]
> En este ejemplo se vuelve a crear la base de datos para cada prueba.
> Esto funciona bien para las pruebas de base de datos en memoria de SQLite y EF, pero puede suponer una sobrecarga significativa con otros sistemas de base de datos, como SQL Server.
> Los enfoques para reducir esta sobrecarga se abordan en el [uso compartido de bases de datos entre pruebas](xref:core/miscellaneous/testing/sharing-databases).

Cuando se ejecuta cada prueba:

- DbContextOptions se configuran para el proveedor en uso y se pasan al constructor de clase base
  - Estas opciones se almacenan en una propiedad y se usan en las pruebas para la creación de instancias de DbContext.
- Se llama a un método de inicialización para crear e inicializar la base de datos
  - El método de inicialización garantiza que la base de datos está limpia al eliminarla y volver a crearla.
  - Algunas entidades de prueba conocidas se crean y se guardan en la base de datos.

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

Cada clase de prueba concreta hereda de este.
Por ejemplo:

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>Estructura de prueba

Aunque la aplicación usa la inserción de dependencias, las pruebas no lo hacen.
Sería adecuado usar la inserción de dependencias aquí, pero el código adicional que requiere tiene poco valor.
En su lugar, se crea un DbContext usando `new` y, a continuación, se pasa directamente como la dependencia al controlador.

Después, cada prueba ejecuta el método sometido a prueba en el controlador y valida que los resultados son los esperados.
Por ejemplo:

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

Observe que se usan diferentes instancias de DbContext para inicializar la base de datos y ejecutar las pruebas.
Esto garantiza que la prueba no esté usando (o pasando por) entidades cuyo seguimiento realiza el contexto al realizar la propagación.
También mejor coincide con lo que ocurre en servicios y aplicaciones Web.

Las pruebas que mutan la base de datos crean una segunda instancia de DbContext en la prueba por motivos similares.
Es decir, crear un nuevo contexto, limpiar y, a continuación, leerlo desde la base de datos para asegurarse de que los cambios se guardaron en la base de datos.
Por ejemplo:

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

Dos pruebas ligeramente más complicadas cubren la lógica de negocios en torno a la adición tags .

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>Problemas con diferentes proveedores de bases de datos

La prueba con un sistema de base de datos diferente al que se usa en la aplicación de producción puede provocar problemas.
Estos se describen en el nivel conceptual del [código de prueba que usa EF Core](xref:core/miscellaneous/testing/index).
En las secciones siguientes se incluyen dos ejemplos de estos problemas que se muestran en las pruebas de este ejemplo.

### <a name="test-passes-when-the-application-is-broken"></a>La prueba se supera cuando se interrumpe la aplicación

Uno de los requisitos de nuestra aplicación es que " Items tiene un nombre que distingue entre mayúsculas y minúsculas y una colección de Tags ".
Esto es bastante sencillo de probar:

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

La ejecución de esta prueba en la base de datos en memoria de EF indica que todo está bien.
Todo sigue teniendo el aspecto correcto al usar SQLite.
Pero se produce un error en la prueba cuando se ejecuta en SQL Server.

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

Esto se debe a que la base de datos de EF en memoria y la base de datos de SQLite distinguen mayúsculas de minúsculas de forma predeterminada.
SQL Server, por otro lado, no distingue entre mayúsculas y minúsculas.

EF Core, por diseño, no cambia estos comportamientos porque forzar un cambio en la distinción de mayúsculas y minúsculas puede tener un gran impacto en el rendimiento.

Una vez que sabemos que se trata de un problema, podemos corregir la aplicación y compensar las pruebas.
Sin embargo, el punto aquí es que este error podría perderse si solo se prueba con la base de datos de EF en memoria o con proveedores de SQLite.

### <a name="test-fails-when-the-application-is-correct"></a>Se produce un error en la prueba cuando la aplicación es correcta

Otro de los requisitos para nuestra aplicación es que "la eliminación de un Item debe eliminar todos los asociados Tags ".
De nuevo, fácil de probar:

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

Esta prueba se supera en SQL Server y SQLite, pero produce un error con la base de datos de EF en memoria.

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

En este caso, la aplicación funciona correctamente porque SQL Server admite [eliminaciones en cascada](xref:core/saving/cascade-delete).
SQLite también admite eliminaciones en cascada, al igual que la mayoría de las bases de datos relacionales, por lo que la prueba en SQLite funciona.
Por otro lado, la base de datos en memoria de EF [no admite eliminaciones en cascada](https://github.com/dotnet/efcore/issues/3924).
Esto significa que esta parte de la aplicación no se puede probar con el proveedor de base de datos de EF en memoria.
