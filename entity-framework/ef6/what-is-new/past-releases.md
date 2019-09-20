---
title: Versiones anteriores de Entity Framework-EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 1060bb99-765f-4f32-aaeb-d6635d3dbd3e
ms.openlocfilehash: 3ee433ac0932f89841b5cc42fb864eefb9419ef2
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149282"
---
# <a name="past-releases-of-entity-framework"></a>Versiones anteriores de Entity Framework

La primera versión de Entity Framework se lanzó en 2008, como parte de .NET Framework 3,5 SP1 y Visual Studio 2008 SP1.

A partir de la versión EF 4.1, se ha distribuido como el [paquete NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/) , actualmente uno de los paquetes más populares en NuGet.org.

Entre las versiones 4,1 y 5,0, el paquete NuGet EntityFramework amplió las bibliotecas de EF que se incluían como parte de .NET Framework.

A partir de la versión 6, EF se convirtió en un proyecto de código abierto y también se movía completamente fuera de banda en el .NET Framework.
Esto significa que cuando se agrega el paquete NuGet de la versión 6 de EntityFramework a una aplicación, se obtiene una copia completa de la biblioteca EF que no depende de los bits EF que se incluyen como parte de .NET Framework.
Esto ayudó en cierto modo a acelerar el ritmo del desarrollo y la entrega de nuevas características.

En junio de 2016, se publicó EF Core 1,0. EF Core se basa en un nuevo código base y está diseñado como una versión más ligera y extensible de EF.
Actualmente EF Core es el principal enfoque del desarrollo para el equipo de Entity Framework en Microsoft.
Esto significa que no hay nuevas características principales planeadas para EF6. Sin embargo, EF6 se sigue manteniendo como un proyecto de código abierto y un producto de Microsoft compatible.

Esta es la lista de versiones anteriores, en orden cronológico inverso, con información sobre las nuevas características que se introdujeron en cada versión.

## <a name="ef-tools-update-in-visual-studio-2017-157"></a>Actualización de EF Tools para Visual Studio 2017 15.7
En mayo de 2018 se publicó una versión actualizada de EF6 Tools como parte de Visual Studio 2017 15.7.
Incluye mejoras para algunas de las áreas problemáticas más habituales:

