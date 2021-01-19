---
title: 'Eliminación en cascada: EF Core'
description: Configuración de comportamientos en cascada que se desencadenan cuando una entidad se elimina o se interrumpe la relación con su entidad de seguridad o primaria
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 7c35de900930cf42da0e534df76124b5fb19ca52
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128867"
---
# <a name="cascade-delete"></a>Eliminación en cascada

Entity Framework Core (EF Core) representa relaciones mediante claves externas. Una entidad con una clave externa es la entidad secundaria o dependiente en la relación. El valor de clave externa de esta entidad debe coincidir con el de clave principal (o con uno de clave alternativa) de la entidad de seguridad o primaria relacionada.

Si se elimina la entidad de seguridad o primaria, los valores de clave externa de las entidades dependientes o secundarias dejarán de coincidir con la clave principal o la clave alternativa de _cualquier_ entidad de seguridad o primaria. Este estado no es válido y producirá una infracción de la restricción referencial en la mayoría de las bases de datos.

Hay dos maneras de evitar esta infracción de la restricción referencial:

1. Establecer los valores de CE (clave externa) en NULL
2. Eliminar también las entidades dependientes o secundarias

La primera opción solo es válida para las relaciones opcionales en las que la propiedad de clave externa, así como la columna de la base de datos a la que está asignada, deben admitir valores NULL.

La segunda opción es válida para cualquier tipo de relación y se conoce como "eliminación en cascada".

> [!TIP]
> En este documento se describen las eliminaciones en cascada (y la eliminación de entidades huérfanas) desde el punto de vista de la actualización de una base de datos. Se hace un uso intensivo de los conceptos introducidos en los artículos [Herramienta de seguimiento de cambios en EF Core](xref:core/change-tracking/index) y [Cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes). Debe entender perfectamente estos conceptos antes de abordar el material de este artículo.

> [!TIP]  
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).

## <a name="when-cascading-behaviors-happen"></a>Cuándo se producen comportamientos en cascada

Las eliminaciones en cascada son necesarias cuando una entidad dependiente o secundaria ya no se puede asociar a su entidad de seguridad o a su entidad primaria actual. Esto puede ocurrir porque la entidad de seguridad o primaria se elimina, o bien cuando la entidad de seguridad o primaria todavía existe, pero la entidad dependiente o secundaria ya no está asociada a ella.

### <a name="deleting-a-principalparent"></a>Eliminación de una entidad de seguridad o primaria

Considere este modelo sencillo en el que `Blog` es la entidad de seguridad o primaria en una relación con `Post`, que es la entidad dependiente o secundaria. `Post.BlogId` es una propiedad de clave externa cuyo valor debe coincidir con el de la clave principal `Post.Id` de la publicación a la que pertenece el blog.

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

Por convención, esta relación se configura como obligatoria, ya que la propiedad de clave externa `Post.BlogId` no acepta valores NULL. Las relaciones obligatorias están configuradas para usar eliminaciones en cascada de manera predeterminada. Consulte el artículo [Relaciones](xref:core/modeling/relationships) para obtener más información sobre las relaciones de modelado.

Al eliminar un blog, se eliminan todas las publicaciones en cascada. Por ejemplo:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

SaveChanges genera el siguiente código SQL, usando SQL Server como ejemplo:

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a>Interrupción de una relación

En lugar de eliminar el blog, podríamos interrumpir la relación entre cada publicación y su blog. Esto se puede hacer estableciendo la propiedad de navegación de referencia `Post.Blog` en NULL para cada publicación:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

La relación también se puede interrumpir quitando todas las publicaciones de la navegación de colección `Blog.Posts`:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

En cualquier caso, el resultado es el mismo: el blog no se elimina, solo se eliminan las publicaciones que ya no están asociadas a ningún blog:

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

La eliminación de entidades que ya no están asociadas a ninguna entidad de seguridad o dependiente se denomina "eliminación de entidades huérfanas".

> [!TIP]
> La eliminación en cascada y la eliminación de entidades huérfanas están estrechamente relacionadas. Ambas dan como resultado la eliminación de entidades dependientes o secundarias cuando se interrumpe su relación con la entidad de seguridad o primaria requerida. En el caso de la eliminación en cascada, esta interrupción de la relación tiene lugar porque se elimina la propia entidad de seguridad o primaria. En el caso de las entidades huérfanas, la entidad de seguridad o primaria sigue existiendo, pero ya no está relacionada con las entidades dependientes o secundarias.  

