---
title: 'Migraciones de Code First con una base de datos existente: EF6'
description: Migraciones de Code First con una base de datos existente en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/existing-database
ms.openlocfilehash: 5dd38f3fb9a59805a9b23b7da5e770995866f715
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072114"
---
# <a name="code-first-migrations-with-an-existing-database"></a>Migraciones de Code First con una base de datos existente
> [!NOTE]
> **EF 4.3** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 4,1. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

En este artículo se describe el uso de Migraciones de Code First con una base de datos existente, una que no se ha creado con Entity Framework.

> [!NOTE]
> En este artículo se supone que sabe cómo usar Migraciones de Code First en escenarios básicos. Si no lo hace, tendrá que leer [migraciones de Code First](xref:ef6/modeling/code-first/migrations/index) antes de continuar.

## <a name="screencasts"></a>Presentaciones en pantalla

Si prefiere ver una screencast que lea este artículo, los dos vídeos siguientes cubren el mismo contenido que este artículo.

### <a name="video-one-migrations---under-the-hood"></a>Vídeo uno: "migraciones-bajo el capó"

En [esta](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) presentación en pantalla se explica cómo realiza la migración y usa información sobre el modelo para detectar los cambios de modelo.

### <a name="video-two-migrations---existing-databases"></a>Vídeo dos: "migraciones: bases de datos existentes"