- Correcciones de varios errores de accesibilidad de la interfaz de usuario
- Solución alternativa para la regresión del rendimiento de SQL Server al generar modelos a partir de bases de datos existentes [#4](https://github.com/aspnet/entityframework6/issues/4)
- Compatibilidad para la actualización de modelos más grandes en SQL Server [#185](https://github.com/aspnet/EntityFramework6/issues/185)

Otra mejora de esta versión nueva de EF Tools es que ahora instala el runtime de EF 6.2 al crear un modelo en un proyecto nuevo. Con versiones anteriores de Visual Studio, es posible usar el runtime de EF 6.2 (así como cualquier versión anterior de EF) mediante la instalación de la versión correspondiente del paquete NuGet.

## <a name="ef-620"></a>EF 6.2.0
El runtime de EF 6.2 para NuGet se publicó en octubre de 2017.
Gracias en gran medida a los esfuerzos de la comunidad de colaboradores de código abierto, EF 6.2 incluye bastantes [correcciones de errores](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug) y [mejoras de producto](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20).

Esta es una breve lista de los cambios más importantes que afectan al runtime de EF 6.2:

- Reducción del tiempo de inicio al cargar los primeros modelos de código finalizados desde una caché persistente [#275](https://github.com/aspnet/EntityFramework6/issues/275)
- API fluida para definir índices [#274](https://github.com/aspnet/EntityFramework6/issues/274)
- DbFunctions.Like() para habilitar la escritura de consultas LINQ que se traducen en LIKE en SQL [#241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe ahora admite la opción -script [#240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 ahora puede trabajar con valores de clave generados por una secuencia en SQL Server [#165](https://github.com/aspnet/EntityFramework6/issues/165)
- Lista de actualización de errores transitorios de la estrategia de ejecución de SQL Azure [#83](https://github.com/aspnet/EntityFramework6/issues/83)
- Error: al volver a intentar consultas o comandos SQL, se produce un error "SqlParameter ya está incluido en otro elemento SqlParameterCollection" [#81](https://github.com/aspnet/EntityFramework6/issues/81)
- Error: la evaluación de DbQuery.ToString() a menudo agota el tiempo de espera en el depurador [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="ef-613"></a>EF 6.1.3
El tiempo de ejecución de EF 6.1.3 se lanzó a NuGet en octubre de 2015.
Esta versión solo contiene correcciones para los defectos de alta prioridad y las regresiones detectadas en la versión 6.1.2.
Las correcciones incluyen:

- Consulta: Regresión en EF 6.1.2: APLICACIÓN externa introducida y consultas más complejas para las relaciones 1:1 y la cláusula "Let"
- Problema de TPT al ocultar la propiedad de clase base en la clase heredada
- Error de DbMigration. SQL cuando la palabra ' Go ' está contenida en el texto
- Crear marca de compatibilidad para la compatibilidad con el acoplamiento de UnionAll y Intersect
- La consulta con varios includes no funciona en 6.1.2 (trabajando en 6.1.1)
- "Hay un error en la sintaxis de SQL" excepción después de actualizar de EF 6.1.1 a 6.1.2

## <a name="ef-612"></a>EF 6.1.2
El tiempo de ejecución de EF 6.1.2 se lanzó a NuGet en diciembre de 2014.
Esta versión está principalmente relacionada con las correcciones de errores. También hemos aceptado un par de cambios notables de los miembros de la comunidad:
- **Los parámetros de la caché de consulta se pueden configurar desde el archivo App/web. Configuration.**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **Los métodos SqlFile y SqlResource de DbMigration** permiten ejecutar un script SQL almacenado como un archivo o un recurso incrustado.

## <a name="ef-611"></a>EF 6.1.1
El tiempo de ejecución de EF 6.1.1 se lanzó a NuGet en junio de 2014.
Esta versión contiene correcciones para los problemas que ha encontrado un número de personas. Entre otras:
- Generador Error al abrir EF5 edmx con precisión decimal en el diseñador de EF6
- La lógica de detección de instancia predeterminada para LocalDB no funciona con SQL Server 2014

## <a name="ef-610"></a>EF 6.1.0
El tiempo de ejecución de EF 6.1.0 se lanzó a NuGet en marzo de 2014.
Esta actualización secundaria incluye un número significativo de características nuevas:

- La **consolidación de herramientas** proporciona una manera coherente de crear un nuevo modelo EF. Esta característica [amplía el Asistente para Entity Data Model de ADO.net para admitir la creación de modelos Code First](~/ef6/modeling/code-first/workflows/existing-database.md), incluida la ingeniería inversa de una base de datos existente. Estas características estaban previamente disponibles en calidad beta en las herramientas avanzadas de EF.
- El **[control de los errores de confirmación de la transacción](~/ef6/fundamentals/connection-resiliency/commit-failures.md)** proporciona la CommitFailureHandler, que hace uso de la nueva capacidad introducida para interceptar las operaciones de transacción. CommitFailureHandler permite la recuperación automática de errores de conexión mientras se confirma una transacción.
- **[IndexAttribute](~/ef6/modeling/code-first/data-annotations.md)** permite especificar los índices colocando un `[Index]` atributo en una propiedad (o propiedades) en el modelo de Code First. A continuación, Code First creará un índice correspondiente en la base de datos.
- **La API de asignación pública** proporciona acceso a la información EF tiene sobre cómo se asignan las propiedades y los tipos a las columnas y tablas de la base de datos. En las versiones anteriores, esta API era interna.
- **[La capacidad de configurar los interceptores mediante el archivo App/web. config](~/ef6/fundamentals/configuring/config-file.md)** permite agregar interceptores sin volver a compilar la aplicación.
- **System. Data. Entity. Infrastructure. intercepción. DatabaseLogger**es un nuevo interceptor que facilita el registro de todas las operaciones de base de datos en un archivo. En combinación con la característica anterior, esto le permite cambiar fácilmente el [registro de las operaciones de base de datos para una aplicación implementada](~/ef6/fundamentals/configuring/config-file.md), sin necesidad de volver a compilar.
- La **detección de cambios del modelo de migración** se ha mejorado para que las migraciones con scaffolding sean más precisas. también se ha mejorado el rendimiento del proceso de detección de cambios.
- **Mejoras de rendimiento** , como las operaciones de base de datos reducidas durante la inicialización, optimizaciones para la comparación de igualdad nula en consultas LINQ, generación más rápida de vistas (creación de modelos) en más escenarios y materialización más eficaz de entidades sometidas a seguimiento con varias asociaciones.

## <a name="ef-602"></a>EF 6.0.2
El tiempo de ejecución de EF 6.0.2 se lanzó a NuGet en diciembre de 2013.
Esta versión de revisión se limita a solucionar los problemas que se introdujeron en la versión EF6 (regresiones en rendimiento/comportamiento desde EF5).

## <a name="ef-601"></a>EF 6.0.1
El tiempo de ejecución de EF 6.0.1 se lanzó a NuGet en octubre de 2013 simultáneamente con EF 6.0.0, ya que este último se incrustó en una versión de Visual Studio que se había bloqueado unos meses antes.
Esta versión de revisión se limita a solucionar los problemas que se introdujeron en la versión EF6 (regresiones en rendimiento/comportamiento desde EF5).
Los cambios más importantes fueron resolver algunos problemas de rendimiento durante el calentamiento de los modelos EF.
Esto era importante porque el rendimiento de la preparación era un área de enfoque en EF6 y estos problemas eran la negación de algunas de las otras mejoras de rendimiento realizadas en EF6.

## <a name="ef-60"></a>EF 6,0
El tiempo de ejecución de EF 6.0.0 se lanzó a NuGet en octubre de 2013.
Esta es la primera versión en la que se incluye un tiempo de ejecución de EF completo en el [paquete NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/) que no depende de los bits EF que forman parte de la .NET Framework.
Mover las partes restantes del tiempo de ejecución al paquete de NuGet requirió un número de cambios importantes en el código existente.
Consulte la sección sobre la [actualización a Entity Framework 6](upgrading-to-ef6.md) para obtener más detalles sobre los pasos manuales necesarios para la actualización.

Esta versión incluye numerosas características nuevas.
Las siguientes características funcionan para los modelos creados con Code First o el diseñador de EF:

- **[Consulta asincrónica y guardar](~/ef6/fundamentals/async.md)** agrega compatibilidad con los patrones asincrónicos basados en tareas que se introdujeron en .net 4,5.
- La resistencia de la **[conexión](~/ef6/fundamentals/connection-resiliency/retry-logic.md)** permite la recuperación automática de errores de conexión transitorios.
- La **[configuración basada en código](~/ef6/fundamentals/configuring/code-based.md)** ofrece la opción de realizar la configuración, que tradicionalmente se llevó a cabo en un archivo de configuración, en el código.
- La **[resolución de dependencias](~/ef6/fundamentals/configuring/dependency-resolution.md)** presenta compatibilidad con el patrón de localizador de servicio y hemos factorizado algunas partes de la funcionalidad que se pueden reemplazar con implementaciones personalizadas.
- El **[registro de intercepción/SQL](~/ef6/fundamentals/logging-and-interception.md)** proporciona bloques de creación de bajo nivel para la interceptación de operaciones EF con un registro de SQL simple basado en la parte superior.
- Las mejoras en la capacidad de **prueba** facilitan la creación de dobles de pruebas para DbContext y DbSet cuando se [usa un marco ficticio](~/ef6/fundamentals/testing/mocking.md) o se [escriben sus propios dobles de pruebas](~/ef6/fundamentals/testing/writing-test-doubles.md).
- **[DbContext ahora se puede crear con un DbConnection que ya está abierto](~/ef6/fundamentals/connection-management.md)** , lo que permite escenarios donde sería útil si la conexión pudiera estar abierta al crear el contexto (por ejemplo, compartir una conexión entre los componentes en los que no se puede garantizar). el estado de la conexión).
- La **[compatibilidad con transacciones mejorada](~/ef6/saving/transactions.md)** proporciona compatibilidad con una transacción externa al marco de trabajo, así como formas mejoradas de crear una transacción en el marco de trabajo.
- **Enumeraciones, un rendimiento espacial y mejor en .net 4,0** : moviendo los componentes principales que solía haber en el .NET Framework al paquete de NUGET de EF, ahora podemos ofrecer compatibilidad con enumeraciones, tipos de datos espaciales y mejoras de rendimiento de EF5 en .net 4,0.
- **Rendimiento mejorado de Enumerable. contiene en consultas LINQ**.
- **Tiempo de preparación mejorado (generación de vistas)** , especialmente para los modelos de gran tamaño.
- **Servicio de &amp; singularidad de pluralización conectable**.
- Ahora se admiten las **implementaciones personalizadas de Equals o GetHashCode** en las clases de entidad.
- **DbSet. AddRange/RemoveRange** proporciona una manera optimizada de agregar o quitar varias entidades de un conjunto.
- **DbChangeTracker. HasChanges** proporciona una manera sencilla y eficaz de ver si hay cambios pendientes que guardar en la base de datos.
- **SqlCeFunctions** proporciona un equivalente de SQL Compact a SqlFunctions.

Las siguientes características solo se aplican a Code First:

- Las **[convenciones de Code First personalizadas](~/ef6/modeling/code-first/conventions/custom.md)** permiten escribir sus propias convenciones para evitar una configuración repetida. Proporcionamos una API simple para convenciones ligeras, así como algunos bloques de creación más complejos para que pueda crear convenciones más complicadas.
- Ahora se admite la **[asignación de Code First a los procedimientos almacenados de inserción, actualización y eliminación](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)** .
- Los **[scripts de migración idempotente](~/ef6/modeling/code-first/migrations/index.md)** permiten generar un script SQL que puede actualizar una base de datos en cualquier versión hasta la versión más reciente.
- La **[tabla de historial de migraciones configurable](~/ef6/modeling/code-first/migrations/history-customization.md)** le permite personalizar la definición de la tabla de historial de migraciones. Esto es especialmente útil para los proveedores de bases de datos que requieren los tipos de datos adecuados, etc., para que la tabla de historial de migraciones funcione correctamente.
- **Varios contextos por base de datos** quitan la limitación anterior de un modelo de Code First por base de datos cuando se usan migraciones o cuando Code First crea automáticamente la base de datos.
- **[DbModelBuilder. HasDefaultSchema](~/ef6/modeling/code-first/fluent/types-and-properties.md)** es una nueva API de Code First que permite configurar el esquema de la base de datos predeterminado para un modelo de Code First en un solo lugar. Anteriormente, el esquema predeterminado de Code First estaba codificado de &quot;forma&quot; rígida en DBO y la única manera de configurar el esquema al que pertenecía una tabla era a través de la API de ToTable.
- El **método DbModelBuilder.** Configurations. AddFromAssembly permite agregar fácilmente todas las clases de configuración definidas en un ensamblado cuando se usan clases de configuración con la API fluida de Code First.
- **[Las operaciones de migración personalizadas](http://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** le permitían agregar operaciones adicionales para usarlas en las migraciones basadas en código.
- El **nivel de aislamiento de transacción predeterminado se cambia a READ_COMMITTED_SNAPSHOT** en el caso de las bases de datos creadas con Code First, lo que permite una mayor escalabilidad y menos interbloqueos.
- Los **tipos de entidad y complejos ahora pueden ser clases nestedinside**. |

## <a name="ef-50"></a>EF 5,0
El tiempo de ejecución de EF 5.0.0 se lanzó a NuGet en agosto de 2012.
En esta versión se presentan algunas características nuevas, como la compatibilidad con enumeraciones, las funciones con valores de tabla, los tipos de datos espaciales y diversas mejoras de rendimiento.

En el Entity Framework Designer de Visual Studio 2012 también se incluye la compatibilidad con varios diagramas por modelo, el color de las formas en la superficie de diseño y la importación por lotes de procedimientos almacenados.

Esta es una lista de contenido que colocamos específicamente para la versión EF 5.

-   [Publicación de la versión de EF 5](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   Nuevas características de EF5
    -   [Compatibilidad de enumeración en Code First](~/ef6/modeling/code-first/data-types/enums.md)
    -   [Compatibilidad de enumeración en EF Designer](~/ef6/modeling/designer/data-types/enums.md)
    -   [Tipos de datos espaciales en Code First](~/ef6/modeling/code-first/data-types/spatial.md)
    -   [Tipos de datos espaciales en EF Designer](~/ef6/modeling/designer/data-types/spatial.md)
    -   [Compatibilidad del proveedor con tipos espaciales](~/ef6/fundamentals/providers/spatial-support.md)
    -   [Funciones con valores de tabla](~/ef6/modeling/designer/advanced/tvfs.md)
    -   [Varios diagramas por modelo](~/ef6/modeling/designer/multiple-diagrams.md)
-   Configuración del modelo
    -   [Creación de un modelo](~/ef6/modeling/index.md)
    -   [Conexiones y modelos](~/ef6/fundamentals/configuring/connection-strings.md)
    -   [Consideraciones sobre el rendimiento](~/ef6/fundamentals/performance/perf-whitepaper.md)
    -   [Trabajar con Microsoft SQL Azure](~/ef6/fundamentals/connection-resiliency/retry-logic.md)
    -   [Configuración del archivo de configuración](~/ef6/fundamentals/configuring/config-file.md)
    -   [Glosario](~/ef6/resources/glossary.md)
    -   Code First
        -   [Code First a una nueva base de datos (tutorial y vídeo)](~/ef6/modeling/code-first/workflows/new-database.md)
        -   [Code First a una base de datos existente (tutorial y vídeo)](~/ef6/modeling/code-first/workflows/existing-database.md)
        -   [Convenciones](~/ef6/modeling/code-first/conventions/built-in.md)
        -   [Anotaciones de datos](~/ef6/modeling/code-first/data-annotations.md)
        -   [API fluida: configuración/asignación de propiedades & tipos](~/ef6/modeling/code-first/fluent/types-and-properties.md)
        -   [API fluida: configuración de relaciones](~/ef6/modeling/code-first/fluent/relationships.md)
        -   [API fluida con VB.NET](~/ef6/modeling/code-first/fluent/vb.md)
        -   [Migraciones de Code First](~/ef6/modeling/code-first/migrations/index.md)
        -   [Migraciones de Code First automática](~/ef6/modeling/code-first/migrations/automatic.md)
        -   [Migrate. exe](~/ef6/modeling/code-first/migrations/migrate-exe.md)
        -   [Definir DbSets](~/ef6/modeling/code-first/dbsets.md)
    -   EF Designer
        -   [Model First (tutorial y vídeo)](~/ef6/modeling/designer/workflows/model-first.md)
        -   [Database First (tutorial y vídeo)](~/ef6/modeling/designer/workflows/database-first.md)
        -   [Tipos complejos](~/ef6/modeling/designer/data-types/complex-types.md)
        -   [Asociaciones/relaciones](~/ef6/modeling/designer/relationships.md)
        -   [Patrón de herencia de TPT](~/ef6/modeling/designer/inheritance/tpt.md)
        -   [Patrón de herencia TPH](~/ef6/modeling/designer/inheritance/tph.md)
        -   [Consulta con procedimientos almacenados](~/ef6/modeling/designer/stored-procedures/query.md)
        -   [Procedimientos almacenados con varios conjuntos de resultados](~/ef6/modeling/designer/advanced/multiple-result-sets.md)
        -   [INSERT, Update & Delete con procedimientos almacenados](~/ef6/modeling/designer/stored-procedures/cud.md)
        -   [Asignación de una entidad a varias tablas (División de entidades)](~/ef6/modeling/designer/entity-splitting.md)
        -   [Asignar varias entidades a una tabla (División de tablas)](~/ef6/modeling/designer/table-splitting.md)
        -   [Definir consultas](~/ef6/modeling/designer/advanced/defining-query.md)
        -   [Plantillas de generación de código](~/ef6/modeling/designer/codegen/index.md)
        -   [Revertir a ObjectContext](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)
-   Uso del modelo
    -   [Trabajar con DbContext](~/ef6/fundamentals/working-with-dbcontext.md)
    -   [Consulta/búsqueda de entidades](~/ef6/querying/index.md)
    -   [Trabajar con relaciones](~/ef6/fundamentals/relationships.md)
    -   [Carga de entidades relacionadas](~/ef6/querying/related-data.md)
    -   [Trabajar con datos locales](~/ef6/querying/local-data.md)
    -   [Aplicaciones de N niveles](~/ef6/fundamentals/disconnected-entities/index.md)
    -   [Consultas SQL sin formato](~/ef6/querying/raw-sql.md)
    -   [Patrones de simultaneidad optimista](~/ef6/saving/concurrency.md)
    -   [Trabajar con servidores proxy](~/ef6/fundamentals/proxies.md)
    -   [Detección automática de cambios](~/ef6/saving/change-tracking/auto-detect-changes.md)
    -   [Consultas sin seguimiento](~/ef6/querying/no-tracking.md)
    -   [Método de carga](~/ef6/querying/load-method.md)
    -   [Agregar, adjuntar y Estados de entidad](~/ef6/saving/change-tracking/entity-state.md)
    -   [Trabajar con valores de propiedad](~/ef6/saving/change-tracking/property-values.md)
    -   [Enlace de datos con WPF (Windows Presentation Foundation)](~/ef6/fundamentals/databinding/wpf.md)
    -   [Enlace de datos con WinForms (Windows Forms)](~/ef6/fundamentals/databinding/winforms.md)

## <a name="ef-431"></a>EF 4.3.1
El tiempo de ejecución de EF 4.3.1 se lanzó a NuGet en febrero de 2012 poco después de EF 4.3.0.
Esta versión de revisión incluyó algunas correcciones de errores en la versión EF 4,3 y presentó una mejor compatibilidad con LocalDB para clientes que usan EF 4,3 con Visual Studio 2012.

Esta es una lista de contenido que colocamos en concreto para la versión EF 4.3.1. la mayor parte del contenido proporcionado para EF 4,1 también se aplica también a EF 4,3.

-   [Publicación de blog de la versión de EF 4.3.1](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4,3
El tiempo de ejecución de EF 4.3.0 se lanzó a NuGet en febrero de 2012.
Esta versión incluye la nueva característica Migraciones de Code First que permite cambiar incrementalmente una base de datos creada por Code First a medida que el modelo de Code First evolucione.

Esta es una lista de contenido que colocamos en concreto para la versión EF 4,3, la mayor parte del contenido proporcionado para EF 4,1 sigue siendo aplicable a EF 4,3 también:
-   [Publicación de la versión de EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [Tutorial de migraciones basadas en código EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [Tutorial de migraciones automáticas de EF 4,3](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4,2
El tiempo de ejecución de EF 4.2.0 se lanzó a NuGet en noviembre de 2011.
En esta versión se incluyen correcciones de errores de la versión EF 4.1.1.
Dado que esta versión solo incluye correcciones de errores, podría haber sido la versión de revisión EF 4.1.2, pero decidimos pasar a 4,2 para dejar de usar los números de versión de revisión de fecha que usamos en las versiones 4.1. x y adoptar el estándar de [control de versiones semántico](https://semver.org) para s control de versiones de emantic.

Esta es una lista de contenido que colocamos en concreto para la versión EF 4,2, el contenido proporcionado para EF 4,1 todavía se aplica también a EF 4,2.

-   [Publicación de la versión de EF 4,2](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Code First tutorial](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [Tutorial de Database First de & de modelo](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1.1
El tiempo de ejecución de EF 4.1.10715 se lanzó a NuGet en julio de 2011.
Además de las correcciones de errores, esta versión de revisión incorporó algunos componentes para facilitar el trabajo de las herramientas en tiempo de diseño con un modelo de Code First.
Estos componentes se usan en Migraciones de Code First (incluido en EF 4,3) y las herramientas avanzadas de EF.

Observará que el número de versión extraño 4.1.10715 del paquete.
Usamos para usar versiones de revisión basadas en fechas antes de decidir adoptar las versiones [semánticas](https://semver.org).
Piense en esta versión como EF 4,1 patch 1 (o EF 4.1.1).

Esta es una lista de contenido que colocamos para la versión 4.1.1:

-   [Publicación de la versión de EF 4.1.1](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4,1
El tiempo de ejecución de EF 4.1.10331 fue el primero en publicarse en NuGet, en abril de 2011.
Esta versión incluye la API de DbContext simplificada y el flujo de trabajo Code First.

Observará el número de versión extraño, 4.1.10331, que realmente debería ser 4,1. Además, hay una versión de 4.1.10311 que debe ser 4.1.0-RC (' RC ' significa ' Release Candidate ').
Usamos para usar versiones de revisión basadas en fechas antes de decidir adoptar las versiones [semánticas](https://semver.org).

Esta es una lista de contenido que colocamos juntos para la versión 4,1. Gran parte de este todavía se aplica a las versiones posteriores de Entity Framework:

-   [Publicación de la versión de EF 4,1](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Code First tutorial](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [Tutorial de Database First de & de modelo](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure federaciones y el Entity Framework](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4,0
Esta versión se incluyó en .NET Framework 4 y Visual Studio 2010, en abril de 2010.
Las nuevas características importantes de esta versión incluyen compatibilidad POCO, asignación de claves externas, carga diferida, mejoras en la capacidad de prueba, generación de código personalizable y el flujo de trabajo Model First.

Aunque era la segunda versión de Entity Framework, se llamaba EF 4 para que se alinee con la versión de .NET Framework con la que se distribuyó.
Después de esta versión, comenzamos a poner Entity Framework disponible en NuGet y adoptamos las versiones semánticas, puesto que ya no se asociaron a la versión .NET Framework.

Tenga en cuenta que algunas versiones posteriores de .NET Framework se han incluido con actualizaciones significativas de los bits EF incluidos.
De hecho, muchas de las nuevas características de EF 5,0 se implementaron como mejoras en estos bits.
Sin embargo, para racionalizar el caso de control de versiones para EF, seguimos haciendo referencia a los bits EF que forman parte de la .NET Framework como el tiempo de ejecución de EF 4,0, mientras que todas las versiones más recientes están compuestas por el [paquete NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/).

## <a name="ef-35"></a>EF 3,5
La versión inicial de Entity Framework se incluyó en .NET 3,5 Service Pack 1 y Visual Studio 2008 SP1, publicada en agosto de 2008.
Esta versión proporcionó compatibilidad básica con O/RM mediante el flujo de trabajo de Database First.