## <a name="where-cascading-behaviors-happen"></a>Dónde se producen los comportamientos en cascada

Los comportamientos en cascada se pueden aplicar a:

- Entidades de las que la clase <xref:Microsoft.EntityFrameworkCore.DbContext> actual realiza un seguimiento
- Entidades de la base de datos que no se han cargado en el contexto

### <a name="cascade-delete-of-tracked-entities"></a>Eliminación en cascada de entidades de las que se realiza un seguimiento

EF Core aplica siempre los comportamientos en cascada configurados a las entidades sometidas a seguimiento. Esto significa que, si la aplicación carga todas las entidades dependientes o secundarias relevantes en la instancia de DbContext (como se muestra en los ejemplos anteriores), los comportamientos en cascada se aplicarán correctamente independientemente de cómo esté configurada la base de datos.

> [!TIP]
> El momento exacto en que se producen los comportamientos en cascada para las entidades sometidas a seguimiento se puede controlar mediante las propiedades <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>. Para obtener más información, vea el artículo [Cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes).

### <a name="cascade-delete-in-the-database"></a>Eliminaciones en cascada en la base de datos

Muchos sistemas de base de datos también ofrecen comportamientos en cascada, los cuales se desencadenan cuando se elimina una entidad en la base de datos. EF Core configura estos comportamientos en función del comportamiento de eliminación en cascada del modelo de EF Core cuando se crea una base de datos con migraciones de <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> o EF Core. Por ejemplo, con el modelo anterior, se crea la siguiente tabla para las publicaciones cuando se usa SQL Server:

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

Fíjese en que la restricción de clave externa que define la relación entre los blogs y las publicaciones está configurada con `ON DELETE CASCADE`.

Si sabemos que la base de datos está configurada de esta manera, podemos eliminar un blog _sin cargar primero las publicaciones_, ya que la base de datos se encargará de eliminar todas las publicaciones relacionadas con ese blog. Por ejemplo:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

Observe que no hay ningún método `Include` para las publicaciones, por lo que no se cargan. En este caso, SaveChanges eliminará solo el blog, ya que es la única entidad de la que se realiza un seguimiento:

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

Esto produciría una excepción si la restricción de clave externa de la base de datos no estuviera configurada para las eliminaciones en cascada. Pero, en este caso, la base de datos elimina las publicaciones porque se ha configurado con `ON DELETE CASCADE` en el momento de su creación.

> [!NOTE]
> Las bases de datos no suelen tener ninguna forma de eliminar automáticamente las entidades huérfanas. Esto se debe a que EF Core representa las relaciones mediante navegaciones y claves externas, pero las bases de datos solo tienen claves externas. Lo que significa que normalmente no es posible interrumpir una relación sin cargar ambos lados en la instancia de DbContext.

> [!NOTE]
> Actualmente, la base de datos en memoria de EF Core no admite las eliminaciones en cascada en la base de datos.

> [!WARNING]
> No configure la eliminación en cascada en la base de datos cuando quiera eliminar temporalmente las entidades. Esto podría eliminar por completo las entidades accidentalmente en lugar de eliminarlas temporalmente.

### <a name="database-cascade-limitations"></a>Limitaciones de los comportamientos en cascada en las bases de datos

Algunas bases de datos, especialmente las de SQL Server, tienen limitaciones en cuanto a los comportamientos en cascada que forman ciclos. Por ejemplo, tomemos el siguiente modelo:

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

Este modelo tiene tres relaciones, todas necesarias y, por tanto, están configuradas para la eliminación en cascada por convención:

- Al eliminar un blog, se eliminarán en cascada todas las publicaciones relacionadas.
- Al eliminar al autor de las publicaciones, se eliminarán en cascada sus publicaciones creadas.
- Al eliminar al propietario de un blog, el blog se eliminará en cascada.

Esto es todo razonable (aunque las directivas de administración de blogs son un poco severas), pero al intentar crear una base de datos de SQL Server con estas eliminaciones en cascada configuradas, se produce la siguiente excepción:

> Microsoft.Data.SqlClient.SqlException (0x80131904): Si especifica la restricción FOREIGN KEY "FK_Posts_Person_AuthorId" en la tabla "Posts", podrían producirse ciclos o múltiples rutas en cascada. Especifique ON DELETE NO ACTION o UPDATE NO ACTION, o bien modifique otras restricciones FOREIGN KEY.

