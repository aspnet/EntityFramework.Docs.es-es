---
title: 'Code First procedimientos almacenados de inserción, actualización y eliminación: EF6'
description: Code First los procedimientos almacenados de inserción, actualización y eliminación en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 749bcc9faaf92ae6555c37facb8de31d877d1260
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065191"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>Code First procedimientos almacenados de inserción, actualización y eliminación
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

De forma predeterminada, Code First configurará todas las entidades para que realicen los comandos de inserción, actualización y eliminación mediante el acceso directo a tablas. A partir de EF6, puede configurar el modelo de Code First para que use procedimientos almacenados para algunas o todas las entidades del modelo.  

## <a name="basic-entity-mapping"></a>Asignación de entidad básica  

Puede optar por usar procedimientos almacenados para INSERT, Update y DELETE mediante la API fluida.  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

Esto hará que Code First utilice algunas convenciones para generar la forma esperada de los procedimientos almacenados en la base de datos.  

- Tres procedimientos almacenados denominados ** \<type_name\> _Insert**, ** \<type_name\> _Update** y ** \<type_name\> _Delete** (por ejemplo, Blog_Insert, Blog_Update y Blog_Delete).  
- Los nombres de parámetro se corresponden con los nombres de propiedad.  
  > [!NOTE]
  > Si usa HasColumnName () o el atributo de columna para cambiar el nombre de la columna de una propiedad determinada, este nombre se utiliza para los parámetros en lugar del nombre de la propiedad.  
- **El procedimiento almacenado Insert** tendrá un parámetro para cada propiedad, a excepción de los marcados como almacenamiento generado (identidad o calculado). El procedimiento almacenado debe devolver un conjunto de resultados con una columna para cada propiedad generada por el almacén.  
- **El procedimiento almacenado de actualización** tendrá un parámetro para cada propiedad, excepto aquellos marcados con un modelo generado por un almacén de ' Calculated '. Algunos tokens de simultaneidad requieren un parámetro para el valor original; vea la sección *tokens de simultaneidad* a continuación para obtener más información. El procedimiento almacenado debe devolver un conjunto de resultados con una columna para cada propiedad calculada.  
- **El procedimiento almacenado Delete** debe tener un parámetro para el valor de clave de la entidad (o varios parámetros si la entidad tiene una clave compuesta). Además, el procedimiento de eliminación también debe tener parámetros para cualquier clave externa de asociación independiente en la tabla de destino (relaciones que no tienen las propiedades de clave externa correspondientes declaradas en la entidad). Algunos tokens de simultaneidad requieren un parámetro para el valor original; vea la sección *tokens de simultaneidad* a continuación para obtener más información.  

Use la siguiente clase como ejemplo:  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

Los procedimientos almacenados predeterminados serían:  

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS BlogId
END
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId;
CREATE PROCEDURE [dbo].[Blog_Delete]  
  @BlogId int  
AS  
  DELETE FROM [dbo].[Blogs]
  WHERE BlogId = @BlogId
```  

### <a name="overriding-the-defaults"></a>Reemplazar los valores predeterminados  

Puede invalidar parte o todo lo que se configuró de forma predeterminada.  

Puede cambiar el nombre de uno o más procedimientos almacenados. En este ejemplo se cambia el nombre del procedimiento almacenado de actualización únicamente.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

En este ejemplo se cambia el nombre de los tres procedimientos almacenados.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

En estos ejemplos, las llamadas se encadenan entre sí, pero también puede usar la sintaxis de bloque lambda.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    {  
      s.Update(u => u.HasName("modify_blog"));  
      s.Delete(d => d.HasName("delete_blog"));  
      s.Insert(i => i.HasName("insert_blog"));  
    });
```  

En este ejemplo se cambia el nombre del parámetro de la propiedad BlogId en el procedimiento almacenado de actualización.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

Estas llamadas son todas encadenables y que admiten composición. Este es un ejemplo en el que se cambia el nombre de los tres procedimientos almacenados y sus parámetros.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")  
                   .Parameter(b => b.BlogId, "blog_id")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url"))  
     .Delete(d => d.HasName("delete_blog")  
                   .Parameter(b => b.BlogId, "blog_id"))  
     .Insert(i => i.HasName("insert_blog")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url")));
```  

También puede cambiar el nombre de las columnas del conjunto de resultados que contiene los valores generados por la base de datos.  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures(s =>
    s.Insert(i => i.Result(b => b.BlogId, "generated_blog_identity")));
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS generated_blog_id
END
```  

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>Relaciones sin una clave externa en la clase (asociaciones independientes)  

Cuando se incluye una propiedad de clave externa en la definición de clase, se puede cambiar el nombre del parámetro correspondiente de la misma manera que cualquier otra propiedad. Cuando existe una relación sin una propiedad de clave externa en la clase, el nombre del parámetro predeterminado es ** \<navigation_property_name\> _ \<primary_key_name\> **.  

Por ejemplo, las siguientes definiciones de clase darían lugar a la espera de un parámetro Blog_BlogId en los procedimientos almacenados para insertar y actualizar las entradas.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }

  public List<Post> Posts { get; set; }  
}  

