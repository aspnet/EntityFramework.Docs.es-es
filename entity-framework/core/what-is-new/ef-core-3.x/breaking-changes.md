---
title: 'Cambios importantes en EF Core 3.x: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 3.x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: 8c0be4193c79e838e40bfc2dc10c9d12b01381cd
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128776"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a>Cambios importantes incluidos en EF Core 3.x

Es posible que los siguientes cambios de API y comportamiento interrumpan las aplicaciones existentes cuando se actualicen a las versiones 3.x.
Los cambios que esperamos que solo afecten a proveedores de base de datos se documentan en [Cambios para proveedores](xref:core/providers/provider-log).

## <a name="summary"></a>Resumen

| **Cambio importante**                                                                                               | **Impacto** |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [Las consultas LINQ ya no se evalúan en el cliente](#linq-queries-are-no-longer-evaluated-on-the-client)         | Alto       |
| [La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core](#dotnet-ef) | Alto      |
| [DetectChanges respeta los valores de clave generados por el almacén](#dc) | Alto      |
| [FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre](#fromsql) | Alto      |
| [Los tipos de consulta se consolidan con tipos de entidad](#qt) | Alto      |
| [Entity Framework Core ya no forma parte del marco compartido ASP.NET Core](#no-longer) | Media      |
| [Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada](#cascade) | Media      |
| [La carga diligente de entidades relacionadas ahora se realiza en una sola consulta](#eager-loading-single-query) | Media      |
| [DeleteBehavior.Restrict tiene una semántica más limpia](#deletebehavior) | Media      |
| [La API de configuración para las relaciones de tipo de propiedad ha cambiado](#config) | Media      |
| [Cada propiedad usa la generación de claves enteras en memoria independiente](#each) | Media      |
| [Las consultas sin seguimiento ya no realizan la resolución de la identidad](#notrackingresolution) | Media      |
| [Cambios en la API de metadatos](#metadata-api-changes) | Media      |
| [Cambios en la API de metadatos específicos del proveedor](#provider) | Media      |
| [Se ha quitado el elemento UseRowNumberForPaging](#urn) | Media      |
| [Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar](#fromsqlsproc) | Media      |
| [Solo se pueden especificar métodos de FromSql en raíces de consulta](#fromsql) | Bajo      |
| [Los valores de clave temporal ya no se establecen en instancias de entidad](#tkv) | Bajo      |
| [Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales](#de) | Bajo      |
| [Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad](#aes) | Bajo      |
| [Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento](#owned-query) | Bajo      |
| [Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados](#ip) | Bajo      |
| [La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad](#fkp) | Bajo      |
| [Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope](#dbc) | Bajo      |
| [Los campos de respaldo se usan de forma predeterminada](#backing-fields-are-used-by-default) | Bajo      |
| [Inicio de excepciones si se encuentran varios campos de respaldo compatibles](#throw-if-multiple-compatible-backing-fields-are-found) | Bajo      |
| [Los nombres de propiedades de solo campo deben coincidir con el nombre del campo](#field-only-property-names-should-match-the-field-name) | Bajo      |
| [AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache](#adddbc) | Bajo      |
| [AddEntityFramework* agrega IMemoryCache con un límite de tamaño](#addentityframework-adds-imemorycache-with-a-size-limit) | Bajo      |
| [Ahora DbContext.Entry realiza una operación DetectChanges local](#dbe) | Bajo      |
| [El cliente no genera las claves de matriz de cadena y byte de forma predeterminada](#string-and-byte-array-keys-are-not-client-generated-by-default) | Bajo      |
| [Ahora ILoggerFactory es un servicio con ámbito](#ilf) | Bajo      |
| [En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | Bajo      |
| [La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | Bajo      |
| [Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena](#nbh) | Bajo      |
| [El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask](#rtnt) | Bajo      |
| [La anotación Relational:TypeMapping ahora es simplemente TypeMapping](#rtt) | Bajo      |
| [ToTable en un tipo derivado produce una excepción](#totable-on-a-derived-type-throws-an-exception) | Bajo      |
| [EF Core ya no envía pragma para el cumplimiento de SQLite FK](#pragma) | Bajo      |
| [Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3](#sqlite3) | Bajo      |
| [Los valores GUID se almacenan ahora como TEXT en SQLite](#guid) | Bajo      |
| [Ahora los valores char se almacenan como TEXT en SQLite](#char) | Bajo      |
| [Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable](#migid) | Bajo      |
| [La información o los metadatos de la extensión se han quitado de IDbContextOptionsExtension](#xinfo) | Bajo      |
| [LogQueryPossibleExceptionWithAggregateOperator ha cambiado de nombre](#lqpe) | Bajo      |
| [Clarificación de la API para nombres de restricciones de claves externas](#clarify) | Bajo      |
| [IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos](#irdc2) | Bajo      |
| [Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency](#dip) | Bajo      |
| [SQLitePCL.raw se ha actualizado a la versión 2.0.0](#SQLitePCL) | Bajo      |
| [NetTopologySuite se actualizó a la versión 2.0.0](#NetTopologySuite) | Bajo      |
| [Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient](#SqlClient) | Bajo      |
| [Se deben configurar varias relaciones de referencia automática ambiguas](#mersa) | Bajo      |
| [DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo](#udf-empty-string) | Bajo      |
| [~~EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0~~ Revertido](#netstandard21) | |
| [~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido](#qe) | |

## <a name="high-impact-changes"></a>Cambios de impacto alto

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>Las consultas LINQ ya no se evalúan en el cliente

[Problema de seguimiento n.° 14935](https://github.com/dotnet/efcore/issues/14935)
[Consulte también el problema n.° 12795](https://github.com/dotnet/efcore/issues/12795)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de 3.0, cuando en EF Core no se podía convertir una expresión que formaba parte de una consulta SQL o un parámetro, la expresión se evaluaba de forma automática en el cliente.
De forma predeterminada, la evaluación de cliente de expresiones potencialmente costosas solo desencadenaba una advertencia.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de 3.0, en EF Core solo se permite que se evalúen en el cliente las expresiones en la proyección de nivel superior (la última llamada a `Select()` de la consulta).
Cuando las expresiones de otra parte de la consulta no se pueden convertir en SQL o un parámetro, se inicia una excepción.

#### <a name="why"></a>Por qué

La evaluación de cliente automática de las consultas permite que se ejecuten muchas consultas incluso si no se pueden convertir elementos importantes de ellas.
Esto puede provocar un comportamiento inesperado y potencialmente dañino que es posible que solo sea evidente en entornos de producción.
Por ejemplo, una condición en una llamada a `Where()` que no se puede convertir puede provocar que todas las filas de la tabla se transfieran desde el servidor de base de datos y que el filtro se aplique en el cliente.
Esta situación puede pasar desapercibida fácilmente si la tabla solo contiene algunas filas en la fase de desarrollo, pero ser más grave cuando la aplicación pase a producción, donde la tabla puede contener millones de filas.
Las advertencias de evaluación de cliente también se suelen pasar por alto durante el desarrollo.

Además de esto, la evaluación de cliente automática puede causar problemas en los que la mejora de la traducción de consultas para expresiones específicas provocaba cambios importantes no deseados entre versiones.

#### <a name="mitigations"></a>Mitigaciones

Si una consulta no se puede traducir totalmente, vuelva a escribirla en un formato que se pueda traducir, o bien use `AsEnumerable()`, `ToList()` o una función similar para devolver los datos al cliente de forma explícita, donde después se puedan seguir procesando mediante LINQ to Objects.

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a>Cambios de impacto medio

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core ya no forma parte del marco compartido ASP.NET Core

[Anuncios del problema de seguimiento n.º 325](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de ASP.NET Core 3.0, cuando se agregaba una referencia de paquete a `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, se incluía EF Core y algunos de los proveedores de datos de EF Core, como el de SQL Server.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, el marco compartido ASP.NET Core no incluye EF Core ni ningún proveedor de datos de EF Core.

#### <a name="why"></a>Por qué

Antes de este cambio, para obtener EF Core se necesitaban varios pasos en función de si la aplicación se destinaba a ASP.NET Core y SQL Server o no.
Además, la actualización de ASP.NET Core forzaba la de EF Core y el proveedor de SQL Server, lo que no siempre es deseable.

Con este cambio, la experiencia de obtención de EF Core es la misma en todos los proveedores, implementaciones admitidas de .NET y tipos de aplicación.
Ahora los desarrolladores también pueden controlar exactamente cuándo se actualizan EF Core y los proveedores de datos de EF Core.

#### <a name="mitigations"></a>Mitigaciones

Para usar EF Core en una aplicación ASP.NET Core 3.0 o cualquier otra aplicación compatible, debe agregar de forma explícita una referencia de paquete al proveedor de base de datos de EF Core que se va a usar en la aplicación.

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a>La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core

[Problema de seguimiento n.º 14016](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de 3.0, la herramienta `dotnet ef` se incluía en el SDK de .NET Core y estaba disponible para usarse desde la línea de comandos de cualquier proyecto sin necesidad de realizar pasos adicionales.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, el SDK de .NET no incluye la herramienta `dotnet ef`, por lo que antes de poder usarla tendrá que instalarla de forma explícita como una herramienta local o global.

#### <a name="why"></a>Por qué

Este cambio nos permite distribuir y actualizar `dotnet ef` como una herramienta convencional de la CLI de .NET en NuGet, coherente con el hecho de que la versión 3.0 de EF Core también se distribuye siempre como un paquete NuGet.

#### <a name="mitigations"></a>Mitigaciones

Para poder administrar las migraciones o aplicar scaffolding a `DbContext`, instale `dotnet-ef` como herramienta global:

```dotnetcli
dotnet tool install --global dotnet-ef
```

También se puede obtener una herramienta local cuando se restauran las dependencias de un proyecto que la declara como una dependencia de herramientas mediante un [archivo de manifiesto de herramientas](/dotnet/core/tools/global-tools#install-a-local-tool).

## <a name="low-impact-changes"></a>Cambios de impacto bajo

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre

[Problema de seguimiento n.º 10996](https://github.com/dotnet/efcore/issues/10996)

> [!IMPORTANT]
> `ExecuteSqlCommand` y `ExecuteSqlCommandAsync` están en desuso. En su lugar, use estos métodos.

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, estos nombres de métodos se sobrecargaban para funcionar tanto con una cadena normal como con una cadena que se debería interpolar en SQL y parámetros.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw` y `ExecuteSqlRawAsync` para crear una consulta con parámetros donde los parámetros se pasan por separado de la cadena de consulta.
Por ejemplo:

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

Use `FromSqlInterpolated`, `ExecuteSqlInterpolated` y `ExecuteSqlInterpolatedAsync` para crear una consulta con parámetros donde los parámetros se pasan como parte de una cadena de consulta interpolada.
Por ejemplo:

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

Tenga en cuenta que las dos consultas anteriores producirán el mismo código SQL parametrizado con los mismos parámetros SQL.

#### <a name="why"></a>Por qué

Las sobrecargas del método como esta facilitan las llamadas accidentales al método de cadena sin procesar cuando la intención era llamar al método de cadena interpolada y viceversa.
Esto podría resultar en consultas que no se parametrizan cuando deberían.

#### <a name="mitigations"></a>Mitigaciones

Haga el cambio para usar los nuevos nombres de métodos.

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a>Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar

[Problema de seguimiento n.° 15392](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, el método FromSql intentaba detectar si se podía redactar en el código SQL pasado. Cuando el código SQL no se podía redactar, como un procedimiento almacenado, realizaba la evaluación de cliente. La consulta siguiente funcionaba al ejecutar el procedimiento almacenado en el servidor y aplicar FirstOrDefault en el lado cliente.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, EF Core no intentará analizar el código SQL. Por tanto, si va a redactar después de FromSqlRaw/FromSqlInterpolated, EF Core redactará el código SQL generando una subconsulta. Por tanto, si usa un procedimiento almacenado con la redacción, obtendrá una excepción de sintaxis de SQL no válida.

#### <a name="why"></a>Por qué

EF Core 3.0 no admite la evaluación automática de cliente, ya que era propenso a errores, como se explica [aquí](#linq-queries-are-no-longer-evaluated-on-the-client).

#### <a name="mitigations"></a>Mitigaciones

Si usa un procedimiento almacenado en FromSqlRaw/FromSqlInterpolated, sabe que no se puede redactar, por lo que puede agregar __AsEnumerable/AsAsyncEnumerable__ justo después de la llamada al método FromSql para evitar cualquier redacción en el lado servidor.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a>Solo se pueden especificar métodos de FromSql en raíces de consulta.

[Problema de seguimiento n.° 15704](https://github.com/dotnet/efcore/issues/15704)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, el método `FromSql` podía especificarse en cualquier lugar en la consulta.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, los nuevos métodos `FromSqlRaw` y `FromSqlInterpolated` (que reemplazan a `FromSql`) solo pueden especificarse en las raíces de la consulta, es decir, directamente en `DbSet<>`. Si intenta especificarlos en cualquier otro lugar se producirá un error de compilación.

#### <a name="why"></a>Por qué

La especificación de `FromSql` en cualquier otro lugar diferente de `DbSet` no tenía un significado o valor agregado, y podría causar ambigüedad en ciertos escenarios.

#### <a name="mitigations"></a>Mitigaciones

Las invocaciones de `FromSql` se deben mover para que estén directamente en el `DbSet` al que se aplican.

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a>Las consultas sin seguimiento ya no realizan la resolución de la identidad

[Problema de seguimiento n.º 13518](https://github.com/dotnet/efcore/issues/13518)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, se usaba la misma instancia de la entidad para cada aparición de una entidad con un tipo e identificador determinados. Este comportamiento coincide con el de las consultas de seguimiento. Fijémonos en esta consulta:

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

Esta consulta devolverá la misma instancia de `Category` para cada elemento `Product` asociado con la categoría determinada.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, se crean distintas instancias de la entidad si se encuentra una entidad con un tipo e identificador determinados en varias ubicaciones del gráfico devuelto. Por ejemplo, la consulta anterior ahora devolverá una nueva instancia de `Category` para cada elemento `Product` cuando haya dos productos asociados a la misma categoría.

#### <a name="why"></a>Por qué

La resolución de las identidades (es decir, el hecho de determinar que una entidad tiene los mismos tipo e identificador que la entidad encontrada) agrega más rendimiento y sobrecarga de memoria. Este enfoque suele ser contrario a por qué las consultas sin seguimiento se usan en primer lugar. Además, aunque la resolución de las identidades a veces puede resultar útil, no es necesaria si las entidades se van a serializar y enviar a un cliente, algo habitual para las consultas sin seguimiento.

#### <a name="mitigations"></a>Mitigaciones

Si se requiere la resolución de identidad, use una consulta de seguimiento.

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>Los valores de clave temporal ya no se establecen en instancias de entidad

[Problema de seguimiento n.º 12378](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, los valores temporales se asignaban a todas las propiedades de clave para las que posteriormente la base de datos generaba un valor real.
Normalmente, estos valores temporales eran números negativos grandes.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, en EF Core se almacena el valor de clave temporal como parte de la información de seguimiento de la entidad y la propiedad clave en sí no se modifica.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para evitar que los valores de clave temporales se conviertan erróneamente en permanentes cuando una entidad de la que previamente una instancia de `DbContext` ha realizado el seguimiento se mueve a otra instancia de `DbContext`.

#### <a name="mitigations"></a>Mitigaciones

Las aplicaciones que asignan valores de clave principal a claves externas para crear asociaciones entre entidades pueden depender del comportamiento anterior si las claves principales son generadas por el almacén y pertenecen a entidades en el estado `Added`.
Esto se puede evitar con las siguientes situaciones:

* No se usan claves generadas por el almacén.
* Se establecen propiedades de navegación para crear relaciones en lugar de establecer valores de clave externa.
* Se obtienen los valores de clave temporal reales de la información de seguimiento de la entidad.
Por ejemplo, `context.Entry(blog).Property(e => e.Id).CurrentValue` devolverá el valor temporal aunque no se haya establecido `blog.Id`.

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges respeta los valores de clave generados por el almacén

[Problema de seguimiento n.º 14616](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, se realizaba el seguimiento en el estado `Added` de las entidades sin seguimiento detectadas por `DetectChanges` y se insertaban como una fila nueva cuando se llamaba a `SaveChanges`.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, si una entidad usa valores de clave generados y se establece un valor de clave, se realizará el seguimiento de la entidad en el estado `Modified`.
Esto significa que se supone que existe una fila para la entidad y que se actualizará cuando se llame a `SaveChanges`.
Si no se establece el valor de clave, o bien si el tipo de entidad no usa claves generadas, se seguirá realizando el seguimiento de la entidad nueva como `Added` al igual que en las versiones anteriores.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para que sea más sencillo y coherente trabajar con gráficos de entidades desconectadas mientras se usan claves generadas por el almacén.

#### <a name="mitigations"></a>Mitigaciones

Este cambio puede interrumpir una aplicación si se configura un tipo de entidad para usar claves generadas, pero se establecen de forma explícita valores de clave para las instancias nuevas.
La solución consiste en configurar de forma explícita las propiedades de clave para que no usen valores generados.
Por ejemplo, con la API fluida:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

O bien con anotaciones de datos:

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a>Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada

[Problema de seguimiento n.º 10114](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de la versión 3.0, en EF Core no se aplicaban acciones en cascada (eliminación de entidades dependientes cuando se eliminaba una entidad de seguridad obligatoria o cuando se rompía la relación con una entidad de seguridad obligatoria) hasta que se llamaba a SaveChanges.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de 3.0, en EF Core las acciones en cascada se aplican en cuanto se detecta la condición desencadenadora.
Por ejemplo, como resultado de la llamada a `context.Remove()` para eliminar una entidad de seguridad, todos los dependientes obligatorios relacionados de los que se realiza el seguimiento también se establecen en `Deleted` inmediatamente.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para mejorar la experiencia en escenarios de auditoría y enlace de datos, donde es importante comprender qué entidades se van a eliminar _antes de llamar a_  `SaveChanges`.

#### <a name="mitigations"></a>Mitigaciones

El comportamiento anterior se puede restaurar mediante opciones de `context.ChangeTracker`.
Por ejemplo:

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a>La carga diligente de entidades relacionadas ahora se realiza en una sola consulta

[Problema de seguimiento n.º 18022](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de la versión 3.0, la carga diligente de navegaciones de colección a través de operadores `Include` provocaba la generación de varias consultas en la base de datos relacional, una para cada tipo de entidad relacionada.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, EF Core genera una sola consulta con operadores JOIN en las bases de datos relacionales.

#### <a name="why"></a>Por qué

La emisión de varias consultas para implementar una única consulta LINQ provocaba numerosos problemas, incluido el rendimiento negativo, ya que se necesitaban varios recorridos de ida y vuelta a la base de datos, y problemas de coherencia de datos, ya que cada consulta podía observar un estado distinto de la base de datos.

#### <a name="mitigations"></a>Mitigaciones

Aunque técnicamente esto no es un cambio importante, podría tener un efecto considerable en el rendimiento de la aplicación cuando una sola consulta contiene un gran número de operadores `Include` en las navegaciones de la colección. [Vea este comentario](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) para obtener más información y para volver a escribir las consultas de una manera más eficaz.

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a>DeleteBehavior.Restrict tiene una semántica más limpia

[Problema de seguimiento n.º 12661](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de la versión 3.0, `DeleteBehavior.Restrict` creaba claves externas en la base de datos con la semántica `Restrict`, pero también realizaba una corrección interna de manera no evidente.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, `DeleteBehavior.Restrict` garantiza que las claves externas se crean con la semántica `Restrict`, es decir, sin cascadas y realizando una infracción de restricción, sin llevar a cabo correcciones internas de EF.

#### <a name="why"></a>Por qué

Este cambio se realizó para mejorar la experiencia de uso de `DeleteBehavior` de manera intuitiva sin efectos secundarios inesperados.

#### <a name="mitigations"></a>Mitigaciones

El comportamiento anterior se puede restaurar con `DeleteBehavior.ClientNoAction`.

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a>Los tipos de consulta se consolidan con tipos de entidad

[Problema de seguimiento n.º 14194](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, los [tipos de consulta](xref:core/modeling/keyless-entity-types) eran un medio para consultar los datos que no definen una clave principal de una manera estructurada.
Es decir, un tipo de consulta se usaba para asignar tipos de entidad sin claves (más probablemente desde una vista, pero posiblemente desde una tabla), mientras que un tipo de entidad estándar se usaba cuando había una clave disponible (más probablemente desde una tabla, pero posiblemente desde una vista).

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora un tipo de consulta se convierte en un tipo de entidad sin una clave principal.
Los tipos de entidad sin clave tienen la misma funcionalidad que los tipos de consulta de las versiones anteriores.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para reducir la confusión en torno a la finalidad de los tipos de consulta.
En concreto, son tipos de entidad sin clave y, por ello, intrínsecamente son de solo lectura, pero no se deben usar solo porque un tipo de entidad tenga que ser de solo lectura.
Del mismo modo, se suelen asignar a vistas, pero solo porque las vistas no suelen definir claves.

#### <a name="mitigations"></a>Mitigaciones

Los elementos siguientes de la API ahora están obsoletos:

* **`ModelBuilder.Query<>()`** : en su lugar es necesario llamar a `ModelBuilder.Entity<>().HasNoKey()` para marcar un tipo de entidad como sin claves.
Esto todavía no se configurará por convención para evitar una configuración incorrecta cuando se espera una clave principal, pero no coincide con la convención.
* **`DbQuery<>`** : en su lugar se debe usar `DbSet<>`.
* **`DbContext.Query<>()`** : en su lugar se debe usar `DbContext.Set<>()`.
* **`IQueryTypeConfiguration<TQuery>`** : en su lugar se debe usar `IEntityTypeConfiguration<TEntity>`.

> [!NOTE]
> Debido a [un problema en la versión 3.x](https://github.com/dotnet/efcore/issues/19537), al consultar entidades sin clave que tienen todas las propiedades establecidas en `null` se devolverá un `null` en lugar de una entidad, si este problema es aplicable a su escenario, agregue también lógica para administrar `null` en los resultados.

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a>La API de configuración para las relaciones de tipo de propiedad ha cambiado

[Problema de seguimiento n.º 12444](https://github.com/dotnet/efcore/issues/12444)
[Problema de seguimiento n.º 9148](https://github.com/dotnet/efcore/issues/9148)
[Problema de seguimiento n.º 14153](https://github.com/dotnet/efcore/issues/14153)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, la configuración de la relación de propiedad se realizaba directamente después de la llamada a `OwnsOne` o `OwnsMany`.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, ahora hay una API fluida para configurar una propiedad de navegación para el propietario mediante `WithOwner()`.
Por ejemplo:

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

La configuración relacionada con la relación entre el propietario y lo que se posee ahora se debe encadenar después de `WithOwner()`, de forma similar a cómo se configuran otras relaciones.
Pero la configuración del propio tipo de propiedad se seguirá encadenando después de `OwnsOne()/OwnsMany()`.
Por ejemplo:

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");

        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

Además, la llamada a `Entity()`, `HasOne()` o `Set()` con un tipo de propiedad de destino ahora iniciará una excepción.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para crear una separación más clara entre la configuración del propio tipo de propiedad y la _relación con_ el tipo de propiedad.
A su vez, esto elimina la ambigüedad y la confusión de métodos como `HasForeignKey`.

#### <a name="mitigations"></a>Mitigaciones

Cambie la configuración de las relaciones de tipo de propiedad para usar la nueva superficie de API, como se muestra en el ejemplo anterior.

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales

[Problema de seguimiento n.º 9005](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a>Comportamiento anterior

Considere el modelo siguiente:

```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, siempre era necesaria una instancia de `OrderDetails` al agregar un elemento `Order` nuevo.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, EF Core permite agregar `Order` sin `OrderDetails` y asigna todas las propiedades `OrderDetails` a excepción de la clave principal a columnas que aceptan valores NULL.
Al realizar consultas, EF Core establece `OrderDetails` en `null` si ninguna de las propiedades necesarias tiene un valor o si no tiene propiedades necesarias más allá de la clave principal y todas las propiedades son `null`.

#### <a name="mitigations"></a>Mitigaciones

Si el modelo tiene una tabla que comparte dependencias con todas las columnas opcionales, pero la navegación que apunta a ella no se espera que sea `null`, la aplicación debería modificarse para controlar los casos en los que la navegación sea `null`. Si esto no es posible, debería agregarse una propiedad necesaria al tipo de entidad o, al menos, una entidad debería tener un valor distinto a `null` asignado.

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad

[Problema de seguimiento n.º 14154](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a>Comportamiento anterior

Considere el modelo siguiente:

```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```

Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, si solo se actualizaba `OrderDetails`, no se actualizaba el valor `Version` en el cliente y se producía un error en la próxima actualización.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, EF Core propaga el nuevo valor `Version` en `Order` si posee `OrderDetails`. En caso contrario, se produce una excepción durante la validación del modelo.

#### <a name="why"></a>Por qué

Este cambio se realizó para evitar un valor de token de simultaneidad obsoleto cuando solo se actualiza una de las entidades asignadas a la misma tabla.

#### <a name="mitigations"></a>Mitigaciones

Todas las entidades que comparten la tabla deben incluir una propiedad que se asigna a la columna del token de simultaneidad. Es posible crear una en estado reemplazado:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a>Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento

[Problema de seguimiento n.º 18876](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, las entidades en propiedad se podían consultar como cualquier otra navegación.

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, EF Core iniciará una excepción si una consulta de seguimiento proyecta una entidad en propiedad sin el propietario.

#### <a name="why"></a>Por qué

Las entidades en propiedad no se pueden manipular sin el propietario, por lo que en la mayoría de los casos es un error consultarlas de esta manera.

#### <a name="mitigations"></a>Mitigaciones

Si se debe realizar el seguimiento de la entidad en propiedad para modificarla de cualquier manera posterior, el propietario se debe incluir en la consulta.

De lo contrario, agregue una llamada a `AsNoTracking()`:

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados

[Problema de seguimiento n.º 13998](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a>Comportamiento anterior

Considere el modelo siguiente:

```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

Antes de EF Core 3.0, la propiedad `ShippingAddress` se asignaba a columnas distintas para `BulkOrder` y `Order` de forma predeterminada.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión3.0, EF Core solo crea una columna para `ShippingAddress`.

#### <a name="why"></a>Por qué

El comportamiento anterior no era el esperado.

#### <a name="mitigations"></a>Mitigaciones

Todavía se puede asignar explícitamente la propiedad a columnas separadas en los tipos derivados:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad

[Problema de seguimiento n.º 13274](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a>Comportamiento anterior

Considere el modelo siguiente:

```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

Antes de EF Core 3.0, se podía usar la propiedad `CustomerId` para la clave externa por convención.
Pero si `Order` es un tipo de propiedad, entonces esto convertiría también a `CustomerId` en la clave principal, algo que no suele ser lo esperado.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, EF Core no intenta usar las propiedades de claves externas por convención si tienen el mismo nombre que la propiedad de entidad de seguridad.
Los patrones de nombre de tipo de entidad de seguridad concatenado con el nombre de propiedad de la entidad de seguridad y de nombre de navegación concatenado con el nombre de propiedad de la entidad de seguridad todavía se hacen coincidir.
Por ejemplo:

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

#### <a name="why"></a>Por qué

Este cambio se ha realizado para evitar definir erróneamente una propiedad de clave principal en el tipo de propiedad.

#### <a name="mitigations"></a>Mitigaciones

Si la propiedad se ha diseñado para ser la clave externa y, por tanto, parte de la clave principal, se debe configurar explícitamente como tal.

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope

[Problema de seguimiento n.º 14218](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, si el contexto abría la conexión dentro de `TransactionScope`, la conexión permanecía abierta mientras el ámbito actual `TransactionScope` estuviese activo.

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de la versión 3.0, EF Core cierra la conexión en cuanto se deja de usar.

#### <a name="why"></a>Por qué

Este cambio permite usar varios contextos en el mismo ámbito `TransactionScope`. El comportamiento nuevo también coincide con el de EF6.

#### <a name="mitigations"></a>Mitigaciones

Si la conexión debe permanecer abierta, una llamada explícita a `OpenConnection()` asegurará que EF Core no la cierre de forma prematura:

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>Cada propiedad usa la generación de claves enteras en memoria independiente

[Problema de seguimiento n.º 6872](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, se usaba un generador de valores compartidos para todas las propiedades de clave entera en memoria.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, cada propiedad de clave entera obtiene su propio generador de valores cuando se usa la base de datos en memoria.
Además, si se elimina la base de datos, la generación de claves se restablece para todas las tablas.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para alinear la generación de claves en memoria más estrechamente a la generación de claves de base de datos reales y para mejorar la capacidad para aislar las pruebas entre sí cuando se usa la base de datos en memoria.

#### <a name="mitigations"></a>Mitigaciones

Esto puede interrumpir una aplicación que se base en el establecimiento de valores de clave específicos en memoria.
En su lugar, considere la posibilidad de no depender de valores de clave específicos, o bien de actualizar para que coincida con el comportamiento nuevo.

### <a name="backing-fields-are-used-by-default"></a>Los campos de respaldo se usan de forma predeterminada

[Problema de seguimiento n.º 12430](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de la versión 3.0, incluso si se conocía el campo de respaldo de una propiedad, de forma predeterminada en EF Core se leía y escribía el valor de propiedad mediante los métodos captadores y establecedores de propiedades.
La excepción era la ejecución de consultas, donde el campo de respaldo se establecía directamente si se conocía.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, si se conoce el campo de respaldo para una propiedad, EF Core siempre la leerá y escribirá mediante el campo de respaldo.
Esto podría provocar una interrupción de la aplicación si depende de un comportamiento adicional codificado en los métodos captadores o establecedores.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para evitar que EF Core desencadene erróneamente lógica de negocios de forma predeterminada al realizar operaciones de base de datos que implican entidades.

#### <a name="mitigations"></a>Mitigaciones

El comportamiento anterior a la versión 3.0 se puede restaurar mediante la configuración del modo de acceso de propiedad en `ModelBuilder`.
Por ejemplo:

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>Inicio de excepciones si se encuentran varios campos de respaldo compatibles

[Problema de seguimiento n.º 12523](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, si varios campos coincidían con las reglas para buscar el campo de respaldo de una propiedad, se elegía un campo según un orden de prioridad.
Esto podía producir que, en caso de ambigüedad, se usara el campo incorrecto.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, si varios campos coinciden con la misma propiedad, se inicia una excepción.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para evitar de forma silenciosa el uso de un campo con respecto a otro cuando solo uno puede ser correcto.

#### <a name="mitigations"></a>Mitigaciones

En las propiedades con campos de respaldo ambiguos se debe especificar de forma explícita el campo que se va usar.
Por ejemplo, con la API fluida:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a>Los nombres de propiedades de solo campo deben coincidir con el nombre del campo

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, una propiedad podía especificarse con un valor de cadena y, si no había ninguna propiedad con ese nombre en el tipo .NET, EF Core intentaba hacerla coincidir con un campo mediante reglas de convención.

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, una propiedad de solo campo debe coincidir exactamente con el nombre del campo.

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a>Por qué

Este cambio se realizó para evitar el uso del mismo campo para dos propiedades con nombres similares. También hace que las reglas de coincidencia para propiedades solo de campo sean las mismas que para las propiedades asignadas a propiedades CLR.

#### <a name="mitigations"></a>Mitigaciones

Las propiedades solo de campo deberían tener el mismo nombre que el campo al que están asignadas.
En una próxima versión de EF Core 3.0 tenemos planeado volver a habilitar la configuración explícita de un nombre de campo distinto al nombre de la propiedad (vea el problema [n.° 15307](https://github.com/dotnet/efcore/issues/15307)):

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache

[Problema de seguimiento n.º 14756](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, la llamada a `AddDbContext` o `AddDbContextPool` también podría registrar los servicios de almacenamiento en caché y de registro con inserción de dependencias a través de llamadas a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) y [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, `AddDbContext` y `AddDbContextPool` ya no registrarán estos servicios con inserción de dependencias (DI).

#### <a name="why"></a>Por qué

EF Core 3.0 no requiere que estos servicios estén en el contenedor de inserción de dependencias de la aplicación. Pero si `ILoggerFactory` se registra en el contenedor de DI de la aplicación, EF Core lo empezará a usar de todos modos.

#### <a name="mitigations"></a>Mitigaciones

Si la aplicación necesita estos servicios, regístrelos de manera explícita con el contenedor de DI mediante [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a>AddEntityFramework* agrega IMemoryCache con un límite de tamaño

[Problema de seguimiento n.º 12905](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, la llamada a los métodos `AddEntityFramework*` también registraba los servicios de almacenamiento en caché de memoria con inserción de dependencias sin límite de tamaño.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, `AddEntityFramework*` registrará un servicio IMemoryCache con un límite de tamaño. Si otros servicios agregados después dependen de IMemoryCache, pueden alcanzar rápidamente el límite predeterminado y provocar excepciones o un rendimiento degradado.

#### <a name="why"></a>Por qué

El uso de IMemoryCache sin un límite podría dar lugar a un uso de memoria no controlado si hay un error en la lógica de almacenamiento en caché de las consultas o las consultas se generan de forma dinámica. Tener un límite predeterminado mitiga un posible ataque DoS.

#### <a name="mitigations"></a>Mitigaciones

En la mayoría de los casos, no es necesario llamar a `AddEntityFramework*` si también se llama a `AddDbContext` o `AddDbContextPool`. Por tanto, la mejor mitigación consiste en quitar la llamada a `AddEntityFramework*`.

Si la aplicación necesita estos servicios, registre de forma explícita una implementación de IMemoryCache con el contenedor de DI por anticipado mediante [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>Ahora DbContext.Entry realiza una operación DetectChanges local

[Problema de seguimiento n.º 13552](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, la llamada a `DbContext.Entry` provocaba que se detectaran cambios para todas las entidades con seguimiento.
Esto garantizaba que el estado expuesto en `EntityEntry` estuviera actualizado.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, ahora la llamada a `DbContext.Entry` solo intenta detectar cambios en la entidad dada y cualquier entidad de seguridad relacionada con ella de la que se haya realizado el seguimiento.
Esto significa que es posible que la llamada a este método no haya detectado otros cambios, lo que podría tener implicaciones en el estado de la aplicación.

Observe que si `ChangeTracker.AutoDetectChangesEnabled` se establece en `false` incluso esta detección de cambios local se deshabilitará.

Otros métodos que provocan la detección de cambios (como `ChangeTracker.Entries` y `SaveChanges`) siguen provocando una acción `DetectChanges` completa de todas las entidades de las que se realiza el seguimiento.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para mejorar el rendimiento predeterminado del uso de `context.Entry`.

#### <a name="mitigations"></a>Mitigaciones

Llame a `ChangeTracker.DetectChanges()` de forma explícita antes de llamar a `Entry` para garantizar el comportamiento anterior a la versión 3.0.

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>El cliente no genera las claves de matriz de cadena y byte de forma predeterminada

[Problema de seguimiento n.º 14617](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, se podían usar las propiedades de clave `string` y `byte[]` sin tener que establecer de forma explícita un valor distinto de NULL.
En ese caso, el valor de clave se generaba en el cliente como un GUID, que se serializaba en bytes para `byte[]`.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, se iniciará una excepción en la que indica que no se ha establecido ningún valor de clave.

#### <a name="why"></a>Por qué

Este cambio se ha realizado porque los valores `string`/`byte[]` generados por el cliente no suelen ser útiles, y el comportamiento predeterminado dificultaba razonar sobre los valores de clave generados de una forma habitual.

#### <a name="mitigations"></a>Mitigaciones

Se puede obtener el comportamiento anterior a la versión 3.0 si se especifica de forma explícita que las propiedades de clave deben usar los valores generados si no se establece ningún otro valor distinto de NULL.
Por ejemplo, con la API fluida:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

O bien con anotaciones de datos:

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a>Ahora ILoggerFactory es un servicio con ámbito

[Problema de seguimiento n.º 14698](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, `ILoggerFactory` se registraba como un servicio de singleton.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, `ILoggerFactory` ahora se registra como con ámbito.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para permitir la asociación de un registrador con una instancia de `DbContext`, lo que habilita otras funciones y quita algunos casos de comportamiento patológico como un aumento vertiginoso de los proveedores de servicios internos.

#### <a name="mitigations"></a>Mitigaciones

Este cambio no debería afectar al código de la aplicación a menos que registre y use servicios personalizados en el proveedor de servicios internos de EF Core.
Esto no es habitual.
En estos casos, la mayoría de los elementos seguirá funcionando, pero cualquier servicio de singleton que dependiera de `ILoggerFactory` tendrá que cambiarse para obtener la interfaz `ILoggerFactory` de otra forma.

Si experimenta situaciones como esta, registre un problema en el [rastreador de problemas de GitHub de EF Core](https://github.com/dotnet/efcore/issues) para hacernos saber cómo usa `ILoggerFactory`, para que podamos comprender mejor cómo evitar esta interrupción en el futuro.

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas

[Problema de seguimiento n.º 12780](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, una vez que se eliminaba `DbContext` no había ninguna forma de saber si una determinada propiedad de navegación de una entidad obtenida de ese contexto se había cargado completamente o no.
En su lugar, los proxies asumían que una navegación de referencia se cargaba si tenía un valor distinto de NULL, y que una navegación de colección se cargaba si no estaba vacía.
En estos casos, el intento de carga diferida era no operativo.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, los proxies realizan el seguimiento de si una propiedad de navegación se carga o no.
Esto significa que el intento de acceder a una propiedad de navegación que se carga después de que se haya eliminado el contexto siempre será no operativo, incluso cuando la navegación cargada está vacía o es NULL.
Por el contrario, el intento de acceder a una propiedad de navegación que no está cargada iniciará una excepción si el contexto se ha eliminado, incluso si la propiedad de navegación es una colección no vacía.
Si se produce esta situación, significa que el código de aplicación está intentando usar la carga diferida en un momento no válido y que se debe cambiar la aplicación para que lo no haga.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para que el comportamiento sea coherente y correcto cuando se intenta la carga diferida de una instancia de `DbContext` eliminada.

#### <a name="mitigations"></a>Mitigaciones

Actualice el código de la aplicación para que no intente la carga diferida con un contexto eliminado, o bien configúrelo para que sea no operativo, como se describe en el mensaje de la excepción.

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada

[Problema de seguimiento n.º 10236](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, se registraba una advertencia para una aplicación que creaba un número patológico de proveedores de servicios internos.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, ahora esta advertencia se considera un error y se inicia una excepción.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para controlar mejor el código de la aplicación mediante la exposición de este caso patológico de una forma más explícita.

#### <a name="mitigations"></a>Mitigaciones

Cuando se produce este error, la acción más adecuada consiste en comprender la causa raíz y detener la creación de tantos proveedores de servicios internos.
Pero el error se puede convertir en una advertencia (u omitirse) mediante configuración en `DbContextOptionsBuilder`.
Por ejemplo:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena

[Problema de seguimiento n.º 9171](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, el código para llamar a `HasOne` o `HasMany` con una cadena se interpretaba de manera confusa.
Por ejemplo:

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

El código parece relacionar `Samurai` con otro tipo de entidad mediante la propiedad de navegación `Entrance`, que puede ser privada.

En realidad, este código intenta crear una relación con algún tipo de entidad denominada `Entrance` sin ninguna propiedad de navegación.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, el código anterior ahora hace lo que parecía que debía hacer antes.

#### <a name="why"></a>Por qué

El comportamiento anterior era muy confuso, especialmente al leer el código de configuración y al buscar errores.

#### <a name="mitigations"></a>Mitigaciones

Esto solo interrumpirá las aplicaciones que configuran de manera explícita las relaciones con cadenas para nombres de tipos y sin especificar explícitamente la propiedad de navegación.
Esto no es habitual.
El comportamiento anterior se puede obtener al pasar de manera explícita `null` para el nombre de la propiedad de navegación.
Por ejemplo:

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask

[Problema de seguimiento n.º 15184](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes, los siguientes métodos asincrónicos devolvían `Task<T>`:

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` (y las clases derivadas)

#### <a name="new-behavior"></a>Comportamiento nuevo

Dichos métodos ahora devuelven `ValueTask<T>` durante el mismo `T` que antes.

#### <a name="why"></a>Por qué

Este cambio reduce el número de asignaciones de montones que se producen al invocar estos métodos, lo que mejora el rendimiento general.

#### <a name="mitigations"></a>Mitigaciones

Las aplicaciones que simplemente esperen las API anteriores solo necesitan recompilarse, sin que sea necesario realizar cambios en el código fuente.
Un uso más complejo (p. ej., pasar el valor `Task` devuelto a `Task.WhenAny()`) normalmente requiere que el valor `ValueTask<T>` devuelto se convierta en `Task<T>` mediante una llamada a `AsTask()` en él.
Tenga en cuenta que esto niega la reducción de asignación que implica este cambio.

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>La anotación Relational:TypeMapping ahora es simplemente TypeMapping

[Problema de seguimiento n.º 9913](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a>Comportamiento anterior

El nombre de anotación para las anotaciones de asignación de tipos era "Relational:TypeMapping".

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora, el nombre de anotación para las anotaciones de asignación de tipos es "TypeMapping".

#### <a name="why"></a>Por qué

Ahora, las asignaciones de tipos se usan para algo más que solo para proveedores de bases de datos relacionales.

#### <a name="mitigations"></a>Mitigaciones

Esto solo interrumpirá a las aplicaciones que acceden directamente a la asignación de tipos como una anotación, lo que no es habitual.
La acción más apropiada para corregir es usar la superficie de API para acceder a las asignaciones de tipos en lugar de usar directamente la anotación.

### <a name="totable-on-a-derived-type-throws-an-exception"></a>ToTable en un tipo derivado inicia una excepción

[Problema de seguimiento n.º 11811](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, la llamada a `ToTable()` en un tipo derivado se omitía, ya que la única estrategia asignación de herencia era TPH, lo que no es válido.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, y en preparación para agregar compatibilidad con TPT y TPC en una versión posterior, ahora la llamada a `ToTable()` en un tipo derivado iniciará una excepción para evitar un cambio de asignación inesperado en el futuro.

#### <a name="why"></a>Por qué

En la actualidad no se considera válido asignar un tipo derivado a otra tabla.
Este cambio evita interrupciones en el futuro, cuando se convierta en una operación válida.

#### <a name="mitigations"></a>Mitigaciones

Quite todos los intentos de asignar tipos derivados a otras tablas.

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex se ha reemplazado por HasIndex

[Problema de seguimiento n.º 12366](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` proporcionaba una manera de configurar las columnas que se usaban con `INCLUDE`.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, ya se admite el uso de `Include` en un índice en el nivel relacional.
Use `HasIndex().ForSqlServerInclude()`.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para consolidar la API para índices con `Include` en un mismo lugar para todos los proveedores de base de datos.

#### <a name="mitigations"></a>Mitigaciones

Use la API nueva, como se ha mostrado anteriormente.

### <a name="metadata-api-changes"></a>Cambios en la API de metadatos

[Problema de seguimiento n.º 214](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a>Comportamiento nuevo

Las siguientes propiedades se han convertido en métodos de extensión:

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a>Por qué

Este cambio simplifica la implementación de las interfaces mencionadas anteriormente.

#### <a name="mitigations"></a>Mitigaciones

Use los nuevos métodos de extensión.

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a>Cambios en la API de metadatos específicos del proveedor

[Problema de seguimiento n.º 214](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a>Comportamiento nuevo

Los métodos de extensión específicos del proveedor se simplificarán:

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a>Por qué

Este cambio simplifica la implementación de los métodos de extensión mencionados anteriormente.

#### <a name="mitigations"></a>Mitigaciones

Use los nuevos métodos de extensión.

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core ya no envía pragma para el cumplimiento de SQLite FK

[Problema de seguimiento n.º 12151](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, EF Core enviaba `PRAGMA foreign_keys = 1` cuando se abría una conexión con SQLite.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, EF Core ya no envía `PRAGMA foreign_keys = 1` cuando se abre una conexión con SQLite.

#### <a name="why"></a>Por qué

Este cambio se ha realizado porque en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3` de forma predeterminada, lo que a su vez significa que el cumplimiento de CD está activado de forma predeterminada y no es necesario habilitarlo explícitamente cada vez que se abra una conexión.

#### <a name="mitigations"></a>Mitigaciones

Las claves externas se habilitan de forma predeterminada en SQLitePCLRaw.bundle_e_sqlite3, que en EF Core se usa de forma predeterminada.
Para otros casos, las claves externas se pueden habilitar mediante la especificación de `Foreign Keys=True` en la cadena de conexión.

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, en EF Core se usaba `SQLitePCLRaw.bundle_green`.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3`.

#### <a name="why"></a>Por qué

Este cambio se ha realizado para que la versión de SQLite que se usa en iOS sea coherente con otras plataformas.

#### <a name="mitigations"></a>Mitigaciones

Para usar la versión nativa de SQLite en iOS, configure `Microsoft.Data.Sqlite` para usar otra agrupación `SQLitePCLRaw`.

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>Almacenamiento de valores GUID como TEXT en SQLite

[Problema de seguimiento n.º 15078](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes, los valores GUID se almacenaban como valores BLOB en SQLite.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora, los valores GUID se almacenan como TEXT.

#### <a name="why"></a>Por qué

El formato binario de los GUID no está normalizado. El almacenamiento de los valores como TEXT mejora la compatibilidad de la base de datos con otras tecnologías.

#### <a name="mitigations"></a>Mitigaciones

Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.

```sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite sigue siendo capaz de leer valores GUID de ambas columnas BLOB y TEXT. Sin embargo, dado que el formato predeterminado de los parámetros y las constantes ha cambiado, seguramente deberá realizar alguna acción en la mayoría de casos que impliquen el uso de valores GUID.

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Ahora los valores char se almacenan como TEXT en SQLite

[Problema de seguimiento n.º 15020](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a>Comportamiento anterior

Anteriormente los valores char se almacenaban como valores INTEGER en SQLite. Por ejemplo, un valor char de *A* se almacenaba como el valor entero 65.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora, los valores char se almacenan como TEXT.

#### <a name="why"></a>Por qué

El almacenamiento de valores como TEXT es más natural y mejora la compatibilidad de la base de datos con otras tecnologías.

#### <a name="mitigations"></a>Mitigaciones

Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite también puede leer valores de caracteres tanto de columnas INTEGER como de columnas TEXT, por lo que es posible que no deba hacer nada dependiendo de su caso.

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable

[Problema de seguimiento n.º 12978](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a>Comportamiento anterior

Los identificadores de migración se generaban de forma involuntaria con el calendario de la referencia cultural actual.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora los id. de migración siempre se generan usando el calendario de la referencia cultural invariable (gregoriano).

#### <a name="why"></a>Por qué

El orden de las migraciones es importante al actualizar la base de datos o al solucionar conflictos de combinación. Al usar el calendario invariable, se evitan problemas de ordenación que pueden producirse si los miembros del equipo tienen distintos calendarios del sistema.

#### <a name="mitigations"></a>Mitigaciones

Esta cambio afecta a todas las personas que usan un calendario no gregoriano en el que el año sea superior al del calendario gregoriano (como el calendario budista tailandés). Los id. de migración existentes deberán actualizarse para que las migraciones nuevas se ordenen después de las existentes.

Puede ver el id. de migración en el atributo Migration de los archivos de diseñador de la migración.

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

También debe actualizarse la tabla de historial de migraciones.

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a>Se ha quitado el elemento UseRowNumberForPaging

[Problema de seguimiento n.º 16400](https://github.com/dotnet/efcore/issues/16400)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, `UseRowNumberForPaging` se podía usar para generar SQL para la paginación de forma que fuera compatible con SQL Server 2008.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, EF solo genera SQL para la paginación que únicamente es compatible con las versiones posteriores de SQL Server.

#### <a name="why"></a>Por qué

El motivo de este cambio es que [SQL Server 2008 ya no se admite](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/). Además, la actualización de esta característica para que funcionase con los cambios en las consultas implementados en EF Core 3.0 llevaría mucho trabajo.

#### <a name="mitigations"></a>Mitigaciones

Se recomienda actualizar a una versión más reciente de SQL Server, o bien utilizar un nivel de compatibilidad superior, de modo que el SQL que se genere se admita. Dicho esto, si no puede hacerlo, [escriba un comentario en el problema de seguimiento](https://github.com/dotnet/efcore/issues/16400) con los detalles al respecto. En función de los comentarios, es posible que volvamos a valorar esta decisión.

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a>La información o metadatos de la extensión se han quitado de IDbContextOptionsExtension

[Problema de seguimiento n.º 16119](https://github.com/dotnet/efcore/issues/16119)

#### <a name="old-behavior"></a>Comportamiento anterior

`IDbContextOptionsExtension` incluía métodos para proporcionar metadatos sobre la extensión.

#### <a name="new-behavior"></a>Comportamiento nuevo

Estos métodos se han movido a una nueva clase base abstracta `DbContextOptionsExtensionInfo`, que se devuelve desde una nueva propiedad `IDbContextOptionsExtension.Info`.

#### <a name="why"></a>Por qué

Al lanzarse las versiones 2.0 y 3.0, tuvimos que agregar o cambiar estos métodos varias veces.
Su división en una nueva clase base abstracta facilitará la realización de este tipo de cambios sin interrumpir las extensiones existentes.

#### <a name="mitigations"></a>Mitigaciones

Actualice las extensiones para seguir el nuevo patrón.
Encontrará ejemplos en las muchas implementaciones de `IDbContextOptionsExtension` para los diferentes tipos de extensiones en el código fuente de EF Core.

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>Cambio de nombre de LogQueryPossibleExceptionWithAggregateOperator

[Problema de seguimiento n.º 10985](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a>Change

Se ha cambiado el nombre de `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.

#### <a name="why"></a>Por qué

Conviene alinear el nombre de este evento de advertencia con el del resto de eventos de advertencia.

#### <a name="mitigations"></a>Mitigaciones

Use el nuevo nombre. (Tenga en cuenta que el número de id. evento sigue siendo el mismo).

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a>Clarificación de la API para nombres de restricciones de claves externas

[Problema de seguimiento n.º 10730](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, se utilizaba simplemente el término "nombre" para hacer referencia a los nombres de las restricciones de claves externas. Por ejemplo:

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 3.0, el término con el que se hace referencia a los nombres de las restricciones de claves externas es "nombre de la restricción". Por ejemplo:

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a>Por qué

Este cambio permite mejorar la coherencia relativa a la nomenclatura en este aspecto y aclarar que se trata del nombre de una restricción de clave externa, y no del de la columna o propiedad en la que está definida la clave externa.

#### <a name="mitigations"></a>Mitigaciones

Use el nuevo nombre.

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a>IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos

[Problema de seguimiento n.° 15997](https://github.com/dotnet/efcore/issues/15997)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, estos métodos estaban protegidos.

#### <a name="new-behavior"></a>Comportamiento nuevo

Desde EF Core 3.0, estos métodos son públicos.

#### <a name="why"></a>Por qué

EF usa estos métodos para determinar si se ha creado una base de datos, pero está vacía. Esto también puede resultar útil fuera de EF al determinar si se deben aplicar migraciones o no.

#### <a name="mitigations"></a>Mitigaciones

Cambie la accesibilidad de cualquier invalidación.

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a>Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency

[Problema de seguimiento n.° 11506](https://github.com/dotnet/efcore/issues/11506)

#### <a name="old-behavior"></a>Comportamiento anterior

Antes de EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un paquete NuGet regular con un ensamblado al que podían hacer referencia los proyectos que dependían de él.

#### <a name="new-behavior"></a>Comportamiento nuevo

Desde EF Core 3.0, es un paquete DevelopmentDependency. Esto significa que la dependencia no fluirá de manera transitiva en otros proyectos y que ya no puede, de forma predeterminada, hacer referencia a su ensamblado.

#### <a name="why"></a>Por qué

Este paquete solo está destinado a usarse en tiempo de diseño. Las aplicaciones implementadas no deben hacer referencia al mismo. Hacer que el paquete sea DevelopmentDependency refuerza esta recomendación.

#### <a name="mitigations"></a>Mitigaciones

Si tiene que hacer referencia a este paquete para invalidar el comportamiento en tiempo de diseño de EF Core, puede actualizar los metadatos de elementos PackageReference del proyecto.

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

Si se hace referencia al paquete de manera transitiva a través de Microsoft.EntityFrameworkCore.Tools, tendrá que agregar una PackageReference explícita al paquete para cambiar sus metadatos. Este tipo de referencia explícita debe agregarse a cualquier proyecto que requiera los tipos de paquete.

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a>SQLitePCL.raw se ha actualizado a la versión 2.0.0

[Problema de seguimiento n.° 14824](https://github.com/dotnet/efcore/issues/14824)

#### <a name="old-behavior"></a>Comportamiento anterior

Microsoft.EntityFrameworkCore.Sqlite dependía anteriormente de la versión 1.1.12 de SQLitePCL.raw.

#### <a name="new-behavior"></a>Comportamiento nuevo

Hemos actualizado nuestro paquete para depender de la versión 2.0.0.

#### <a name="why"></a>Por qué

La versión 2.0.0 de SQLitePCL.raw selecciona .NET Standard 2.0 como destino. Anteriormente seleccionaba .NET Standard 1.1 como destino, que requería el cierre a gran escala de paquetes transitivos para su funcionamiento.

#### <a name="mitigations"></a>Mitigaciones

En la versión 2.0.0 de SQLitePCL.raw se incluyen algunos cambios importantes. Consulte las [notas de la versión](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) para obtener detalles.

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a>NetTopologySuite se actualizó a la versión 2.0.0

[Problema de seguimiento n.° 14825](https://github.com/dotnet/efcore/issues/14825)

#### <a name="old-behavior"></a>Comportamiento anterior

Los paquetes espaciales anteriormente dependían de la versión 1.15.1 de NetTopologySuite.

#### <a name="new-behavior"></a>Comportamiento nuevo

Hemos actualizado nuestro paquete para depender de la versión 2.0.0.

#### <a name="why"></a>Por qué

La versión 2.0.0 de NetTopologySuite pretende resolver varios problemas de usabilidad que encontraron los usuarios de EF Core.

#### <a name="mitigations"></a>Mitigaciones

En la versión 2.0.0 de NetTopologySuite se incluyen algunos cambios importantes. Consulte las [notas de la versión](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) para obtener detalles.

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a>Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient

[Problema de seguimiento n.º 15636](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a>Comportamiento anterior

Microsoft.EntityFrameworkCore.SqlServer dependía anteriormente de la versión System.Data.SqlClient.

#### <a name="new-behavior"></a>Comportamiento nuevo

Hemos actualizado nuestro paquete para que dependa de Microsoft.Data.SqlClient.

#### <a name="why"></a>Por qué

A partir de ahora, Microsoft.Data.SqlClient es el controlador de acceso a datos insignia para SQL Server y System.Data.SqlClient ya no es el centro de desarrollo.
Algunas características importantes, como Always Encrypted, solo están disponibles en Microsoft.Data.SqlClient.

#### <a name="mitigations"></a>Mitigaciones

Si el código toma una dependencia directa en System.Data.SqlClient, debe cambiarla para que haga referencia a Microsoft.Data.SqlClient en su lugar. Dado que los dos paquetes mantienen un grado muy alto de compatibilidad con la API, solo debería ser un paquete simple y un cambio de espacio de nombres.

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a>Se deben configurar varias relaciones de referencia automática ambiguas

[Problema de seguimiento n.º 13573](https://github.com/dotnet/efcore/issues/13573)

#### <a name="old-behavior"></a>Comportamiento anterior

Un tipo de entidad con varias propiedades de navegación unidireccional de referencia automática y claves externas coincidentes se configuró incorrectamente como una única relación. Por ejemplo:

```csharp
public class User
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

#### <a name="new-behavior"></a>Comportamiento nuevo

Este escenario se detecta ahora en la generación del modelo y se produce una excepción que indica que el modelo es ambiguo.

#### <a name="why"></a>Por qué

El modelo resultante era ambiguo, y lo más probable es que sea incorrecto en este caso.

#### <a name="mitigations"></a>Mitigaciones

Utilice la configuración completa de la relación. Por ejemplo:

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();

 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a>DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo

[Problema de seguimiento n.º 12757](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a>Comportamiento anterior

Una función DbFunction configurada con el esquema como una cadena vacía se trataba como una función integrada sin un esquema. Por ejemplo, el código siguiente asignará la función CLR `DatePart` a la función integrada `DATEPART` en SqlServer.

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a>Comportamiento nuevo

Todas las asignaciones de DbFunction se consideran asignadas a funciones definidas por el usuario. Por lo tanto, el valor de cadena vacía colocaría la función dentro del esquema predeterminado del modelo, que podría ser el esquema configurado de forma explícita mediante `modelBuilder.HasDefaultSchema()` de la API fluida o `dbo` en caso contrario.

#### <a name="why"></a>Por qué

Anteriormente, el esquema vacío era una manera de indicar que la función estaba integrada, pero esa lógica solo es aplicable a SqlServer, donde las funciones integradas no pertenecen a ningún esquema.

#### <a name="mitigations"></a>Mitigaciones

Configure la traslación de DbFunction manualmente para asignarla a una función integrada.

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a>~~EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0~~ Revertido

[Problema de seguimiento n.º 15498](https://github.com/dotnet/efcore/issues/15498)

EF Core 3.0 tiene como destino .NET Standard 2.1, que es un cambio importante que excluye las aplicaciones de .NET Framework. En EF Core 3.1 se revirtió esto y ahora tiene como destino .NET Standard 2.0 de nuevo.

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a>~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido

[Problema de seguimiento n.º 14523](https://github.com/dotnet/efcore/issues/14523)

Revertimos este cambio porque la nueva configuración de EF Core 3.0 permite a la aplicación especificar el nivel de registro para cualquier evento. Por ejemplo, para cambiar el registro de SQL a `Debug`, configure el nivel de forma explícita en `OnConfiguring` o `AddDbContext`:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