Hay dos formas de abordar esta situación:

1. Cambiar una o varias relaciones para que no se eliminen en cascada.
2. Configurar la base de datos sin una o varias de estas eliminaciones en cascada y asegurarse de que se cargan todas las entidades dependientes para que EF Core pueda realizar el comportamiento en cascada.

Tomando el primer enfoque con nuestro ejemplo, podríamos hacer que la relación entre el propietario y el blog fuera opcional asignándole una propiedad de clave externa que acepte valores NULL:

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Una relación opcional permite que el blog exista sin un propietario, lo que significa que la eliminación en cascada ya no se configurará de manera predeterminada. También significa que ya no hay un ciclo en las acciones en cascada y, por tanto, la base de datos se puede crear sin errores en SQL Server.

Si tomamos el segundo enfoque, podemos mantener la relación entre el propietario y el blog como obligatoria, configurarla para la eliminación en cascada y hacer que esta configuración solo se aplique a las entidades sometidas a seguimiento, no a la base de datos:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

¿Qué ocurre si se cargan tanto un propietario como el blog que posee y luego se elimina al propietario?

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

EF Core eliminará en cascada al propietario para que también se elimine el blog:

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Pero el blog puede no cargarse cuando se elimina al propietario:

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

Esto generará una excepción debido a una infracción de la restricción de clave externa en la base de datos:

> Microsoft.Data.SqlClient.SqlException: Instrucción DELETE en conflicto con la restricción REFERENCE "FK_Blogs_People_OwnerId". El conflicto ha aparecido en la base de datos "Scratch", tabla "dbo.Blogs", columna "OwnerId".
Se terminó la instrucción.

## <a name="cascading-nulls"></a>Establecimiento de valores NULL en cascada

Las relaciones opcionales tienen propiedades de clave externa que aceptan valores NULL asignadas a columnas de base de datos que aceptan valores NULL. Esto significa que el valor de clave externa se puede establecer en NULL cuando la entidad de seguridad o primaria actual se elimina o cuando se interrumpe su relación con la entidad dependiente o secundaria.

