---
title: Plan para Entity Framework Core 6.0
description: Temas y características planeados para EF Core 6.0
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129568"
---
# <a name="plan-for-entity-framework-core-60"></a>Plan para Entity Framework Core 6.0

Como se describe en el [proceso de planificación](xref:core/what-is-new/release-planning), se ha recopilado información de las partes interesadas en un plan para la versión 6.0 de Entity Framework Core (EF Core).

A diferencia de las versiones anteriores, este plan no intenta abarcar todo el trabajo de la versión 6.0. En su lugar, indica dónde y cómo se pretende invertir en esta versión, pero con flexibilidad para ajustar el ámbito o incorporar el nuevo trabajo a medida que se recopilen comentarios y se aprenda mientras se trabaja en la versión.

> [!IMPORTANT]
> Este plan no es un compromiso. Es un punto de partida que evolucionará a medida que se obtenga más información. Es posible que algunos aspectos no planeados en la actualidad se incorporen a la versión 6.0. Es posible que algunos aspectos planeados en la actualidad se eliminen de la versión 6.0.

## <a name="general-information"></a>Información general

### <a name="version-number-and-release-date"></a>Número de versión y fecha de lanzamiento

EF Core 6.0 es la siguiente versión después de EF Core 5.0 y actualmente está programada para su lanzamiento en noviembre de 2021 al mismo tiempo que .NET 6.

### <a name="supported-platforms"></a>Plataformas admitidas

Actualmente, EF Core 6.0 se destina a .NET 5. Lo más probable es que se actualice a .NET 6 a medida que se acerque el lanzamiento. EF Core 6.0 no tiene como destino ninguna versión de .NET Standard; para obtener más información, vea [El futuro de .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).

EF Core 6.0 no se ejecutará en .NET Framework.

