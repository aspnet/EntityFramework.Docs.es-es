---
title: 'Migraciones de Code First en entornos de equipo: EF6'
description: Migraciones de Code First en entornos de equipo en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/teams
ms.openlocfilehash: c3f12788f2aba85f54dc062bdb6a7919be47b56d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072231"
---
# <a name="code-first-migrations-in-team-environments"></a>Code First Migrations in Team Environments (Migraciones de Code First en entornos de equipo)
> [!NOTE]
> En este artículo se supone que sabe cómo usar Migraciones de Code First en escenarios básicos. Si no lo hace, tendrá que leer [migraciones de Code First](xref:ef6/modeling/code-first/migrations/index) antes de continuar.

## <a name="grab-a-coffee-you-need-to-read-this-whole-article"></a>Tome un café, debe leer todo el artículo

Los problemas en los entornos de equipo están principalmente en torno a la combinación de migraciones cuando dos desarrolladores han generado migraciones en su base de código local. Aunque los pasos para solucionar estos problemas son bastante sencillos, es necesario tener un conocimiento sólido de cómo funcionan las migraciones. No avance hasta el final; Tómese el tiempo necesario para leer todo el artículo y asegurarse de que se ha realizado correctamente.

## <a name="some-general-guidelines"></a>Algunas directrices generales

Antes de profundizar en cómo administrar las migraciones de combinación generadas por varios desarrolladores, estas son algunas directrices generales para configurar el éxito.

### <a name="each-team-member-should-have-a-local-development-database"></a>Cada miembro del equipo debe tener una base de datos de desarrollo local

Las migraciones usan la tabla ** \_ \_ MigrationsHistory** para almacenar las migraciones que se han aplicado a la base de datos. Si tiene varios desarrolladores que generan diferentes migraciones al intentar tener como destino la misma base de datos (y, por tanto, compartir una tabla ** \_ \_ MigrationsHistory** ), las migraciones se van a confundir.

Por supuesto, si tiene miembros del equipo que no están generando migraciones, no hay problemas para que compartan una base de datos de desarrollo central.

### <a name="avoid-automatic-migrations"></a>Evitar migraciones automáticas

La línea inferior es que las migraciones automáticas tienen un aspecto correcto en los entornos de equipo, pero en realidad no funcionan. Si desea saber por qué, siga leyendo; si no es así, puede ir directamente a la sección siguiente.

Migraciones automáticas permite actualizar el esquema de la base de datos para que coincida con el modelo actual sin necesidad de generar archivos de código (migraciones basadas en código). Las migraciones automáticas funcionarían bien en un entorno de equipo si solo se usaron y nunca se generaron migraciones basadas en código. El problema es que las migraciones automáticas están limitadas y no controlan varias operaciones: cambio de nombre de propiedades o columnas, movimiento de datos a otra tabla, etc. Para controlar estos escenarios, acaba de generar migraciones basadas en código (y editar el código con scaffolding) que se mezclan entre los cambios administrados por migraciones automáticas. Esto hace que resulte casi imposible combinar los cambios cuando dos desarrolladores protegen las migraciones.

## <a name="screencasts"></a>Presentaciones en pantalla

Si prefiere ver una screencast que lea este artículo, los dos vídeos siguientes cubren el mismo contenido que este artículo.

### <a name="video-one-migrations---under-the-hood"></a>Vídeo uno: "migraciones-bajo el capó"

En [esta](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) presentación en pantalla se explica cómo realiza la migración y usa información sobre el modelo para detectar los cambios de modelo.

### <a name="video-two-migrations---team-environments"></a>Vídeo dos: "migraciones: entornos de equipo"