Echemos un vistazo de nuevo a los ejemplos del apartado [Cuándo se producen comportamientos en cascada](#when-cascading-behaviors-happen), pero esta vez con una relación opcional representada por una propiedad de clave externa `Post.BlogId` que acepta valores NULL:

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Esta propiedad de clave externa se establecerá en NULL para cada publicación cuando se elimine su blog relacionado. Por ejemplo, tenemos este código (que es el mismo que antes):

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

Y este código ahora dará como resultado las siguientes actualizaciones de la base de datos cuando se llame a SaveChanges:

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

Del mismo modo, si la relación se interrumpe con cualquiera de los ejemplos anteriores, como el siguiente:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

O:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

En estos casos, se actualizan las publicaciones con valores de clave externa NULL cuando se llama a SaveChanges:

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Consulte el artículo [Cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) para obtener más información sobre cómo EF Core administra las claves externas y las navegaciones a medida que se cambian sus valores.

> [!NOTE]
> La corrección de relaciones como esta es el comportamiento predeterminado de Entity Framework desde la primera versión en 2008. Antes de EF Core, este comportamiento no tenía nombre y no era posible cambiarlo. Ahora se conoce como `ClientSetNull`, tal y como se describe en la sección siguiente.

Las bases de datos también se pueden configurar para establecer valores NULL en cascada de esta forma cuando se elimina una entidad de seguridad o primaria en una relación opcional, aunque esto es mucho menos común que el uso de eliminaciones en cascada en las bases de datos. El uso de eliminaciones en cascada y del establecimiento de valores NULL en cascada en la base de datos al mismo tiempo producirá casi siempre ciclos de relación cuando se use SQL Server. Consulte la sección siguiente para obtener más información sobre la configuración de valores NULL en cascada.

## <a name="configuring-cascading-behaviors"></a>Configuración de comportamientos en cascada

> [!TIP]
> Asegúrese de leer las secciones anteriores antes de llegar aquí. Es posible que las opciones de configuración no tengan sentido si no se comprende el material anterior.

Los comportamientos en cascada se configuran por relación mediante el método <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>. Por ejemplo:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

Consulte el artículo [Relaciones](xref:core/modeling/relationships) para obtener más información sobre la configuración de relaciones entre tipos de entidad.

`OnDelete` acepta un valor de la enumeración <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> (que es ciertamente confusa). Esta enumeración define el comportamiento de EF Core en las entidades sometidas a seguimiento y también la configuración de eliminación en cascada en la base de datos cuando se usa EF para crear el esquema.

### <a name="impact-on-database-schema"></a>Impacto en el esquema de la base de datos

En la siguiente tabla se muestra el resultado de cada valor `OnDelete` en la restricción de clave externa creada por migraciones de EF Core o <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.

| DeleteBehavior        | Impacto en el esquema de la base de datos
|:----------------------|--------------------------
| Cascade               | ON DELETE CASCADE
| Restringir              | ON DELETE NO ACTION
| NoAction              | <database default>
| SetNull               | ON DELETE SET NULL
| ClientSetNull         | ON DELETE NO ACTION
| ClientCascade         | ON DELETE NO ACTION
| ClientNoAction        | <database default>

> [!NOTE]
> Esta tabla es confusa y tenemos previsto revisarla en una versión futura. Consulte la [incidencia n.º 21252 de GitHub](https://github.com/dotnet/efcore/issues/21252).

Los comportamientos de `ON DELETE NO ACTION` y `ON DELETE RESTRICT` en las bases de datos relacionales suelen ser idénticos o muy parecidos. A pesar de lo que puede implicar `NO ACTION`, ambas opciones hacen que se apliquen las restricciones referenciales. La diferencia, si la hay, es _cuándo_ comprueba las restricciones la base de datos.  Consulte la documentación de su base de datos para conocer las diferencias específicas entre `ON DELETE NO ACTION` y `ON DELETE RESTRICT` en su sistema de base de datos.

Los únicos valores que generarán comportamientos en cascada en la base de datos son `Cascade` y `SetNull`. El resto de valores configurarán la base de datos para que no se realice ningún cambio en cascada.

### <a name="impact-on-savechanges-behavior"></a>Impacto en el comportamiento de SaveChanges

En las tablas de las siguientes secciones se describe lo que sucede con las entidades dependientes o secundarias cuando se elimina la entidad de seguridad o primaria, así como cuando se interrumpe su relación con las entidades dependientes o secundarias. Cada tabla abarca uno de los siguientes casos:

- Relaciones opcionales (CE que admiten valores NULL) y obligatorias (CE que no aceptan valores NULL)
- Cuando la instancia de DbContext carga y realiza el seguimiento de las entidades dependientes o secundarias, y cuando estas solo existen en la base de datos

#### <a name="required-relationship-with-dependentschildren-loaded"></a>Relación obligatoria con las entidades dependientes o secundarias cargadas

| DeleteBehavior    | Impacto al eliminar una entidad de seguridad o primaria             | Impacto al interrumpir la relación con la entidad de seguridad o primaria
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | EF Core elimina las entidades dependientes            | EF Core elimina las entidades dependientes
| Restringir          | `InvalidOperationException`              | `InvalidOperationException`
| NoAction          | `InvalidOperationException`              | `InvalidOperationException`
| SetNull           | Se produce una excepción `SqlException` al crear la base de datos      | Se produce una excepción `SqlException` al crear la base de datos
| ClientSetNull     | `InvalidOperationException`              | `InvalidOperationException`
| ClientCascade     | EF Core elimina las entidades dependientes            | EF Core elimina las entidades dependientes
| ClientNoAction    | `DbUpdateException`                      | `InvalidOperationException`

Notas:

- El valor predeterminado para las relaciones obligatorias como esta es `Cascade`.
- El uso de cualquier cosa que no sea la eliminación en cascada para las relaciones obligatorias producirá una excepción cuando se llame a SaveChanges.
  - Normalmente, se trata de una excepción `InvalidOperationException` de EF Core, puesto que el estado no válido se detecta en las entidades secundarias o dependientes cargadas.
  - `ClientNoAction` obliga a EF Core a no comprobar la corrección de las entidades dependientes antes de enviarlas a la base de datos, por lo que en este caso la base de datos produce una excepción, y SaveChanges la encapsula en una excepción `DbUpdateException`.
  - El método `SetNull` se rechaza al crear la base de datos, ya que la columna de clave externa no admite valores NULL.
- Como las entidades dependientes o secundarias están cargadas, EF Core siempre las elimina y nunca se dejan para que la base de datos las elimine.

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a>Relación obligatoria con las entidades dependientes o secundarias no cargadas

| DeleteBehavior    | Impacto al eliminar una entidad de seguridad o primaria             | Impacto al interrumpir la relación con la entidad de seguridad o primaria
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | La base de datos elimina las entidades dependientes           | N/D
| Restringir          | `DbUpdateException`                      | N/D
| NoAction          | `DbUpdateException`                      | N/D
| SetNull           | Se produce una excepción `SqlException` al crear la base de datos      | N/D
| ClientSetNull     | `DbUpdateException`                      | N/D
| ClientCascade     | `DbUpdateException`                      | N/D
| ClientNoAction    | `DbUpdateException`                      | N/D

Notas:

- No es posible interrumpir la relación en este caso, ya que las entidades dependientes o secundarias no están cargadas.
- El valor predeterminado para las relaciones obligatorias como esta es `Cascade`.
- El uso de cualquier cosa que no sea la eliminación en cascada para las relaciones obligatorias producirá una excepción cuando se llame a SaveChanges.
  - Normalmente, se trata de una excepción `DbUpdateException` porque las entidades dependientes o secundarias no están cargadas y, por lo tanto, el estado no válido solo puede detectarlo la base de datos. Después, SaveChanges encapsula la excepción de la base de datos en una excepción `DbUpdateException`.
  - El método `SetNull` se rechaza al crear la base de datos, ya que la columna de clave externa no admite valores NULL.

#### <a name="optional-relationship-with-dependentschildren-loaded"></a>Relación opcional con las entidades dependientes o secundarias cargadas

| DeleteBehavior    | Impacto al eliminar una entidad de seguridad o primaria             | Impacto al interrumpir la relación con la entidad de seguridad o primaria
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | EF Core elimina las entidades dependientes            | EF Core elimina las entidades dependientes
| Restringir          | EF Core establece las CE de las entidades dependientes en NULL     | EF Core establece las CE de las entidades dependientes en NULL
| NoAction          | EF Core establece las CE de las entidades dependientes en NULL     | EF Core establece las CE de las entidades dependientes en NULL
| SetNull           | EF Core establece las CE de las entidades dependientes en NULL     | EF Core establece las CE de las entidades dependientes en NULL
| ClientSetNull     | EF Core establece las CE de las entidades dependientes en NULL     | EF Core establece las CE de las entidades dependientes en NULL
| ClientCascade     | EF Core elimina las entidades dependientes            | EF Core elimina las entidades dependientes
| ClientNoAction    | `DbUpdateException`                      | EF Core establece las CE de las entidades dependientes en NULL

Notas:

- El valor predeterminado para las relaciones opcionales como esta es `ClientSetNull`.
- Las entidades dependientes o secundarias nunca se eliminan, a menos que se configuren los comportamientos `Cascade` o `ClientCascade`.
- El resto de valores hacen que EF Core establezca las claves externas de las entidades dependientes en NULL, excepto el siguiente valor:
  - `ClientNoAction` indica a EF Core que no toque las claves externas de las entidades dependientes o secundarias cuando se elimina la entidad de seguridad. En este caso, la base de datos produce una excepción, que SaveChanges encapsula como `DbUpdateException`.

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a>Relación opcional con las entidades dependientes o secundarias no cargadas

| DeleteBehavior    | Impacto al eliminar una entidad de seguridad o primaria             | Impacto al interrumpir la relación con la entidad de seguridad o primaria
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | La base de datos elimina las entidades dependientes           | N/D
| Restringir          | `DbUpdateException`                      | N/D
| NoAction          | `DbUpdateException`                      | N/D
| SetNull           | La base de datos establece las CE de las entidades dependientes en NULL    | N/D
| ClientSetNull     | `DbUpdateException`                      | N/D
| ClientCascade     | `DbUpdateException`                      | N/D
| ClientNoAction    | `DbUpdateException`                      | N/D

Notas:

- No es posible interrumpir la relación en este caso, ya que las entidades dependientes o secundarias no están cargadas.
- El valor predeterminado para las relaciones opcionales como esta es `ClientSetNull`.
- Las entidades dependientes o secundarias se deben cargar para evitar una excepción de base de datos, a menos que la base de datos se haya configurado para realizar eliminaciones en cascada o para establecer valores NULL en cascada.
