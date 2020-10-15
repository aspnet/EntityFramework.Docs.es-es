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
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="df576-103">Code First procedimientos almacenados de inserción, actualización y eliminación</span><span class="sxs-lookup"><span data-stu-id="df576-103">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="df576-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="df576-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="df576-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="df576-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="df576-106">De forma predeterminada, Code First configurará todas las entidades para que realicen los comandos de inserción, actualización y eliminación mediante el acceso directo a tablas.</span><span class="sxs-lookup"><span data-stu-id="df576-106">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="df576-107">A partir de EF6, puede configurar el modelo de Code First para que use procedimientos almacenados para algunas o todas las entidades del modelo.</span><span class="sxs-lookup"><span data-stu-id="df576-107">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="df576-108">Asignación de entidad básica</span><span class="sxs-lookup"><span data-stu-id="df576-108">Basic Entity Mapping</span></span>  

<span data-ttu-id="df576-109">Puede optar por usar procedimientos almacenados para INSERT, Update y DELETE mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="df576-109">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="df576-110">Esto hará que Code First utilice algunas convenciones para generar la forma esperada de los procedimientos almacenados en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="df576-110">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="df576-111">Tres procedimientos almacenados denominados \*\* \<type_name\> _Insert**, \*\* \<type_name\> _Update** y \*\* \<type_name\> _Delete\*\* (por ejemplo, Blog_Insert, Blog_Update y Blog_Delete).</span><span class="sxs-lookup"><span data-stu-id="df576-111">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="df576-112">Los nombres de parámetro se corresponden con los nombres de propiedad.</span><span class="sxs-lookup"><span data-stu-id="df576-112">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="df576-113">Si usa HasColumnName () o el atributo de columna para cambiar el nombre de la columna de una propiedad determinada, este nombre se utiliza para los parámetros en lugar del nombre de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="df576-113">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="df576-114">**El procedimiento almacenado Insert** tendrá un parámetro para cada propiedad, a excepción de los marcados como almacenamiento generado (identidad o calculado).</span><span class="sxs-lookup"><span data-stu-id="df576-114">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="df576-115">El procedimiento almacenado debe devolver un conjunto de resultados con una columna para cada propiedad generada por el almacén.</span><span class="sxs-lookup"><span data-stu-id="df576-115">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="df576-116">**El procedimiento almacenado de actualización** tendrá un parámetro para cada propiedad, excepto aquellos marcados con un modelo generado por un almacén de ' Calculated '.</span><span class="sxs-lookup"><span data-stu-id="df576-116">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="df576-117">Algunos tokens de simultaneidad requieren un parámetro para el valor original; vea la sección *tokens de simultaneidad* a continuación para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="df576-117">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="df576-118">El procedimiento almacenado debe devolver un conjunto de resultados con una columna para cada propiedad calculada.</span><span class="sxs-lookup"><span data-stu-id="df576-118">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="df576-119">**El procedimiento almacenado Delete** debe tener un parámetro para el valor de clave de la entidad (o varios parámetros si la entidad tiene una clave compuesta).</span><span class="sxs-lookup"><span data-stu-id="df576-119">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="df576-120">Además, el procedimiento de eliminación también debe tener parámetros para cualquier clave externa de asociación independiente en la tabla de destino (relaciones que no tienen las propiedades de clave externa correspondientes declaradas en la entidad).</span><span class="sxs-lookup"><span data-stu-id="df576-120">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="df576-121">Algunos tokens de simultaneidad requieren un parámetro para el valor original; vea la sección *tokens de simultaneidad* a continuación para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="df576-121">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="df576-122">Use la siguiente clase como ejemplo:</span><span class="sxs-lookup"><span data-stu-id="df576-122">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="df576-123">Los procedimientos almacenados predeterminados serían:</span><span class="sxs-lookup"><span data-stu-id="df576-123">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="df576-124">Reemplazar los valores predeterminados</span><span class="sxs-lookup"><span data-stu-id="df576-124">Overriding the Defaults</span></span>  