Basándose en los conceptos del vídeo anterior, [esta](https://channel9.msdn.com/blogs/ef/migrations-existing-databases) presentación en pantalla explica cómo habilitar y usar las migraciones con una base de datos existente.

## <a name="step-1-create-a-model"></a>Paso 1: creación de un modelo

El primer paso será crear un modelo de Code First que tenga como destino la base de datos existente. En el tema [code First a una base de datos existente se](xref:ef6/modeling/code-first/workflows/existing-database) proporcionan instrucciones detalladas sobre cómo hacerlo.

>[!NOTE]
> Es importante seguir el resto de los pasos de este tema antes de realizar cambios en el modelo que requieran cambios en el esquema de la base de datos. Los pasos siguientes requieren que el modelo esté sincronizado con el esquema de la base de datos.

## <a name="step-2-enable-migrations"></a>Paso 2: habilitar las migraciones

El siguiente paso consiste en habilitar las migraciones. Para ello, ejecute el comando **enable-Migrations** en la consola del administrador de paquetes.

Este comando creará una carpeta en la solución denominada migraciones y colocará una sola clase en ella llamada configuración. La clase de configuración es donde se configuran las migraciones de la aplicación, puede encontrar más información en el tema [migraciones de Code First](xref:ef6/modeling/code-first/migrations/index) .

## <a name="step-3-add-an-initial-migration"></a>Paso 3: agregar una migración inicial

Una vez que se han creado y aplicado las migraciones en la base de datos local, es posible que también desee aplicar estos cambios a otras bases de datos. Por ejemplo, la base de datos local puede ser una base de datos de prueba y, en última instancia, querrá aplicar también los cambios a una base de datos de producción o a otros desarrolladores de prueba. Hay dos opciones para este paso y la que debe elegir depende de si el esquema de otras bases de datos está vacío o no coincide actualmente con el esquema de la base de datos local.

-   **Opción uno: usar el esquema existente como punto de partida.** Debe usar este enfoque si las demás bases de datos que se van a aplicar a las migraciones en el futuro tendrán el mismo esquema que la base de datos local que tiene actualmente. Por ejemplo, podría utilizarlo si la base de datos de prueba local coincide con la versión 1 de la base de datos de producción y, posteriormente, aplicará estas migraciones para actualizar la base de datos de producción a V2.
-   **Opción dos: usar una base de datos vacía como punto de partida.** Debe usar este enfoque cuando las demás bases de datos que se van a aplicar a las migraciones en el futuro estén vacías (o aún no existan). Por ejemplo, podría utilizarlo si comenzó a desarrollar la aplicación con una base de datos de prueba pero sin usar migraciones y posteriormente deseará crear una base de datos de producción desde cero.

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>Opción uno: usar el esquema existente como punto de partida

Migraciones de Code First usa una instantánea del modelo almacenada en la migración más reciente para detectar los cambios en el modelo (puede encontrar información detallada sobre esto en [migraciones de Code First en entornos de equipo](xref:ef6/modeling/code-first/migrations/teams)). Como vamos a suponer que las bases de datos ya tienen el esquema del modelo actual, generaremos una migración vacía (no operativa) que tenga el modelo actual como una instantánea.

1.  Ejecute el comando **Add-Migration InitialCreate – IgnoreChanges** en la consola del administrador de paquetes. Esto crea una migración vacía con el modelo actual como una instantánea.
2.  Ejecute el comando **Update-Database** en la consola del administrador de paquetes. Esto aplicará la migración de InitialCreate a la base de datos. Dado que la migración real no contiene ningún cambio, simplemente agregará una fila a la \_ \_ tabla MigrationsHistory que indica que ya se ha aplicado esta migración.

### <a name="option-two-use-empty-database-as-a-starting-point"></a>Opción dos: usar una base de datos vacía como punto de partida

En este escenario, se necesitan migraciones para poder crear la base de datos completa desde cero, incluidas las tablas que ya están presentes en nuestra base de datos local. Vamos a generar una migración de InitialCreate que incluye la lógica para crear el esquema existente. A continuación, haremos que nuestra base de datos existente tenga el mismo aspecto que ya se ha aplicado esta migración.

1.  Ejecute el comando **Add-Migration InitialCreate** en la consola del administrador de paquetes. Esto crea una migración para crear el esquema existente.
2.  Comente todo el código del método up de la migración recién creada. Esto nos permitirá "aplicar" la migración a la base de datos local sin intentar volver a crear todas las tablas, etc., que ya existen.
3.  Ejecute el comando **Update-Database** en la consola del administrador de paquetes. Esto aplicará la migración de InitialCreate a la base de datos. Dado que la migración real no contiene ningún cambio (dado que hemos comentado temporalmente), simplemente agregará una fila a la \_ \_ tabla MigrationsHistory que indica que ya se ha aplicado esta migración.
4.  Quite la marca de comentario del código en el método up. Esto significa que, cuando esta migración se aplica a bases de datos futuras, el esquema que ya existía en la base de datos local lo crearán las migraciones.

## <a name="things-to-be-aware-of"></a>Aspectos que debe tener en cuenta

Hay algunas cosas que debe tener en cuenta al usar migraciones en una base de datos existente.

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>Los nombres predeterminados o calculados pueden no coincidir con el esquema existente

Las migraciones especifican explícitamente los nombres de las columnas y las tablas cuando se scaffolding una migración. Sin embargo, hay otros objetos de base de datos que las migraciones calculan un nombre predeterminado para cuando se aplican las migraciones. Esto incluye los índices y las restricciones Foreign Key. Cuando el destino es un esquema existente, es posible que estos nombres calculados no coincidan con lo que existe realmente en la base de datos.

Estos son algunos ejemplos de Cuándo es necesario tener en cuenta lo siguiente:

**Si usó ' opción uno: usar el esquema existente como punto de partida ' del paso 3:**

-   Si los cambios futuros en el modelo requieren el cambio o la eliminación de uno de los objetos de base de datos que se denominan de forma diferente, tendrá que modificar la migración con scaffolding para especificar el nombre correcto. Las API de migraciones tienen un parámetro de nombre opcional que le permite hacerlo.
    Por ejemplo, el esquema existente puede tener una tabla post con una columna de clave externa BlogId que tenga un índice denominado IndexFk \_ BlogId. Sin embargo, de forma predeterminada, las migraciones esperan que este índice se denomine IX \_ BlogId. Si realiza un cambio en el modelo que tiene como resultado la eliminación de este índice, tendrá que modificar la llamada a scaffolding DropIndex para especificar el nombre de la BlogId de IndexFk \_ .

**Si ha usado la opción dos: usar una base de datos vacía como punto de partida en el paso 3:**

-   Puede producirse un error al intentar ejecutar el método Down de la migración inicial (es decir, revertir a una base de datos vacía) en la base de datos local, ya que las migraciones intentarán quitar índices y restricciones de clave externa usando los nombres incorrectos. Esto solo afectará a la base de datos local, ya que las demás bases de datos se crearán desde cero con el método up de la migración inicial.
    Si desea cambiar la base de datos local existente a un estado vacío, es más fácil hacerlo manualmente, ya sea quitando la base de datos o quitando todas las tablas. Después de esta degradación inicial, todos los objetos de base de datos se volverán a crear con los nombres predeterminados, por lo que este problema no se volverá a presentar.
-   Si los cambios futuros en el modelo requieren el cambio o la eliminación de uno de los objetos de base de datos con el nombre diferente, no funcionará en la base de datos local existente, ya que los nombres no coinciden con los valores predeterminados. Sin embargo, funcionará en las bases de datos que se crearon desde cero, ya que se usarán los nombres predeterminados elegidos por las migraciones.
    Puede realizar estos cambios de forma manual en la base de datos existente o considerar la posibilidad de hacer que las migraciones vuelvan a crear la base de datos desde cero, como se hará en otras máquinas.
-   Las bases de datos creadas con el método up de la migración inicial pueden diferir ligeramente de la base de datos local, ya que se usarán los nombres predeterminados calculados para los índices y las restricciones Foreign Key. También puede acabar con índices adicionales ya que las migraciones crearán índices en columnas de clave externa de forma predeterminada; esto puede no haber sido el caso en la base de datos local original.

### <a name="not-all-database-objects-are-represented-in-the-model"></a>No todos los objetos de base de datos se representan en el modelo

Los objetos de base de datos que no forman parte de su modelo no serán controlados por las migraciones. Esto puede incluir vistas, procedimientos almacenados, permisos, tablas que no forman parte del modelo, índices adicionales, etc.

Estos son algunos ejemplos de Cuándo es necesario tener en cuenta lo siguiente:

-   Independientemente de la opción elegida en el ' paso 3 ', si los cambios futuros en el modelo requieren el cambio o la eliminación de estas migraciones de objetos adicionales, no sabrá realizar estos cambios. Por ejemplo, si quita una columna que tiene un índice adicional en ella, las migraciones no sabrán quitar el índice. Tendrá que agregarlo manualmente a la migración con scaffolding.
-   Si ha usado la opción dos: usar una base de datos vacía como punto de partida, estos objetos adicionales no se crearán con el método up de la migración inicial.
    Puede modificar los métodos arriba y abajo para que se ocupen de estos objetos adicionales si lo desea. En el caso de los objetos que no se admiten de forma nativa en la API de migraciones, como las vistas, puede usar el método [SQL](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx) para ejecutar SQL sin formato para crearlos y quitarlos.
