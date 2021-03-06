---
title: 'Cambios importantes en EF Core 5.0: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 5.0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 4a463e785edaceaf5dd96164c39e2cc9b5f86de4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128750"
---
# <a name="breaking-changes-in-ef-core-50"></a>Cambios importantes en EF Core 5.0

Es posible que los siguientes cambios de API y comportamiento puedan interrumpir las aplicaciones actuales cuando se actualicen a EF Core 5.0.0.

## <a name="summary"></a>Resumen

| **Cambio importante**                                                                                                                   | **Impacto** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [EF Core 5.0 no admite .NET Framework](#netstandard21)                                                                         | Media     |
| [IProperty.GetColumnName() está obsoleto](#getcolumnname-obsolete)                                                                  | Media     |
| [Los decimales necesitan precisión y escala](#decimals)                                                                            | Media     |
| [El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente](#required-dependent).                                 | Media     |
| [La definición de la consulta se reemplaza por métodos específicos del proveedor](#defining-query)                                                          | Media     |
| [Las consultas no sobrescriben las navegaciones de referencia no nula](#nonnullreferences)                                                   | Media     |
| [ToView() se trata de forma diferente en las migraciones](#toview)                                                                              | Media     |
| [ToTable(null) marca el tipo de entidad como no asignado a una tabla](#totable)                                                              | Media     |
| [Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS](#geometric-sqlite)                                                   | Bajo        |
| [Cosmos: la clave de partición se ha agregado ahora a la clave principal](#cosmos-partition-key)                                                        | Bajo        |
| [Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`](#cosmos-id)                                                                                 | Bajo        |
| [Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números](#cosmos-byte)                                             | Bajo        |
| [Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName](#cosmos-metadata)                                                          | Bajo        |
| [Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado](#non-added-generation) | Bajo        |
| [IMigrationsModelDiffer ahora usa IRelationalModel](#relational-model).                                                                 | Bajo        |
| [Los discriminadores son de solo lectura](#read-only-discriminators).                                                                             | Bajo        |
| [Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory](#no-client-methods)                                              | Bajo        |
| [IndexBuilder.HasName ahora está obsoleto](#index-obsolete)                                                                               | Bajo        |
| [Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa](#pluralizer)                                                 | Bajo        |
| [INavigationBase reemplaza a INavigation en algunas API para admitir la omisión de navegaciones](#inavigationbase)                                     | Bajo        |
| [Ya no se admiten algunas consultas con colecciones correlacionadas que también usan `Distinct` o `GroupBy`](#collection-distinct-groupby) | Bajo        |
| [No se admite el uso de una colección de tipo consultable en la proyección](#queryable-projection)                                          | Bajo        |

## <a name="medium-impact-changes"></a>Cambios de impacto medio

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a>EF Core 5.0 no admite .NET Framework

[Problema de seguimiento n.º 15498](https://github.com/dotnet/efcore/issues/15498)

#### <a name="old-behavior"></a>Comportamiento anterior

EF Core 3.1 tiene como destino .NET Standard 2.0, que es compatible con .NET Framework.

#### <a name="new-behavior"></a>Comportamiento nuevo

EF Core 5.0 tiene como destino .NET Standard 2.1, que no es compatible con .NET Framework. Esto significa que EF Core 5.0 no se puede usar con aplicaciones de .NET Framework.

#### <a name="why"></a>Por qué

Esto forma parte de un mayor movimiento entre los equipos de .NET destinado a la unificación en una sola plataforma .NET de destino. Para obtener más información, vea [El futuro de .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).

#### <a name="mitigations"></a>Mitigaciones

Las aplicaciones de .NET Framework pueden seguir usando EF Core 3.1, que es una [versión de soporte a largo plazo (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Como alternativa, las aplicaciones se pueden actualizar para que usen .NET Core 2.1, .NET Core 3.1 o .NET 5, que admiten .NET Standard 2.1.

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a>IProperty.GetColumnName() está obsoleto

[Incidencia de seguimiento n.º 2266](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a>Comportamiento anterior

`GetColumnName()` devolvía el nombre de la columna a la que se asigna una propiedad.

#### <a name="new-behavior"></a>Comportamiento nuevo

`GetColumnName()` todavía devuelve el nombre de la columna a la que se asigna una propiedad, pero este comportamiento ahora es ambiguo, ya que EF Core 5 admite el modelo de tabla por tipo y la asignación simultánea a una vista o una función en la que estas asignaciones podrían usar nombres de columna diferentes para la misma propiedad.

#### <a name="why"></a>Por qué

Hemos marcado este método como obsoleto para guiar a los usuarios a una sobrecarga más precisa: <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.

#### <a name="mitigations"></a>Mitigaciones

Use el siguiente código para obtener el nombre de columna de una tabla específica:

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a>Los decimales necesitan precisión y escala

[Incidencia de seguimiento n.º 19293](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a>Comportamiento anterior

Normalmente, en EF Core no se establecía la precisión y la escala en los objetos <xref:Microsoft.Data.SqlClient.SqlParameter>. Esto significa que la precisión y la escala completa se enviaban a SQL Server, donde se redondeaba en función de la precisión y la escala de la columna de base de datos.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora en EF Core se establece la precisión y la escala de los parámetros con los valores configurados para las propiedades en el modelo de EF Core. Esto significa que ahora el redondeo se produce en SqlClient. Por tanto, si la precisión y la escala configuradas no coinciden con las de la base de datos, es posible que cambie el redondeo que se ve.

#### <a name="why"></a>Por qué

Para las características más recientes de SQL Server, incluida Always Encrypted, es necesario que las facetas de parámetro se especifiquen de forma completa. Además, SqlClient ha realizado un cambio para redondear los valores decimales en lugar de truncarlos, lo que coincide con el comportamiento de SQL Server. Esto ha hecho posible que EF Core establezca estas facetas sin cambiar el comportamiento de los decimales configurados correctamente.

#### <a name="mitigations"></a>Mitigaciones

Asigne las propiedades decimales mediante un nombre de tipo que incluya precisión y escala. Por ejemplo:

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

O bien, use `HasPrecision` en las API de creación de modelos. Por ejemplo:

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>El requisito relativo a la navegación de una entidad de seguridad a un elemento dependiente tiene una semántica diferente.

[Incidencia de seguimiento n.º 17286](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a>Comportamiento anterior

Solo las navegaciones a la entidad de seguridad se podían configurar como necesarias. Por lo tanto, el uso de `RequiredAttribute` en la navegación a un elemento dependiente (la entidad que contiene la clave externa) creaba en su lugar la clave externa en el tipo de entidad de definición.

#### <a name="new-behavior"></a>Comportamiento nuevo

Gracias a la compatibilidad agregada con los elementos dependientes necesarios, ahora es posible marcar cualquier navegación de referencia como obligatoria, lo que significa que, en el caso anterior, la clave externa se definirá en el otro lado de la relación y las propiedades no se marcarán como obligatorias.

Llamar a `IsRequired` antes de especificar el extremo dependiente ahora es ambiguo:

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a>Por qué

El comportamiento nuevo es obligatorio para habilitar la compatibilidad con los dependientes necesarios ([vea n.º 12100](https://github.com/dotnet/efcore/issues/12100)).

#### <a name="mitigations"></a>Mitigaciones

Quite `RequiredAttribute` de la navegación al elemento dependiente y colóquelo en su lugar en la navegación de la entidad de seguridad o configure la relación en `OnModelCreating`:

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>La definición de la consulta se reemplaza por métodos específicos del proveedor

[Problema de seguimiento n.º 18903](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a>Comportamiento anterior

Los tipos de entidad se asignaban en la definición de consultas en el nivel básico. Cuando el tipo de entidad se usaba en la raíz de la consulta del tipo de entidad se sustituía por la consulta de definición de cualquier proveedor.

#### <a name="new-behavior"></a>Comportamiento nuevo

Las API de definición de consulta han quedado en desuso. Se introdujeron nuevas API específicas del proveedor.

#### <a name="why"></a>Por qué

Aunque la definición de consulta se implementaba como consulta de reemplazo siempre que se usaba la raíz de la consulta en la consulta, tenía algunos problemas:

- Si la definición de consulta establece la proyección del tipo de entidad con `new { ... }` en el método `Select`, su identificación como entidad requería trabajo adicional y lo hacía incoherente con el modo en que EF Core trata los tipos nominales en la consulta.
- En el caso de los proveedores relacionales `FromSql` todavía hay que pasar la cadena SQL en forma de expresión LINQ.

Las definiciones de consulta se introdujeron inicialmente como vistas del lado cliente para su uso con el proveedor en memoria para las entidades sin clave (similares a las vistas de base de datos de las bases de datos relacionales). Esta definición facilita la prueba de la aplicación en la base de datos en memoria. Después, se convirtieron en ampliamente aplicables, lo que resultaba útil pero no era coherente y resultaba difícil de entender. Por tanto, decidimos simplificar el concepto. Hemos creado una consulta de definición basada en LINQ exclusiva para el proveedor en memoria, que tratamos de otra forma. Para más información, consulte [este problema](https://github.com/dotnet/efcore/issues/20023).

#### <a name="mitigations"></a>Mitigaciones

En el caso de los proveedores relacionales, use el método `ToSqlQuery` en `OnModelCreating` y pase una cadena SQL que se utilice como tipo de entidad.
En el caso del proveedor en memoria, use el método `ToInMemoryQuery` en `OnModelCreating` y pase una consulta LINQ que se utilice como tipo de entidad.

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a>Las consultas no sobrescriben las navegaciones de referencia no nula

[Incidencia de seguimiento n.º 2693](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a>Comportamiento anterior

En EF Core 3.1, las instancias de la entidad de la base de datos sobrescribirán las navegaciones de referencia que se inicializan de manera diligente en valores que no sean NULL, con independencia de si coinciden o no los valores de claves. Sin embargo, en otros casos, EF Core 3.1 haría lo contrario y dejaría el valor no NULL existente.

#### <a name="new-behavior"></a>Comportamiento nuevo

A partir de EF Core 5.0, las instancias devueltas por una consulta nunca sobrescriben las navegaciones de referencia no NULL.

Tenga en cuenta que todavía se admite la inicialización diligente de una navegación de _colección_ en una colección vacía.

#### <a name="why"></a>Por qué

La inicialización de una propiedad de navegación de referencia en una instancia de entidad "vacía" da como resultado un estado ambiguo. Por ejemplo:

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

Normalmente, una consulta de blogs y autores primero creará instancias `Blog` y, después, establecerá las instancias `Author` apropiadas en función de los datos devueltos por la base de datos. Sin embargo, en este caso, cada propiedad `Blog.Author` ya se ha inicializado en una instancia `Author` vacía. Excepto EF Core, no tiene ninguna manera de saber que esta instancia está "vacía". Por lo tanto, si se sobrescribe esta instancia, es posible que se elimine de forma silenciosa una instancia `Author` válida. Por lo tanto, EF Core 5.0 ahora no sobrescribe de forma coherente una navegación que ya está inicializada.

Este nuevo comportamiento también se alinea con el comportamiento de EF6 en la mayoría de los casos, aunque en la investigación también encontramos algunos casos de incoherencia en EF6.

#### <a name="mitigations"></a>Mitigaciones

Si se detecta esta interrupción, la solución consiste en detener la inicialización diligente de las propiedades de navegación de referencia.

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a>ToView() se trata de forma diferente en las migraciones

[Incidencia de seguimiento n.º 2725](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a>Comportamiento anterior

Las llamadas a `ToView(string)` hacían que las migraciones omitieran el tipo de entidad y lo asignaran a una vista.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora `ToView(string)` marca el tipo de entidad como no asignado a una tabla y lo asigna a una vista. Como resultado, la primera migración después de actualizar a EF Core 5 intenta quitar la tabla predeterminada para este tipo de entidad, puesto que ya no se omite.

#### <a name="why"></a>Por qué

EF Core ahora permite asignar un tipo de entidad a una tabla y una vista simultáneamente, de modo que `ToView` ya no es un indicador válido que las migraciones deben omitir.

#### <a name="mitigations"></a>Mitigaciones

Use el siguiente código para marcar la tabla asignada como excluida de las migraciones:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a>ToTable(null) marca el tipo de entidad como no asignado a una tabla

[Incidencia de seguimiento n.º 21172](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a>Comportamiento anterior

`ToTable(null)` restablecía el nombre de la tabla al valor predeterminado.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora, `ToTable(null)` marca el tipo de entidad como no asignado a ninguna tabla.

#### <a name="why"></a>Por qué

EF Core ahora permite asignar un tipo de entidad a una tabla y una vista simultáneamente, de modo que `ToTable(null)` se usa para indicar que no está asignado a ninguna tabla.

#### <a name="mitigations"></a>Mitigaciones

Use el siguiente código para restablecer el nombre de la tabla al valor predeterminado si no está asignado a una vista o una función DbFunction:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a>Cambios de impacto bajo

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Se ha quitado el método HasGeometricDimension de la extensión de SQLite NTS

[Problema de seguimiento n.º 14257](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a>Comportamiento anterior

Se ha utilizado HasGeometricDimension para habilitar dimensiones adicionales (Z y M) en columnas de geometría. Sin embargo, solo ha afectado a la creación de la base de datos. No era necesario especificarlo para consultar los valores con dimensiones adicionales. Tampoco ha funcionado correctamente al introducir o actualizar valores con dimensiones adicionales ([consulte el problema n.º 14257](https://github.com/dotnet/efcore/issues/14257)).

#### <a name="new-behavior"></a>Comportamiento nuevo

Para habilitar la inserción y la actualización de valores de geometría con dimensiones adicionales (Z y M), la dimensión debe especificarse como parte del nombre del tipo de columna. Esta API coincide más estrechamente con el comportamiento subyacente de la función AddGeometryColumn de SpatiaLite.

#### <a name="why"></a>Por qué

El uso de HasGeometricDimension después de especificar la dimensión en el tipo de columna es innecesario y redundante, por lo que se ha quitado HasGeometricDimension por completo.

#### <a name="mitigations"></a>Mitigaciones

Use `HasColumnType` para especificar la dimensión:

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos: la clave de partición se ha agregado ahora a la clave principal

[Incidencia de seguimiento n.º 15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Comportamiento anterior

La propiedad de clave de partición solo se agregaba a la clave alternativa que incluye `id`.

#### <a name="new-behavior"></a>Comportamiento nuevo

La propiedad de clave de partición ahora también se agrega por convención a la clave principal.

#### <a name="why"></a>Por qué

Este cambio hace que el modelo se alinee mejor con la semántica de Azure Cosmos DB y mejora el rendimiento de `Find` y algunas consultas.

#### <a name="mitigations"></a>Mitigaciones

Para evitar que la propiedad de clave de partición se agregue a la clave principal, configúrela en `OnModelCreating`.

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos: el nombre de la propiedad `id` se ha cambiado a `__id`

[Problema de seguimiento n.º 17751](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a>Comportamiento anterior

La propiedad Shadow asignada a la propiedad `id` de JSON también se llama `id`.

#### <a name="new-behavior"></a>Comportamiento nuevo

La propiedad Shadow creada por convención se denomina ahora `__id`.

#### <a name="why"></a>Por qué

Este cambio hace menos probable que la propiedad `id` entre en conflicto con una propiedad existente en el tipo de entidad.

#### <a name="mitigations"></a>Mitigaciones

Para volver al comportamiento de la versión 3.x, configure la propiedad `id` en `OnModelCreating`.

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos: byte[] se almacena ahora como cadena Base64 y no como matriz de números

[Problema de seguimiento n.º 17306](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a>Comportamiento anterior

Las propiedades de tipo byte[] se almacenaban como matriz de números.

#### <a name="new-behavior"></a>Comportamiento nuevo

Las propiedades de tipo byte[] se almacenan ahora como cadena Base64.

#### <a name="why"></a>Por qué

Esta representación de byte [] se alinea mejor con las expectativas y es el comportamiento predeterminado de las principales bibliotecas de serialización de JSON.

#### <a name="mitigations"></a>Mitigaciones

Los datos existentes almacenados como matrices de números se seguirán consultando correctamente, pero actualmente no hay ninguna manera compatible de volver a cambiar el comportamiento de inserción. Si esta limitación bloquea su escenario, haga un comentario en [este problema](https://github.com/dotnet/efcore/issues/17306)

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos: se ha cambiado el nombre de GetPropertyName y SetPropertyName

[Problema de seguimiento n.º 17874](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a>Comportamiento anterior

Anteriormente, se llamaba a los métodos de extensión `GetPropertyName` y `SetPropertyName`

#### <a name="new-behavior"></a>Comportamiento nuevo

La API antigua se ha quitado y se han agregado métodos nuevos: `GetJsonPropertyName` y `SetJsonPropertyName`.

#### <a name="why"></a>Por qué

Este cambio elimina la ambigüedad en torno a lo que estos métodos configuran.

#### <a name="mitigations"></a>Mitigaciones

Use la API nueva.

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>Se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado

[Incidencia de seguimiento n.º 15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Comportamiento anterior

Solo se llamaba a los generadores de valores cuando el estado de la entidad cambiaba a Agregado.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora se llama a los generadores de valores cuando se cambia el estado de la entidad de Desasociado a Sin cambios, Actualizado o Eliminado, y la propiedad incluye los valores predeterminados.

#### <a name="why"></a>Por qué

Este cambio era necesario para mejorar la experiencia con propiedades que no se conservan en el almacén de datos y que su valor se genera siempre en el cliente.

#### <a name="mitigations"></a>Mitigaciones

Para evitar que se llame al generador de valores, asigne un valor no predeterminado a la propiedad antes de cambiar el estado.

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer ahora usa IRelationalModel.

[Incidencia de seguimiento n.º 20305](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a>Comportamiento anterior

`IMigrationsModelDiffer` API se definía mediante `IModel`.

#### <a name="new-behavior"></a>Comportamiento nuevo

`IMigrationsModelDiffer` API ahora usa `IRelationalModel`. Sin embargo, la instantánea del modelo todavía contiene solo `IModel`, ya que este código forma parte de la aplicación y Entity Framework no puede cambiarla sin hacer un cambio más importante.

#### <a name="why"></a>Por qué

`IRelationalModel` es una representación recién agregada del esquema de la base de datos. Su uso para encontrar diferencias es más rápido y preciso.

#### <a name="mitigations"></a>Mitigaciones

Use el código siguiente para comparar el modelo de `snapshot` con el modelo de `context`:

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

Tenemos previsto mejorar esta experiencia en la versión 6.0 ([vea n.º 22031](https://github.com/dotnet/efcore/issues/22031)).

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a>Los discriminadores son de solo lectura.

[Incidencia de seguimiento n.º 21154](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a>Comportamiento anterior

Era posible cambiar el valor del discriminador antes de llamar a `SaveChanges`.

#### <a name="new-behavior"></a>Comportamiento nuevo

En el caso anterior, se producirá una excepción.

#### <a name="why"></a>Por qué

EF no espera que el tipo de entidad cambie mientras se sigue realizando el seguimiento, por lo que cambiar el valor del discriminador deja el contexto en un estado incoherente, lo que podría dar como resultado un comportamiento inesperado.

#### <a name="mitigations"></a>Mitigaciones

Si es necesario cambiar el valor del discriminador y el contexto se va a eliminar inmediatamente después de llamar a `SaveChanges`, el discriminador se puede convertir en mutable:

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>Los métodos EF.Functions específicos del proveedor se inician para el proveedor InMemory.

[Incidencia de seguimiento n.º 20294](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a>Comportamiento anterior

Los métodos EF.Functions específicos del proveedor incluían la implementación para la ejecución del cliente, lo cual les permitía ejecutarse en el proveedor de InMemory. Por ejemplo, `EF.Functions.DateDiffDay` es un método específico de SQL Server que funcionaba en el proveedor InMemory.

#### <a name="new-behavior"></a>Comportamiento nuevo

Los métodos específicos del proveedor se han actualizado para producir una excepción en el cuerpo del método a fin de bloquear su evaluación en el lado cliente.

#### <a name="why"></a>Por qué

Los métodos específicos del proveedor se asignan a una función de base de datos. El cálculo realizado por la función de base de datos asignada no siempre se puede replicar en el lado cliente en LINQ. Esto puede provocar que el resultado del servidor sea diferente al ejecutar el mismo método en el cliente. Dado que estos métodos se usan en LINQ para traducir a funciones específicas de base de datos, no es necesario que se evalúen en el lado cliente. Dado que el proveedor InMemory es una base de datos *diferente*, estos métodos no están disponibles para este proveedor. Al intentar ejecutarlos para el proveedor InMemory o cualquier otro proveedor que no traduzca estos métodos, se produce una excepción.

#### <a name="mitigations"></a>Mitigaciones

Dado que no hay forma de imitar el comportamiento de las funciones de base de datos con precisión, debe probar las consultas que las contienen en el mismo tipo de base de datos que en producción.

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder.HasName ahora está obsoleto

[Incidencia de seguimiento n.º 21089](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a>Comportamiento anterior

Anteriormente, solo se podía definir un índice en un conjunto determinado de propiedades. El nombre de la base de datos de un índice se configuró mediante IndexBuilder.HasName.

#### <a name="new-behavior"></a>Comportamiento nuevo

Ahora se permiten varios índices en el mismo conjunto o las mismas propiedades. Ahora, estos índices se distinguen por un nombre en el modelo. Por convención, el nombre del modelo se utiliza como nombre de la base de datos; sin embargo, también se puede configurar de forma independiente utilizando HasDatabaseName.

#### <a name="why"></a>Por qué

En el futuro, nos gustaría habilitar índices ascendentes y descendentes o índices con distintas intercalaciones en el mismo conjunto de propiedades. Este cambio nos hace dar otro paso en esa dirección.

#### <a name="mitigations"></a>Mitigaciones

Cualquier código que llamara anteriormente a IndexBuilder.HasName debe actualizarse para llamar a HasDatabaseName en su lugar.

Si su proyecto incluye migraciones generadas antes de la versión 2.0.0 de EF Core, puede ignorar la advertencia en esos archivos de forma segura y suprimirla agregando `#pragma warning disable 612, 618`.

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Ahora se incluye un pluralizador para el scaffolding de los modelos de ingeniería inversa

[Incidencia de seguimiento n.º 11160](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a>Comportamiento anterior

Anteriormente, tenía que instalar un paquete de pluralizador independiente para pluralizar los nombres de navegación de colección y DbSet y singularizar los nombres de tabla al aplicar scaffolding a DbContext y los tipos de entidad mediante la utilización de técnicas de ingeniería inversa en un esquema de base de datos.

#### <a name="new-behavior"></a>Comportamiento nuevo

EF Core incluye ahora un pluralizador que usa la biblioteca [Humanizer](https://github.com/Humanizr/Humanizer). Se trata de la misma biblioteca que Visual Studio usa para recomendar nombres de variable.

#### <a name="why"></a>Por qué

El uso de formas plurales de palabras para las propiedades de colección y de formas singulares para los tipos y las propiedades de referencia es idiomático en .NET.

#### <a name="mitigations"></a>Mitigaciones

Para deshabilitar el pluralizador, use la opción `--no-pluralize` en `dotnet ef dbcontext scaffold` o el modificador `-NoPluralize` en `Scaffold-DbContext`.

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a>INavigationBase reemplaza a INavigation en algunas API para admitir la omisión de navegaciones

[Incidencia de seguimiento n.º 2568](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a>Comportamiento anterior

Las versiones de EF Core anteriores a la 5.0 solo admiten una forma de propiedad de navegación, representada por la interfaz `INavigation`.

#### <a name="new-behavior"></a>Comportamiento nuevo

EF Core 5.0 presenta relaciones varios a varios que usan las "navegaciones por omisión". Se representan mediante la interfaz `ISkipNavigation`, y la mayor parte de la funcionalidad de `INavigation` se ha insertado en una interfaz base común: `INavigationBase`.

#### <a name="why"></a>Por qué

La mayor parte de la funcionalidad entre las navegaciones normal y por omisión es la misma. Sin embargo, las navegaciones por omisión tienen una relación diferente con las claves externas con respecto a las navegaciones normales, ya que las claves externas implicadas no están directamente en ninguno de los extremos de la relación, sino en la entidad join.

#### <a name="mitigations"></a>Mitigaciones

En muchos casos, las aplicaciones pueden cambiar al uso de la nueva interfaz base sin realizar ningún otro cambio. Sin embargo, en los casos en los que se usa la navegación para acceder a las propiedades de clave externa, el código de aplicación se debe restringir solo a las navegaciones normales, o bien actualizarse para hacer lo adecuado para las navegaciones normal y por omisión.

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a>Ya no se admiten algunas consultas con colecciones correlacionadas que también usan `Distinct` o `GroupBy`

[Incidencia de seguimiento n.º 15873](https://github.com/dotnet/efcore/issues/15873)

**Comportamiento anterior**

Anteriormente, las consultas que implicaban colecciones correlacionadas seguidas de `GroupBy`, así como algunas consultas con `Distinct` que permitimos ejecutar.

Ejemplo de GroupBy:

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

Ejemplo de `Distinct`: en concreto, las consultas `Distinct` en las que la proyección de la colección interna no contiene la clave principal:

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

Estas consultas podrían devolver resultados incorrectos si la colección interna contuviera duplicados, pero funcionaría correctamente si todos los elementos de la colección interna fueran únicos.

**Comportamiento nuevo**

Estas consultas ya no son compatibles. Se produce una excepción que indica que no hay suficiente información para compilar los resultados correctamente.

**Por qué**

En el caso de los escenarios de colecciones correlacionadas, es necesario conocer la clave principal de la entidad para asignar entidades de colección al elemento primario correcto. Cuando la colección interna no utiliza `GroupBy` ni `Distinct`, la clave principal que falta se puede agregar simplemente a la proyección. Sin embargo, en el caso de `GroupBy` y `Distinct`, no se puede hacer porque cambiaría el resultado de la operación `GroupBy` o `Distinct`.

**Mitigaciones**

Vuelva a escribir la consulta para que no use las operaciones `GroupBy` o `Distinct` en la colección interna y, en su lugar, realice estas operaciones en el cliente.

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a>No se admite el uso de una colección de tipo consultable en la proyección

[Incidencia de seguimiento n.º 16314](https://github.com/dotnet/efcore/issues/16314)

**Comportamiento anterior**

Anteriormente, era posible usar la colección de un tipo consultable dentro de la proyección en algunos casos, por ejemplo, como un argumento de un constructor `List<T>`:

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

**Comportamiento nuevo**

Estas consultas ya no son compatibles. Se produce una excepción que indica que no se puede crear un objeto de tipo consultable y, además, se sugiere cómo corregir este problema.

**Por qué**

No se puede materializar un objeto de un tipo consultable, por lo que, en su lugar, se creará automáticamente con el tipo `List<T>`. Esto suele provocar una excepción debido a una falta de coincidencia de tipos que no era muy clara y podría ser sorprendente para algunos usuarios. Decidimos reconocer el patrón y producir una excepción más significativa.

**Mitigaciones**

Agregue la llamada a `ToList()` después del objeto consultable en la proyección:

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