<span data-ttu-id="df576-125">Puede invalidar parte o todo lo que se configuró de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="df576-125">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="df576-126">Puede cambiar el nombre de uno o más procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="df576-126">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="df576-127">En este ejemplo se cambia el nombre del procedimiento almacenado de actualización únicamente.</span><span class="sxs-lookup"><span data-stu-id="df576-127">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="df576-128">En este ejemplo se cambia el nombre de los tres procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="df576-128">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="df576-129">En estos ejemplos, las llamadas se encadenan entre sí, pero también puede usar la sintaxis de bloque lambda.</span><span class="sxs-lookup"><span data-stu-id="df576-129">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="df576-130">En este ejemplo se cambia el nombre del parámetro de la propiedad BlogId en el procedimiento almacenado de actualización.</span><span class="sxs-lookup"><span data-stu-id="df576-130">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="df576-131">Estas llamadas son todas encadenables y que admiten composición.</span><span class="sxs-lookup"><span data-stu-id="df576-131">These calls are all chainable and composable.</span></span> <span data-ttu-id="df576-132">Este es un ejemplo en el que se cambia el nombre de los tres procedimientos almacenados y sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="df576-132">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="df576-133">También puede cambiar el nombre de las columnas del conjunto de resultados que contiene los valores generados por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="df576-133">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="df576-134">Relaciones sin una clave externa en la clase (asociaciones independientes)</span><span class="sxs-lookup"><span data-stu-id="df576-134">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="df576-135">Cuando se incluye una propiedad de clave externa en la definición de clase, se puede cambiar el nombre del parámetro correspondiente de la misma manera que cualquier otra propiedad.</span><span class="sxs-lookup"><span data-stu-id="df576-135">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="df576-136">Cuando existe una relación sin una propiedad de clave externa en la clase, el nombre del parámetro predeterminado es \*\* \<navigation_property_name\> _ \<primary_key_name\> \*\*.</span><span class="sxs-lookup"><span data-stu-id="df576-136">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="df576-137">Por ejemplo, las siguientes definiciones de clase darían lugar a la espera de un parámetro Blog_BlogId en los procedimientos almacenados para insertar y actualizar las entradas.</span><span class="sxs-lookup"><span data-stu-id="df576-137">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="df576-138">Reemplazar los valores predeterminados</span><span class="sxs-lookup"><span data-stu-id="df576-138">Overriding the Defaults</span></span>  