public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public Blog Blog { get; set; }  
}
```  

### <a name="overriding-the-defaults"></a>Reemplazar los valores predeterminados  

Puede cambiar los parámetros de las claves externas que no están incluidas en la clase proporcionando la ruta de acceso a la propiedad de clave principal al método de parámetro.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

Si no tiene una propiedad de navegación en la entidad dependiente (es decir, no se puede usar el método Association para identificar el otro extremo de la relación y, después, configurar los parámetros correspondientes a cada una de las propiedades de clave.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>Tokens de simultaneidad  

Los procedimientos almacenados de actualización y eliminación también pueden necesitar tratar la simultaneidad:  

- Si la entidad contiene tokens de simultaneidad, el procedimiento almacenado puede tener opcionalmente un parámetro de salida que devuelve el número de filas actualizadas o eliminadas (filas afectadas). Este tipo de parámetro se debe configurar mediante el método RowsAffectedParameter.  
De forma predeterminada, EF usa el valor devuelto por ExecuteNonQuery para determinar el número de filas afectadas. La especificación de un parámetro de salida de filas afectado es útil si se realiza cualquier lógica en el procedimiento almacenado, lo que daría lugar a que el valor devuelto de ExecuteNonQuery fuera incorrecto (desde la perspectiva del EF) al final de la ejecución.  
- Para cada token de simultaneidad habrá un parámetro denominado ** \<property_name\> _Original** (por ejemplo, Timestamp_Original). Se pasará el valor original de esta propiedad, que es el valor cuando se consulta desde la base de datos.  
    - Los tokens de simultaneidad calculados por la base de datos, como las marcas de tiempo, solo tendrán un parámetro de valor original.  
    - Las propiedades no calculadas que se establecen como tokens de simultaneidad también tendrán un parámetro para el nuevo valor en el procedimiento de actualización. Utiliza las convenciones de nomenclatura ya descritas para los nuevos valores. Un ejemplo de este tipo de token sería usar la dirección URL de un blog como un token de simultaneidad, el nuevo valor es necesario porque puede actualizarse a un nuevo valor por el código (a diferencia de un token de marca de tiempo que solo se actualiza en la base de datos).  

Esta es una clase de ejemplo y un procedimiento almacenado de actualización con un token de simultaneidad de marca de tiempo.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
  [Timestamp]
  public byte[] Timestamp { get; set; }
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Timestamp_Original rowversion  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Timestamp] = @Timestamp_Original
```  

A continuación se muestra una clase de ejemplo y un procedimiento almacenado de actualización con un token de simultaneidad no calculado.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  [ConcurrencyCheck]
  public string Url { get; set; }  
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Url_Original nvarchar(max),
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Url] = @Url_Original
```  

### <a name="overriding-the-defaults"></a>Reemplazar los valores predeterminados  

Opcionalmente, puede introducir un parámetro de filas afectadas.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

En el caso de tokens de simultaneidad calculados de base de datos, donde solo se pasa el valor original, solo puede usar el mecanismo de cambio de nombre de parámetros estándar para cambiar el nombre del parámetro para el valor original.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

En el caso de los tokens de simultaneidad no calculados, donde se pasan tanto el valor original como el nuevo, puede utilizar una sobrecarga de parámetro que le permita proporcionar un nombre para cada parámetro.  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>Relaciones de varios a varios  

Usaremos las clases siguientes como ejemplo en esta sección.  

``` csharp
public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public List<Tag> Tags { get; set; }  
}  

public class Tag  
{  
  public int TagId { get; set; }  
  public string TagName { get; set; }  

  public List<Post> Posts { get; set; }  
}
```  

Las relaciones de varios a varios se pueden asignar a procedimientos almacenados con la siguiente sintaxis.  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

Si no se proporciona ninguna otra configuración, se utiliza de forma predeterminada la siguiente forma de procedimiento almacenado.  

- Dos procedimientos almacenados denominados ** \<type_one\> \<type_two\> _Insert** y ** \<type_one\> \<type_two\> _Delete** (por ejemplo, PostTag_Insert y PostTag_Delete).  
- Los parámetros serán los valores de clave de cada tipo. El nombre de cada parámetro es ** \<type_name\> _ \<property_name\> ** (por ejemplo, Post_PostId y Tag_TagId).

Estos son los procedimientos almacenados de inserción y actualización de ejemplo.  

``` SQL
CREATE PROCEDURE [dbo].[PostTag_Insert]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  INSERT INTO [dbo].[Post_Tags] (Post_PostId, Tag_TagId)   
  VALUES (@Post_PostId, @Tag_TagId)
CREATE PROCEDURE [dbo].[PostTag_Delete]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  DELETE FROM [dbo].[Post_Tags]    
  WHERE Post_PostId = @Post_PostId AND Tag_TagId = @Tag_TagId
```  

### <a name="overriding-the-defaults"></a>Reemplazar los valores predeterminados  

Los nombres de parámetros y procedimientos se pueden configurar de forma similar a los procedimientos almacenados de entidad.  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.HasName("add_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id"))  
     .Delete(d => d.HasName("remove_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id")));
```  
