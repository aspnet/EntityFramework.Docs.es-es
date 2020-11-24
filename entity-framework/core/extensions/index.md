---
title: Herramientas y extensiones - EF Core
description: Herramientas y extensiones externas para Entity Framework Core
author: ErikEJ
ms.date: 04/11/2020
uid: core/extensions/index
ms.openlocfilehash: dc8125ab522b5b0c5126fda2d7c8093c2babdc29
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003307"
---
# <a name="ef-core-tools--extensions"></a>Herramientas y extensiones de EF Core

Estas herramientas y extensiones proporcionan más funcionalidades para Entity Framework Core 2.1 y versiones posteriores.

> [!IMPORTANT]  
> Las extensiones están compiladas a partir de una gran variedad de orígenes, por lo que su mantenimiento no está incluido en el proyecto Entity Framework Core. En lo que respecta a las extensiones de terceros y para asegurarse de que cumplan sus requisitos, no se olvide de evaluar la calidad, las licencias, la compatibilidad, el soporte técnico, etc. En concreto, es posible que sea necesario actualizar una extensión compilada para una versión anterior de EF Core para que funcione con las versiones más recientes.

## <a name="tools"></a>Herramientas

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro es una solución de modelado de entidad compatible con Entity Framework y Entity Framework Core. Permite definir fácilmente el modelo de entidad y asignarlo a la base de datos mediante Database First o Model First, de modo que pueda empezar a escribir consultas de inmediato. Para EF Core: 2, 3