<span data-ttu-id="df576-139">Puede cambiar los parámetros de las claves externas que no están incluidas en la clase proporcionando la ruta de acceso a la propiedad de clave principal al método de parámetro.</span><span class="sxs-lookup"><span data-stu-id="df576-139">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="df576-140">Si no tiene una propiedad de navegación en la entidad dependiente (es decir,</span><span class="sxs-lookup"><span data-stu-id="df576-140">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="df576-141">no se puede usar el método Association para identificar el otro extremo de la relación y, después, configurar los parámetros correspondientes a cada una de las propiedades de clave.</span><span class="sxs-lookup"><span data-stu-id="df576-141">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="df576-142">Tokens de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="df576-142">Concurrency Tokens</span></span>  

<span data-ttu-id="df576-143">Los procedimientos almacenados de actualización y eliminación también pueden necesitar tratar la simultaneidad:</span><span class="sxs-lookup"><span data-stu-id="df576-143">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="df576-144">Si la entidad contiene tokens de simultaneidad, el procedimiento almacenado puede tener opcionalmente un parámetro de salida que devuelve el número de filas actualizadas o eliminadas (filas afectadas).</span><span class="sxs-lookup"><span data-stu-id="df576-144">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="df576-145">Este tipo de parámetro se debe configurar mediante el método RowsAffectedParameter.</span><span class="sxs-lookup"><span data-stu-id="df576-145">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="df576-146">De forma predeterminada, EF usa el valor devuelto por ExecuteNonQuery para determinar el número de filas afectadas.</span><span class="sxs-lookup"><span data-stu-id="df576-146">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="df576-147">La especificación de un parámetro de salida de filas afectado es útil si se realiza cualquier lógica en el procedimiento almacenado, lo que daría lugar a que el valor devuelto de ExecuteNonQuery fuera incorrecto (desde la perspectiva del EF) al final de la ejecución.</span><span class="sxs-lookup"><span data-stu-id="df576-147">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="df576-148">Para cada token de simultaneidad habrá un parámetro denominado \*\* \<property_name\> _Original\*\* (por ejemplo, Timestamp_Original).</span><span class="sxs-lookup"><span data-stu-id="df576-148">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="df576-149">Se pasará el valor original de esta propiedad, que es el valor cuando se consulta desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="df576-149">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="df576-150">Los tokens de simultaneidad calculados por la base de datos, como las marcas de tiempo, solo tendrán un parámetro de valor original.</span><span class="sxs-lookup"><span data-stu-id="df576-150">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="df576-151">Las propiedades no calculadas que se establecen como tokens de simultaneidad también tendrán un parámetro para el nuevo valor en el procedimiento de actualización.</span><span class="sxs-lookup"><span data-stu-id="df576-151">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="df576-152">Utiliza las convenciones de nomenclatura ya descritas para los nuevos valores.</span><span class="sxs-lookup"><span data-stu-id="df576-152">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="df576-153">Un ejemplo de este tipo de token sería usar la dirección URL de un blog como un token de simultaneidad, el nuevo valor es necesario porque puede actualizarse a un nuevo valor por el código (a diferencia de un token de marca de tiempo que solo se actualiza en la base de datos).</span><span class="sxs-lookup"><span data-stu-id="df576-153">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="df576-154">Esta es una clase de ejemplo y un procedimiento almacenado de actualización con un token de simultaneidad de marca de tiempo.</span><span class="sxs-lookup"><span data-stu-id="df576-154">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="df576-155">A continuación se muestra una clase de ejemplo y un procedimiento almacenado de actualización con un token de simultaneidad no calculado.</span><span class="sxs-lookup"><span data-stu-id="df576-155">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="df576-156">Reemplazar los valores predeterminados</span><span class="sxs-lookup"><span data-stu-id="df576-156">Overriding the Defaults</span></span>  

<span data-ttu-id="df576-157">Opcionalmente, puede introducir un parámetro de filas afectadas.</span><span class="sxs-lookup"><span data-stu-id="df576-157">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="df576-158">En el caso de tokens de simultaneidad calculados de base de datos, donde solo se pasa el valor original, solo puede usar el mecanismo de cambio de nombre de parámetros estándar para cambiar el nombre del parámetro para el valor original.</span><span class="sxs-lookup"><span data-stu-id="df576-158">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="df576-159">En el caso de los tokens de simultaneidad no calculados, donde se pasan tanto el valor original como el nuevo, puede utilizar una sobrecarga de parámetro que le permita proporcionar un nombre para cada parámetro.</span><span class="sxs-lookup"><span data-stu-id="df576-159">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="df576-160">Relaciones de varios a varios</span><span class="sxs-lookup"><span data-stu-id="df576-160">Many to Many Relationships</span></span>  

<span data-ttu-id="df576-161">Usaremos las clases siguientes como ejemplo en esta sección.</span><span class="sxs-lookup"><span data-stu-id="df576-161">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="df576-162">Las relaciones de varios a varios se pueden asignar a procedimientos almacenados con la siguiente sintaxis.</span><span class="sxs-lookup"><span data-stu-id="df576-162">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="df576-163">Si no se proporciona ninguna otra configuración, se utiliza de forma predeterminada la siguiente forma de procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="df576-163">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="df576-164">Dos procedimientos almacenados denominados \*\* \<type_one\> \<type_two\> _Insert\*\* y \*\* \<type_one\> \<type_two\> _Delete\*\* (por ejemplo, PostTag_Insert y PostTag_Delete).</span><span class="sxs-lookup"><span data-stu-id="df576-164">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="df576-165">Los parámetros serán los valores de clave de cada tipo.</span><span class="sxs-lookup"><span data-stu-id="df576-165">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="df576-166">El nombre de cada parámetro es \*\* \<type_name\> _ \<property_name\> \*\* (por ejemplo, Post_PostId y Tag_TagId).</span><span class="sxs-lookup"><span data-stu-id="df576-166">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="df576-167">Estos son los procedimientos almacenados de inserción y actualización de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="df576-167">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="df576-168">Reemplazar los valores predeterminados</span><span class="sxs-lookup"><span data-stu-id="df576-168">Overriding the Defaults</span></span>  

<span data-ttu-id="df576-169">Los nombres de parámetros y procedimientos se pueden configurar de forma similar a los procedimientos almacenados de entidad.</span><span class="sxs-lookup"><span data-stu-id="df576-169">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