EF Core 6.0 se alineará con .NET 6 como una [versión de soporte técnico a largo plazo (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="breaking-changes"></a>Últimos cambios

EF Core 6.0 contendrá un pequeño número de [cambios importantes](xref:core/what-is-new/ef-core-6.0/breaking-changes) a medida que EF Core y la plataforma .NET sigan evolucionando. El objetivo es permitir que la gran mayoría de las aplicaciones se actualicen sin interrupciones.

## <a name="themes"></a>Temas

Las siguientes áreas constituirán la base de las principales inversiones en EF Core 6.0.

## <a name="highly-requested-features"></a>Características muy solicitadas

Como siempre, una entrada importante del [proceso de planificación](xref:core/what-is-new/release-planning) procede de la [votación (👍) de características en GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc). Para EF Core 6.0 está previsto trabajar en las siguientes características muy solicitadas:

### <a name="sql-server-temporal-tables"></a>Tablas temporales de SQL Server

Número de seguimiento: [4693](https://github.com/dotnet/efcore/issues/4693)

Estado: Sin iniciar

Talla de camiseta: Grande

Las tablas temporales admiten consultas de datos almacenados en la tabla en _cualquier momento_, en lugar de solo los más recientes, como sucede con las tablas normales. EF Core 6.0 permitirá crear tablas temporales mediante Migraciones, además de permitir el acceso a los datos a través de consultas LINQ.

Inicialmente, el ámbito de este trabajo se limita como se [describe en la incidencia](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974). Es posible que se incorpore soporte adicional en función de los comentarios durante el lanzamiento.

### <a name="json-columns"></a>Columnas JSON

Número de seguimiento: [4021](https://github.com/dotnet/efcore/issues/4021)

Estado: Sin iniciar

Talla de camiseta: Medium

Esta característica presentará un mecanismo común y patrones para la compatibilidad con JSON que cualquier proveedor de bases de datos puede implementar. Se trabajará con la comunidad para alinear las implementaciones existentes de [Npgsql](https://github.com/npgsql/efcore.pg) y [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), además de agregar compatibilidad con SQL Server y SQLite.

### <a name="columnattributeorder"></a>ColumnAttribute.Order

Número de seguimiento: [10059](https://github.com/dotnet/efcore/issues/10059)

Estado: Sin iniciar

Talla de camiseta: Pequeña

Esta característica permitirá el orden arbitrario de las columnas al **crear una tabla** con Migraciones o `EnsureCreated`. Tenga en cuenta que para cambiar el orden de las columnas en las tablas existentes es necesario volver a generar la tabla, y esto es algo que no está previsto admitir en ninguna versión de EF Core.

## <a name="performance"></a>Rendimiento

Aunque por lo general EF Core es más rápido que EF6, todavía hay áreas en las que se puede mejorar significativamente el rendimiento. Está previsto abordar varias de estas áreas en EF Core 6.0, a la vez que se mejoran la infraestructura y las pruebas de rendimiento.

Este tema implicará una gran cantidad de investigación iterativa, que informará de los recursos elegidos como destino. Está previsto comenzar con lo siguiente:

### <a name="performance-infrastructure-and-new-tests"></a>Infraestructura de rendimiento y nuevas pruebas

Estado: Sin iniciar

Talla de camiseta: Medium

El código base de EF Core ya contiene un conjunto de bancos de pruebas de rendimiento que se ejecutan todos los días. En la versión 6.0, está previsto mejorar la infraestructura de estas pruebas, así como agregar otras nuevas. También se generarán perfiles de escenarios de rendimiento principales y se corregirá cualquier instancia de nivel inferior.

### <a name="compiled-models"></a>Modelos compilados

Número de seguimiento: [1906](https://github.com/dotnet/efcore/issues/1906)

Estado: Sin iniciar

Talla de camiseta: Extra grande

Los modelos compilados permitirán la generación de un formato compilado del modelo de EF. Esto proporcionará un mejor rendimiento de inicio, así como un mejor rendimiento general al acceder al modelo.

### <a name="techempower-fortunes"></a>TechEmpower Fortunes

Número de seguimiento: [23611](https://github.com/dotnet/efcore/issues/23611)

Estado: Sin iniciar

Talla de camiseta: Extra grande

Durante varios años se han ejecutado los [bancos de pruebas de TechEmpower](https://www.techempower.com/benchmarks/) estándar del sector en .NET sobre una base de datos PostgreSQL. El [banco de pruebas de Fortune](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) es especialmente relevante para los escenarios de EF. Se dispone de varias variaciones de este banco de pruebas, entre las que se incluyen las siguientes:

- Una implementación en la que se usa ADO.NET directamente. Esta es la implementación más rápida de las tres que se muestran aquí.
- Una implementación en la que se usa [Dapper](https://www.nuget.org/packages/Dapper/). Es más lenta que cuando se usa ADO.NET directamente, pero sigue siendo rápida.
- Una implementación en la que se usa EF Core. Actualmente esta es la implementación más lenta de las tres.

El objetivo de EF Core 6.0 es conseguir que el rendimiento de EF Core coincida con el de Dapper en el banco de pruebas de TechEmpower Fortunes. (Es un reto importante, pero se hará todo lo posible para acercarse al máximo).

### <a name="linkeraot"></a>Enlazador/AOT

Número de seguimiento: [10963](https://github.com/dotnet/efcore/issues/10963)

Estado: Sin iniciar

Talla de camiseta: Medium

EF Core realiza grandes cantidades de generación de código en tiempo de ejecución. Esto supone un reto para los modelos de aplicación que dependen de la modificación del árbol del enlazador, como Xamarin y Blazor, y para las plataformas que no permiten la compilación dinámica, como iOS. Se seguirá investigando en este espacio como parte de EF Core 6.0 y, siempre que se pueda, se intentarán realizar mejoras concretas. Pero no se espera solventar todas las diferencias por completo durante el intervalo de tiempo de la versión 6.0.

## <a name="migrations-and-deployment"></a>Migraciones e implementación

A partir de las [investigaciones realizadas para EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), está previsto incorporar compatibilidad mejorada para la administración de migraciones y la implementación de bases de datos. Esto incluye dos áreas principales:

### <a name="migrations-bundles"></a>Agrupaciones de migraciones

Número de seguimiento: [19693](https://github.com/dotnet/efcore/issues/19693)

Estado: Sin iniciar

Talla de camiseta: Medium

Una agrupación de migraciones es un archivo ejecutable independiente que aplica migraciones a una base de datos de producción. El comportamiento coincidirá con `dotnet ef database update`, pero debe facilitar considerablemente la implementación de SSH, Docker o PowerShell, ya que todo lo que se necesita se incluye en el ejecutable único.

### <a name="managing-migrations"></a>Administración de migraciones

Número de seguimiento: [22945](https://github.com/dotnet/efcore/issues/22945)

Estado: Sin iniciar

Talla de camiseta: Grande

El número de migraciones creadas para una aplicación puede aumentar hasta convertirse en una carga. Además, estas migraciones se suelen implementar con la aplicación aunque no sea necesario. En EF Core 6.0, el plan es mejorar esto mediante mejores herramientas y administración de proyectos y ensamblados. Dos problemas específicos que está previsto abordar son la [inclusión de muchas migraciones en una](https://github.com/dotnet/efcore/issues/2174) y la [regeneración de una instantánea de modelo limpia](https://github.com/dotnet/efcore/issues/18557).

## <a name="improve-existing-features-and-fix-bugs"></a>Mejora de las características existentes y corrección de errores

Actualmente, está previsto para esta versión cualquier [incidencia o error asignado al hito 6.0.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0). Esto incluye muchas mejoras pequeñas y correcciones de errores.

### <a name="ef6-query-parity"></a>Paridad de consultas de EF6

Seguimiento por [incidencias etiquetadas con "ef6-parity" y en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)

Estado: Sin iniciar

Talla de camiseta: Grande

EF Core 5.0 admite la mayoría de los patrones de consulta admitidos por EF6, además de los que no se admiten en EF6. Para EF Core 6.0, está previsto reducir las diferencias y hacer que las consultas de EF Core admitidas sean un superconjunto real de las admitidas en EF6. Esto se controla mediante la investigación de las diferencias, pero ya se incluyen incidencias de GroupBy como la [traducción de GroupBy seguido de FirstOrDefault](https://github.com/dotnet/efcore/issues/12088) y consultas SQL sin procesar para tipos [primitivos](https://github.com/dotnet/efcore/issues/11624) y [no asignados](https://github.com/dotnet/efcore/issues/10753).  

### <a name="value-objects"></a>Objetos de valor

Número de seguimiento: [9906](https://github.com/dotnet/efcore/issues/9906)

Estado: Sin iniciar

Talla de camiseta: Medium

Anteriormente, la vista de equipo era la propietaria de las entidades, destinadas a la [compatibilidad agregada](https://www.martinfowler.com/bliki/DDD_Aggregate.html), lo que también sería una aproximación razonable a los [objetos de valor](https://www.martinfowler.com/bliki/ValueObject.html). La experiencia ha demostrado que este no es el caso. Por tanto, en EF Core 6.0 está previsto introducir una mejor experiencia centrada en las necesidades de los objetos de valor en el diseño controlado por dominios. Este enfoque se basará en convertidores de valores, en lugar de entidades de propiedad.

Inicialmente, el ámbito de este trabajo es permitir [convertidores de valores que se asignan a varias columnas](https://github.com/dotnet/efcore/issues/13947). Es posible que se incorpore soporte adicional en función de los comentarios durante el lanzamiento.

### <a name="cosmos-database-provider"></a>Proveedor de base de datos Cosmos

Seguimiento por [incidencias etiquetadas con "area-cosmos" y en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)

Estado: Sin iniciar

Talla de camiseta: Grande

De forma activa se recopilan comentarios sobre las mejoras que se deben llevar a cabo en el proveedor Cosmos en EF Core 6.0. Este documento se actualizará a medida que se obtenga más información. Por ahora, asegúrese de votar (👍) por las características de Cosmos que necesite.

### <a name="expose-model-building-conventions-to-applications"></a>Exposición de convenciones de creación de modelos para las aplicaciones

Número de seguimiento: [214](https://github.com/dotnet/efcore/issues/214)

Estado: Sin iniciar

Talla de camiseta: mediana

EF Core usa un conjunto de convenciones para crear un modelo a partir de tipos de .NET. Actualmente, estas convenciones las controla el proveedor de base de datos. En EF Core 6.0 está previsto permitir que las aplicaciones se conecten a estas convenciones y las cambien.

### <a name="zero-bug-balance-zbb"></a>Equilibrio de cero errores (ZBB)

Seguimiento por [incidencias etiquetadas con `type-bug` en el hito 6.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)

Estado: En curso

Talla de camiseta: Grande

Está previsto corregir todos los errores pendientes durante el período de tiempo de EF Core 6.0. Algunos aspectos que se deben tener en cuenta:

- Esto se aplica específicamente a las incidencias con la etiqueta [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).
- Habrá excepciones, como cuando el error requiere un cambio de diseño o una característica nueva para corregirlo correctamente. Estas incidencias se marcarán con la etiqueta `blocked`.
- Los errores se puntuarán en función del riesgo cuando sea necesario, lo normal a medida que se aproxima una versión de GA/RTM.

### <a name="miscellaneous-features"></a>Características varias

Seguimiento por [incidencias etiquetadas con `type-enhancement` en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)

Estado: En curso

Talla de camiseta: Grande

Las características varias planeadas para EF 6.0 incluyen, entre otras, las siguientes:

- [División de consultas para colecciones que no son de navegación](https://github.com/dotnet/efcore/issues/21234)
- [Detección de tablas de combinación simples en técnicas de ingeniería inversa y creación de relaciones de varios a varios](https://github.com/dotnet/efcore/issues/22475)
- [Finalización de la búsqueda de texto completo o libre en SQLite y SQL Server](https://github.com/dotnet/efcore/issues/4823)
- [Índices espaciales de SQL Server](https://github.com/dotnet/efcore/issues/12538)
- [Mecanismo o API para especificar una conversión predeterminada para cualquier propiedad de un tipo determinado en el modelo](https://github.com/dotnet/efcore/issues/10784)
- [Uso de la nueva API de procesamiento por lotes de ADO.NET](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a>integración en .NET

El equipo de EF Core también trabaja en varias tecnologías relacionadas pero independientes. En concreto, está previsto trabajar en lo siguiente:

### <a name="enhancements-to-systemdata"></a>Mejoras en System.Data

Seguimiento por [incidencias en el repositorio dotnet\runtime etiquetadas con `area-System.Data` en el hito 6.0](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)

Estado: Sin iniciar

Talla de camiseta: Grande

Este trabajo incluye lo siguiente:

- Implementación de la nueva [API de procesamiento por lotes](https://github.com/dotnet/runtime/issues/28633).
- Trabajo continuado con otros equipos de .NET y la comunidad para comprender y desarrollar ADO.NET.
- [Uso estándar de DiagnosticSource para el seguimiento en componentes System.Data.*](https://github.com/dotnet/runtime/issues/22336).

### <a name="enhancements-to-microsoftdatasqlite"></a>Mejoras en Microsoft.Data.Sqlite

Seguimiento por [incidencias etiquetadas con `type-enhancement` y `area-adonet-sqlite` en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)

Estado: En curso

Talla de camiseta: Medium

Se han planificado varias mejoras pequeñas para Microsoft.Data.SQLite, incluidas la [agrupación de conexiones](https://github.com/dotnet/efcore/issues/13837) y las [instrucciones preparadas](https://github.com/dotnet/efcore/issues/14044) para el rendimiento.

### <a name="nullable-reference-types"></a>Tipos de referencia que aceptan valores NULL

Número de seguimiento: [14150](https://github.com/dotnet/efcore/issues/14150)

Estado: En curso

Talla de camiseta: Grande

Se anotará el código de EF Core para usar [tipos de referencia que aceptan valores NULL](/dotnet/csharp/nullable-references).

## <a name="experiments-and-investigations"></a>Experimentos e investigaciones

Durante el período de tiempo de EF Core 6.0, el equipo de EF tiene previsto invertir en la experimentación e investigación en dos áreas. Se trata de un proceso de aprendizaje y, como tal, no se prevé ninguna entrega concreta para la versión 6.0.

### <a name="sqlservercore"></a>SqlServer.Core

Seguimiento en el [repositorio de laboratorios de datos de .NET](https://github.com/dotnet/datalab/)

Estado: Sin iniciar

Talla de camiseta: En curso

[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) es un proveedor de base de datos de ADO.NET con todas las características para SQL Server. Admite una amplia gama de características de SQL Server, tanto en .NET Core como en .NET Framework. Pero también es un código base grande y antiguo con muchas interacciones complejas entre sus comportamientos. Esto dificulta la investigación de las posibles ventajas que se pueden realizar con las nuevas características de .NET Core. Por tanto, se va a iniciar un experimento en colaboración con la comunidad para determinar las posibilidades de un controlador de SQL Server de alta rendimiento para .NET.

> [!IMPORTANT]
> La inversión en Microsoft.Data.SqlClient no va a cambiar. Seguirá siendo la forma recomendada de conectarse a SQL Server y SQL de Azure, tanto con EF Core como sin EF Core. Seguirá admitiendo nuevas características de SQL Server a medida que se introduzcan.

### <a name="graphql"></a>GraphQL

Estado: Sin iniciar

Talla de camiseta: En curso

En los últimos años [GraphQL](https://graphql.org/) ha cobrado importancia en una variedad de plataformas. Estás previsto investigar el espacio y encontrar formas de mejorar la experiencia con .NET. Esto implicará trabajar con la comunidad para comprender y respaldar el ecosistema existente. También puede implicar una inversión específica por parte de Microsoft, ya sea en forma de contribuciones a trabajos existentes o en el desarrollo de elementos gratuitos en la pila de Microsoft.

### <a name="dataverse-formerly-common-data-services"></a>DataVerse (anteriormente Data Services comunes)

Estado: Sin iniciar

Talla de camiseta: En curso

[DataVerse](/powerapps/maker/data-platform/data-platform-intro) es un almacén de datos basado en columnas diseñado para el desarrollo rápido de aplicaciones empresariales. Controla automáticamente tipos de datos complejos como los objetos binarios (BLOB) y tiene entidades y relaciones integradas como organizaciones y contactos. Existe un SDK, pero los desarrolladores pueden beneficiarse de tener un proveedor de EF Core para usar consultas LINQ avanzadas, aprovechar las ventajas de la unidad de trabajo y tener una API de acceso a datos coherente. El equipo considerará diferentes maneras de mejorar la experiencia de conexión a DataVerse para los desarrolladores de .NET.

## <a name="below-the-cut-line"></a>Por debajo de la línea de corte

Seguimiento por [problemas etiquetados con `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)

Se trata de correcciones de errores y mejoras **no** programadas actualmente para la versión 6.0, pero que se examinarán en función de los comentarios a lo largo del lanzamiento junto con el progreso realizado en el trabajo anterior. Estas incidencias se pueden incorporar en EF Core 6.0 y se convierten automáticamente en candidatas para la próxima versión.

Además, durante la planeación siempre se tienen en cuenta los [problemas más votados](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc). Excluir cualquiera de estos problemas de una versión siempre es complicado, pero necesitamos un plan realista para los recursos que tenemos. Asegúrese de que ha votado (👍) por las características que necesita.

## <a name="suggestions"></a>Sugerencias

Sus comentarios sobre la planeación son importantes. La mejor manera de indicar la importancia de una incidencia consiste en votar (👍) por esa incidencia en GitHub. Estos datos se introducirán después en el [proceso de planeación](xref:core/what-is-new/release-planning) de la próxima versión.