[Sitio web](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer es un potente diseñador O/RM para ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access y LINQ to SQL. Permite diseñar modelos EF Core de forma visual mediante los enfoques Database First y Model First, así como generar código C# y de Visual Basic. Para EF Core: 1, 2, 3, 5.

[Sitio web](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>nHydrate ORM para Entity Framework

O/RM que crea clases extensibles y fuertemente tipadas para Entity Framework. El código generado es Entity Framework Core. No hay ninguna diferencia. Esto no es un reemplazo de EF ni un O/RM personalizado. Es una capa de modelado visual que permite a un equipo administrar esquemas de base de datos complejos. Funciona bien con software SCM como GIT, lo que permite el acceso de varios usuarios al modelo con conflictos mínimos. El instalador realiza un seguimiento de los cambios del modelo y crea scripts de actualización. Para EF Core: 3.

[Sitio de GitHub](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools es una extensión de Visual Studio que expone varias tareas de tiempo de diseño de EF Core en una interfaz de usuario sencilla. Incluye técnicas de ingeniería inversa de DbContext y clases de entidades a partir de bases de datos existentes y [DACPAC de SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), así como la administración de la migración de bases de datos y la visualización de modelos. Para EF Core: 2, 3.

[Wiki de GitHub](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Editor visual de Entity Framework

El Editor de objetos visuales de Entity Framework es una extensión de Visual Studio que agrega un diseñador O/RM que permite generar objetos visuales de las clases EF 6 y EF Core. El código se genera mediante plantillas T4, por lo que se puede personalizar. Asimismo, admite enumeraciones y asociaciones de herencia, unidireccionales y bidireccionales, y permite asignar colores a las clases y agregar bloques de texto para explicar ciertas partes del diseño que puedan resultar difíciles de comprender. Para EF Core: 2.

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory es un motor de scaffolding para .NET Core que permite automatizar la generación de clases DbContext, entidades, opciones de configuración de la asignación y clases de repositorios de una base de datos SQL Server. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>LoreSoft's Entity Framework Core Generator

Entity Framework Core Generator (efg) es una herramienta de la CLI de .NET Core que permite generar modelos EF Core a partir de una base de datos existente. Es similar a `dotnet ef dbcontext scaffold`, pero también admite la [regeneración](https://efg.loresoft.com/en/latest/regeneration/) de código seguro mediante el reemplazo de la región o el análisis de los archivos de asignación. Asimismo, la herramienta permite generar modelos de vista, efectuar la validación y crear el código de los asignadores de objetos. Para EF Core: 2.

[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentación](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>Extensiones

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

Biblioteca de extensiones que permite registrar automáticamente los cambios en los datos realizados por EF Core e incluirlos en una tabla a modo de historial. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

El almacenamiento en caché de segundo nivel es una caché de consulta. Los resultados de los comandos de EF se almacenarán en la memoria caché, para que los mismos comandos de EF recuperen los datos de la memoria caché en lugar de ejecutarlos de nuevo en la base de datos. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (Generator Console) es un generador de código muy sencillo que se basa en un proyecto de consola. Para generar el código, se ejecuta en .NET Core y utiliza cadenas C# interpoladas. Geco incluye un generador de modelos de ingeniería inversa para EF Core que admite la pluralización, la singularización y las plantillas editables. También proporciona un generador de scripts de datos semilla, un ejecutor de scripts y una herramienta para limpiar las bases de datos. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore.Scaffolding.Handlebars

Permite la personalización de clases con ingeniería inversa a partir de una base de datos existente mediante la cadena de herramientas de Entity Framework Core con las plantillas de Handlebars. Para EF Core: 2, 3.

[Repositorio de GitHub](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq amplía las características de proveedores LINQ como Entity Framework para permitir la reutilización de las funciones, la reescritura de las consultas y la creación de consultas dinámicas mediante selectores y predicados traducibles. Para EF Core: 2, 3.

[Repositorio de GitHub](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Extensión de Microsoft.EntityFrameworkCore para admitir los repositorios, los patrones de unidades de trabajo y varias bases de datos compatibles con las transacciones distribuidas. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

Extensiones de EF Core para operaciones masivas (Insert, Update y Delete). Para EF Core: 2, 3.

[Repositorio de GitHub](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

Agrega pluralización en tiempo de diseño. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

Recuperación del atributo [Index] (con extensión para la creación de modelos). Para EF Core: 2, 3.

[Repositorio de GitHub](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

Proporciona un contenedor alrededor del proveedor de la base de datos en memoria de EF Core. Así, hace que actúe de una forma más similar a la de un proveedor relacional. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

Implementación de compatibilidad temporal. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

Realice consultas temporales fácilmente en su base de datos favorita con los métodos de extensión incorporados: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a>EFCore.TimeTraveler

Permita consultas completas de Entity Framework Core en el [historial temporal de SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) mediante el código de EF Core, las entidades y las asignaciones que ya ha definido.  Para viajar a través del tiempo, encapsule el código en `using (TemporalQuery.AsOf(targetDateTime)) {...}`. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/VantageSoftware/EFCore.TimeTraveler)

### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore.TemporalTables

Biblioteca de extensiones para Entity Framework Core que permite a los desarrolladores que usan SQL Server utilizar fácilmente tablas temporales. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

Caché de consulta de segundo nivel y alto rendimiento. Para EF Core: 2.

[Repositorio de GitHub](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a>EntityFrameworkCore.NCache

NCache de Entity Framework Core es un proveedor de caché de segundo nivel distribuido para almacenar en caché resultados de consultas. La arquitectura distribuida de NCache hace que sea más escalable y de alta disponibilidad. Para EF Core 2.

[Sitio web](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a>Entity Framework Plus

Amplía su DbContext con características como: Incluir filtro, Auditoría, Cache, Consulta de futuro, Batch Delete, Actualización por lotes y más. Para EF Core: 2, 3.

[Sitio web](https://entityframework-plus.net/)
[Repositorio de GitHub](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Extensiones de Entity Framework

Extiende su DbContext con operaciones masivas de alto rendimiento: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge y más. Para EF Core: 2, 3.

[Sitio web](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

Agregue compatibilidad para llamar a métodos de extensión en expresiones lambda LINQ. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a>XLinq

Tecnología Language Integrated Query (LINQ) para bases de datos relacionales. Permite usar C# para escribir consultas fuertemente tipadas. Para EF Core: 3.

- Compatibilidad total de C# con la creación de consultas: varias instrucciones dentro de lambda, variables, funciones, etc.
- Sin vacío semántico con SQL. XLinq declara instrucciones SQL (como `SELECT`, `FROM`, `WHERE`) como métodos de C# de primera clase, combinando la sintaxis conocida con IntelliSense, seguridad de tipos y refactorización.

Como resultado, SQL se convierte en "otra" biblioteca de clases que expone su API localmente, literalmente *"Language Integrated SQL"* .

[Sitio web](http://xlinq.live/)

### <a name="ramses"></a>Ramses

Enlaces de ciclo de vida (para SaveChanges). Para EF Core: 2, 3.

[Repositorio de GitHub](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

Todos los nombres de tabla y columna tendrán automáticamente un formato con palabras combinadas unidas por barras bajas (snake_case), todo en MAYÚSCULAS o bien todo en minúsculas. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime

Agrega compatibilidad nativa a EntityFrameworkCore para SQL Server para los tipos NodaTime. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble.EntityFrameworkCore.Temporal.Query

Extensiones LINQ en Entity Framework Core 3.1 para admitir la realización de consultas a tablas temporales de Microsoft SQL Server. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore.SqlServer.HierarchyId

Agrega compatibilidad con hierarchyid al proveedor de EF Core de SQL Server. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a>linq2db.EntityFrameworkCore

Traductor alternativo de consultas LINQ a expresiones SQL. Para EF Core: 3.

Incluye compatibilidad con características SQL avanzadas como CTE, copia masiva, sugerencias de tabla, funciones de división de particiones, tablas temporales y operaciones de creación, actualización y eliminación en la base de datos.

[Repositorio de GitHub](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a>EFCore.SoftDelete

Implementación de entidades de eliminación temporal. Para EF Core: 3.

[NuGet](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a>EntityFrameworkCore.ConfigurationManager

Extiende EF Core para resolver cadenas de conexión de App.config. Para EF Core: 3.

[Repositorio de GitHub](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a>Asignador desasociado

Un asignador de entidades DTO con control de composición/agregación (similar a GraphDiff). Para EF Core: 3.5.

[NuGet](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)