Basándose en los conceptos del vídeo anterior, [esta](https://channel9.msdn.com/blogs/ef/migrations-team-environments) presentación en pantalla trata los problemas que surgen en un entorno de equipo y cómo resolverlos.

## <a name="understanding-how-migrations-works"></a>Descripción de cómo funcionan las migraciones

La clave para usar correctamente las migraciones en un entorno de equipo es una descripción básica de cómo realiza la migración y usa información sobre el modelo para detectar los cambios de modelo.

### <a name="the-first-migration"></a>La primera migración

Al agregar la primera migración al proyecto, ejecute algo como **Add-Migration First** en la consola del administrador de paquetes. Los pasos de alto nivel que realiza este comando se muestran a continuación.

![Primera migración](~/ef6/media/firstmigration.png)

El modelo actual se calcula a partir del código (1). Los objetos de base de datos necesarios se calculan a continuación por el modelo difiere (2): dado que se trata de la primera migración, el modelo solo se usa un modelo vacío para la comparación. Los cambios necesarios se pasan al generador de código para compilar el código de migración necesario (3) que después se agrega a la solución de Visual Studio (4).

Además del código de migración real que se almacena en el archivo de código principal, las migraciones también generan algunos archivos de código subyacente adicionales. Estos archivos son metadatos que se usan en las migraciones y no son algo que deba editar. Uno de estos archivos es un archivo de recursos (. resx) que contiene una instantánea del modelo en el momento en que se generó la migración. Verá cómo se usa en el paso siguiente.

En este momento, probablemente ejecute **Update-Database** para aplicar los cambios a la base de datos y, a continuación, vaya a la implementación de otras áreas de la aplicación.

### <a name="subsequent-migrations"></a>Migraciones posteriores

Más adelante, volverá a realizar algunos cambios en el modelo. en nuestro ejemplo, agregaremos una propiedad de **dirección URL** al **blog**. A continuación, debe emitir un comando como **Add-Migration AddUrl** para aplicar scaffolding a una migración a fin de aplicar los cambios de base de datos correspondientes. Los pasos de alto nivel que realiza este comando se muestran a continuación.

![Segunda migración](~/ef6/media/secondmigration.png)

Al igual que la última vez, el modelo actual se calcula a partir del código (1). Sin embargo, esta vez hay migraciones existentes, por lo que el modelo anterior se recupera de la última migración (2). Estos dos modelos se diferencian para encontrar los cambios de base de datos necesarios (3) y, a continuación, el proceso se completa como antes.

Este mismo proceso se usa para las migraciones posteriores que agregue al proyecto.

### <a name="why-bother-with-the-model-snapshot"></a>¿Por qué molestarse con la instantánea del modelo?

Es posible que se pregunte por qué EF ambos con la instantánea del modelo, por qué no solo mirar en la base de datos. En caso afirmativo, siga leyendo. Si no está interesado, puede omitir esta sección.

Hay varias razones por las que EF mantiene la instantánea del modelo:

-   Permite que la base de datos se desvíe del modelo EF. Estos cambios se pueden realizar directamente en la base de datos o puede cambiar el código con scaffolding en las migraciones para realizar los cambios. Estos son algunos ejemplos de esto en la práctica:
    -   Desea agregar una columna Inserted y Updated to a una o varias tablas, pero no desea incluir estas columnas en el modelo EF. Si las migraciones examinaban la base de datos, intentaría quitar continuamente estas columnas cada vez que se aplicara una migración scaffolding. Con la instantánea del modelo, EF solo detectará los cambios legítimos en el modelo.
    -   Desea cambiar el cuerpo de un procedimiento almacenado usado para que las actualizaciones incluyan algún registro. Si las migraciones examinaban este procedimiento almacenado desde la base de datos, intentaría volver a restablecerla continuamente a la definición que EF espera. Mediante el uso de la instantánea del modelo, EF solo creará código scaffolding para modificar el procedimiento almacenado cuando cambie la forma del procedimiento en el modelo EF.
    -   Estos mismos principios se aplican a la adición de índices adicionales, incluidas tablas adicionales en la base de datos, la asignación de EF a una vista de base de datos que se encuentra sobre una tabla, etc.
-   El modelo EF contiene algo más que la forma de la base de datos. Tener el modelo completo permite a las migraciones ver información sobre las propiedades y las clases del modelo y cómo se asignan a las columnas y tablas. Esta información permite que las migraciones sean más inteligentes en el código que scaffolding. Por ejemplo, si cambia el nombre de la columna que una propiedad se asigna a las migraciones puede detectar el cambio de nombre observando que es la misma propiedad, algo que no se puede hacer si solo tiene el esquema de la base de datos. 

## <a name="what-causes-issues-in-team-environments"></a>Qué provoca problemas en los entornos de equipo

El flujo de trabajo que se trata en la sección anterior funciona bien cuando es un desarrollador único que trabaja en una aplicación. También funciona bien en un entorno de equipo si es la única persona que realiza cambios en el modelo. En este escenario puede realizar cambios en el modelo, generar migraciones y enviarlos al control de código fuente. Otros desarrolladores pueden sincronizar los cambios y ejecutar **Update-Database** para aplicar los cambios de esquema.

Los problemas empiezan a surgir cuando tiene varios desarrolladores que realizan cambios en el modelo de EF y envían al control de código fuente al mismo tiempo. Lo que es EF falta es una primera forma de combinar las migraciones locales con las migraciones que otro desarrollador ha enviado al control de código fuente desde que se sincronizó por última vez.

## <a name="an-example-of-a-merge-conflict"></a>Ejemplo de un conflicto de combinación

En primer lugar, echemos un vistazo a un ejemplo concreto de este tipo de conflicto de combinación. Continuaremos con el ejemplo que hemos examinado anteriormente. Como punto de partida, supongamos que el desarrollador original protegió los cambios de la sección anterior. Haremos un seguimiento de dos desarrolladores a medida que realizan cambios en el código base.

Seguiremos el modelo EF y las migraciones a través de una serie de cambios. Para un punto de partida, ambos desarrolladores se han sincronizado con el repositorio de control de código fuente, tal y como se muestra en el gráfico siguiente.

![Punto de partida](~/ef6/media/startingpoint.png)

Developer \# 1 y Developer \# 2 ahora realizan algunos cambios en el modelo de EF en su base de código local. Developer \# 1 agrega una propiedad **rating** al **blog** y genera una migración **AddRating** para aplicar los cambios a la base de datos. Developer \# 2 agrega una propiedad **Readers** al **blog** y genera la migración de **AddReaders** correspondiente. Ambos desarrolladores ejecutan **Update-Database**para aplicar los cambios a sus bases de datos locales y, a continuación, seguir desarrollando la aplicación.

> [!NOTE]
> Las migraciones llevan el prefijo de una marca de tiempo, por lo que nuestro gráfico representa que la migración de AddReaders de Developer \# 2 viene después de la migración de AddRating de Developer \# 1. Si \# el desarrollador 1 o \# 2 generó la migración en primer lugar no realiza ninguna diferencia con los problemas de trabajo en un equipo o el proceso para combinarlos en la sección siguiente.

![Cambios locales](~/ef6/media/localchanges.png)

Es el día de la suerte para desarrolladores \# 1 a medida que se producen para enviar sus cambios en primer lugar. Dado que no se ha protegido ningún otro usuario desde que se sincronizó su repositorio, solo pueden enviar sus cambios sin realizar ninguna combinación.

![Enviar cambios](~/ef6/media/submit.png)

Ahora es el momento de enviar el desarrollador \# 2. No son tan afortunados. Dado que otra persona ha enviado cambios desde que se sincronizaron, deberán desplegar los cambios y mezclar. Es posible que el sistema de control de código fuente pueda fusionar mediante combinación automáticamente los cambios en el nivel de código, ya que son muy sencillos. El estado del \# repositorio local del desarrollador 2 después de la sincronización se muestra en el gráfico siguiente. 

![Extraer del control de código fuente](~/ef6/media/pull.png)

En esta fase \# , Developer 2 puede ejecutar **Update-Database** , que detectará la nueva migración de **AddRating** (que no se ha aplicado a la \# base de datos del desarrollador 2) y la aplicará. Ahora se agrega la columna **clasificación** a la tabla **blogs** y la base de datos está sincronizada con el modelo.

Sin embargo, hay un par de problemas:

1.  Aunque **Update-Database** aplicará la migración de **AddRating** también generará una advertencia: *no se puede actualizar la base de datos para que coincida con el modelo actual porque hay cambios pendientes y la migración automática está deshabilitada...*
    El problema es que la instantánea del modelo almacenada en la última migración (**AddReader**) no tiene la propiedad **rating** en el **blog** (puesto que no formaba parte del modelo cuando se generó la migración). Code First detecta que el modelo de la última migración no coincide con el modelo actual y genera la advertencia.
2.  Ejecutar la aplicación daría como resultado una excepción InvalidOperationException que indica que "*el modelo de respaldo del contexto ' BloggingContext ' ha cambiado desde que se creó la base de datos. Considere la posibilidad de usar Migraciones de Code First para actualizar la base de datos... "*
    De nuevo, el problema es que la instantánea del modelo almacenada en la última migración no coincide con el modelo actual.
3.  Por último, esperamos que la ejecución **de Add-Migration** genere una migración vacía (ya que no hay ningún cambio que se aplique a la base de datos). Sin embargo, dado que las migraciones comparan el modelo actual con el de la última migración (que no tiene la propiedad **rating** ), en realidad scaffolding otra llamada **AddColumn** para agregarla a la columna de **clasificación** . Por supuesto, esta migración produciría un error durante la **actualización de la base de datos** porque la columna de **clasificación** ya existe.

## <a name="resolving-the-merge-conflict"></a>Resolver el conflicto de fusión mediante combinación

La buena noticia es que no es demasiado difícil tratar la combinación manualmente, siempre que tenga un conocimiento de cómo funcionan las migraciones. Por lo tanto, si ha omitido el paso anterior a esta sección... Lo sentimos, debe volver y leer el resto del artículo en primer lugar.

Hay dos opciones, lo más fácil es generar una migración en blanco que tenga el modelo actual correcto como una instantánea. La segunda opción es actualizar la instantánea de la última migración para que tenga la instantánea del modelo correcta. La segunda opción es un poco más difícil y no se puede usar en todos los escenarios, pero también es más limpia porque no implica agregar una migración adicional.

### <a name="option-1-add-a-blank-merge-migration"></a>Opción 1: agregar una migración de ' Merge ' en blanco

En esta opción, se genera una migración en blanco únicamente con el fin de asegurarse de que la migración más reciente tenga almacenada la instantánea del modelo correcta.

Esta opción se puede usar independientemente de quién haya generado la última migración. En el ejemplo en el que hemos estado \# el desarrollador 2 se está encargando de la combinación y que se han producido para generar la última migración. Pero se pueden usar los mismos pasos si \# el desarrollador 1 generó la última migración. Los pasos también se aplican si hay varias migraciones implicadas: hemos visto dos con el fin de simplificar el proceso.

El siguiente proceso se puede usar para este enfoque, a partir del momento en que se dé cuenta de que tiene cambios que deben sincronizarse desde el control de código fuente.

1.  Asegúrese de que los cambios de modelos pendientes en la base de código local se han escrito en una migración. Este paso garantiza que no se pierda ningún cambio legítimo cuando llegue el momento de generar la migración en blanco.
2.  Sincronizar con control de código fuente.
3.  Ejecute **Update-Database** para aplicar las nuevas migraciones que hayan protegido otros desarrolladores.
    **_Nota:_** *si no recibe ninguna advertencia del comando UPDATE-Database, no habrá nuevas migraciones de otros desarrolladores y no será necesario realizar ninguna combinación adicional.*
4.  Ejecute **Add-Migration &lt; picking \_ \_ name &gt; – IgnoreChanges** (por ejemplo, **Add-Migration Merge – IgnoreChanges**). Esto genera una migración con todos los metadatos (incluida una instantánea del modelo actual), pero omitirá todos los cambios que detecte al comparar el modelo actual con la instantánea en las últimas Migraciones (lo que significa que obtiene un método **en blanco y** **vertical** ).
5.  Ejecute **Update-Database** para volver a aplicar la migración más reciente con los metadatos actualizados.
6.  Continúe desarrollando o envíe el control de código fuente (después de ejecutar las pruebas unitarias del curso).

Este es el estado de la \# base de código local del desarrollador 2 después de usar este enfoque.

![Migración de mezcla](~/ef6/media/mergemigration.png)

### <a name="option-2-update-the-model-snapshot-in-the-last-migration"></a>Opción 2: actualizar la instantánea del modelo en la última migración

Esta opción es muy similar a la opción 1, pero quita la migración en blanco adicional, porque nos encargamos de ello, que quiere archivos de código adicionales en su solución.

**Este enfoque solo es factible si la migración más reciente solo existe en la base de código local y aún no se ha enviado al control de código fuente (por ejemplo, si la última migración fue generada por el usuario que realizó la mezcla)**. La edición de los metadatos de las migraciones que otros desarrolladores pueden haber aplicado ya a su base de datos de desarrollo, o incluso peor si se aplican a una base de datos de producción, puede producir efectos secundarios inesperados. Durante el proceso, vamos a revertir la última migración en nuestra base de datos local y volver a aplicarla con los metadatos actualizados.

Aunque la última migración debe estar solo en la base de código local, no hay ninguna restricción sobre el número o el orden de las migraciones que lo continúan. Puede haber varias migraciones de varios desarrolladores diferentes y se aplican los mismos pasos: simplemente hemos visto dos con el fin de mantenerlos sencillos.

El siguiente proceso se puede usar para este enfoque, a partir del momento en que se dé cuenta de que tiene cambios que deben sincronizarse desde el control de código fuente.

1.  Asegúrese de que los cambios de modelos pendientes en la base de código local se han escrito en una migración. Este paso garantiza que no se pierda ningún cambio legítimo cuando llegue el momento de generar la migración en blanco.
2.  Sincronizar con el control de código fuente.
3.  Ejecute **Update-Database** para aplicar las nuevas migraciones que hayan protegido otros desarrolladores.
    **_Nota:_** *si no recibe ninguna advertencia del comando UPDATE-Database, no habrá nuevas migraciones de otros desarrolladores y no será necesario realizar ninguna combinación adicional.*
4.  Ejecute **Update-Database – TargetMigration &lt; Second \_ Last \_ Migration &gt; ** (en el ejemplo que hemos estado siguiendo esto sería **Update-Database – TargetMigration AddRating**). De este modo, la base de datos vuelve al estado de la segunda migración, con lo que se "anula la aplicación" de la última migración de la base de datos.
    **_Nota:_** *este paso es necesario para que sea seguro editar los metadatos de la migración, ya que los metadatos también se almacenan en el \_ \_ MigrationsHistoryTable de la base de datos. Esta es la razón por la que solo debe usar esta opción si la última migración solo está en la base de código local. Si se ha aplicado la última migración a otras bases de datos, también tendrá que revertirla y volver a aplicar la última migración para actualizar los metadatos.* 
5.  Ejecute el ** &lt; nombre completo de Add-Migration \_ \_ , incluida la \_ marca de tiempo de la \_ \_ última \_ migración** &gt; (en el ejemplo que hemos estado siguiendo esto sería algo como **Add-Migration 201311062215252 \_ AddReaders**).
    **_Nota:_** debe *incluir la marca de tiempo para que las migraciones sepan que desea editar la migración existente en lugar de aplicar la técnica scaffolding a una nueva.*
    De esta forma, se actualizarán los metadatos de la última migración para que coincida con el modelo actual. Obtendrá la siguiente advertencia cuando se complete el comando, pero eso es exactamente lo que desea. "*Solo se volvió a scaffolding el código del diseñador para la migración ' 201311062215252 \_ AddReaders '. Para volver a aplicar el scaffolding a toda la migración, use el parámetro-Force ".*
6.  Ejecute **Update-Database** para volver a aplicar la migración más reciente con los metadatos actualizados.
7.  Continúe desarrollando o envíe el control de código fuente (después de ejecutar las pruebas unitarias del curso).

Este es el estado de la \# base de código local del desarrollador 2 después de usar este enfoque.

![Metadatos actualizados](~/ef6/media/updatedmetadata.png)

## <a name="summary"></a>Resumen

El uso de Migraciones de Code First en un entorno de equipo conlleva algunos desafíos. Sin embargo, una descripción básica de cómo funcionan las migraciones y algunos enfoques sencillos para resolver los conflictos de combinación facilitan la superación de estos desafíos.

El problema fundamental son los metadatos incorrectos almacenados en la última migración. Esto hace que Code First detecte incorrectamente que el modelo actual y el esquema de la base de datos no coinciden y que se debe aplicar scaffolding al código incorrecto en la siguiente migración. Esta situación se puede solucionar generando una migración en blanco con el modelo correcto o actualizando los metadatos en la última migración.
