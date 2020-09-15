---
title: Validación-EF6
description: Validación en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 29f5e22d7f2e388d6e8271a35d10bbf6e318eafa
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073696"
---
# <a name="data-validation"></a>Validación de datos
> [!NOTE]
> **EF 4.1 en adelante solo** : las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 4,1. Si usa una versión anterior, parte o toda la información no se aplica.

El contenido de esta página se adapta a un artículo escrito originalmente por Julia Lerman ( [https://thedatafarm.com](https://thedatafarm.com) ).

Entity Framework proporciona una gran variedad de características de validación que se pueden transmitir a través de una interfaz de usuario para la validación del lado cliente o se pueden usar para la validación del lado servidor. Cuando se usa Code First, se pueden especificar validaciones mediante las configuraciones de anotación o de API fluida. Las validaciones adicionales, y más complejas, se pueden especificar en el código y funcionarán si el modelo es el primero de Code, Model First o Database.

## <a name="the-model"></a>El modelo

Mostraré las validaciones con un par sencillo de clases: blog y post.

``` csharp
public class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public DateTime DateCreated { get; set; }
    public string Content { get; set; }
    public int BlogId { get; set; }
    public ICollection<Comment> Comments { get; set; }
}
```

## <a name="data-annotations"></a>Anotaciones de datos

Code First usa anotaciones del `System.ComponentModel.DataAnnotations` ensamblado como un medio para configurar clases Code First. Entre estas anotaciones se encuentran las que proporcionan reglas como `Required` , `MaxLength` y `MinLength` . Varias aplicaciones cliente de .NET también reconocen estas anotaciones, por ejemplo, ASP.NET MVC. Puede lograr la validación del lado cliente y del lado servidor con estas anotaciones. Por ejemplo, puede forzar que la propiedad título del blog sea una propiedad necesaria.

``` csharp
[Required]
public string Title { get; set; }
```

Sin ningún cambio de código o marcado adicional en la aplicación, una aplicación MVC existente realizará la validación del lado cliente, incluso generando dinámicamente un mensaje usando los nombres de la propiedad y de las anotaciones.

![Figura 1](~/ef6/media/figure01.png)

En el método posterior de esta vista de creación, Entity Framework se usa para guardar el nuevo blog en la base de datos, pero la validación del lado cliente de MVC se desencadena antes de que la aplicación llegue a ese código.

Sin embargo, la validación del lado cliente no es una prueba de viñetas. Los usuarios pueden afectar a las características de su explorador o peor aún, un pirata informático podría usar algún truco para evitar las validaciones de la interfaz de usuario. Sin embargo, Entity Framework también reconocerá la `Required` anotación y la validará.

Una manera sencilla de probarlo es deshabilitar la característica de validación del lado cliente de MVC. Puede hacerlo en el archivo de web.config de la aplicación MVC. La sección appSettings tiene una clave para ClientValidationEnabled. Si se establece esta clave en false, impedirá que la interfaz de usuario realice validaciones.

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

Incluso con la validación del lado cliente deshabilitada, obtendrá la misma respuesta en la aplicación. El mensaje de error "el campo título es obligatorio" se mostrará como antes. A excepción de ahora, será el resultado de la validación del lado servidor. Entity Framework realizará la validación en la `Required` anotación (antes de que se genere un `INSERT` comando para enviarlo a la base de datos) y devolverá el error a MVC, que mostrará el mensaje.

## <a name="fluent-api"></a>API fluida

Puede usar la API fluida de Code First en lugar de anotaciones para obtener el mismo cliente & la validación del lado servidor. En lugar de usar `Required` , lo mostraré con una validación de MaxLength.

Las configuraciones de la API fluida se aplican como Code First está compilando el modelo a partir de las clases. Puede insertar las configuraciones invalidando el método OnModelCreating de la clase DbContext. A continuación se muestra una configuración que especifica que la propiedad BloggerName no puede tener más de 10 caracteres.

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(p => p.BloggerName).HasMaxLength(10);
    }
}
```

Los errores de validación que se produzcan en función de las configuraciones de la API fluida no llegarán automáticamente a la interfaz de usuario, pero puede capturarlos en el código y, a continuación, responder a ellos en consecuencia.

Este es un código de error de control de excepciones en la clase BlogController de la aplicación que captura ese error de validación cuando Entity Framework intenta guardar un blog con un BloggerName que supere el máximo de 10 caracteres.

``` csharp
[HttpPost]
public ActionResult Edit(int id, Blog blog)
{
    try
    {
        db.Entry(blog).State = EntityState.Modified;
        db.SaveChanges();
        return RedirectToAction("Index");
    }
    catch (DbEntityValidationException ex)
    {
        var error = ex.EntityValidationErrors.First().ValidationErrors.First();
        this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
        return View();
    }
}
```

La validación no se vuelve a pasar automáticamente a la vista, que es la razón por la que se usa el código adicional que usa `ModelState.AddModelError` . Esto garantiza que los detalles del error lo convierten en la vista que utilizará a continuación el `ValidationMessageFor` HtmlHelper para mostrar el error.

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject` es una interfaz que reside en `System.ComponentModel.DataAnnotations` . Aunque no forma parte de la API de Entity Framework, todavía puede aprovecharla para la validación del lado servidor en las clases de Entity Framework. `IValidatableObject` proporciona un `Validate` método que Entity Framework llamará durante SaveChanges o se puede llamar a sí mismo en cualquier momento en el que desee validar las clases.

Configuraciones como `Required` y `MaxLength` realizan la validación en un único campo. En el `Validate` método puede tener una lógica aún más compleja, por ejemplo, comparando dos campos.

En el ejemplo siguiente, la `Blog` clase se ha ampliado para implementar `IValidatableObject` y, a continuación, proporciona una regla que `Title` y `BloggerName` no coinciden.

``` csharp
public class Blog : IValidatableObject
{
    public int Id { get; set; }

    [Required]
    public string Title { get; set; }

    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        if (Title == BloggerName)
        {
            yield return new ValidationResult(
                "Blog Title cannot match Blogger Name",
                new[] { nameof(Title), nameof(BloggerName) });
        }
    }
}
```

El `ValidationResult` constructor toma un objeto `string` que representa el mensaje de error y una matriz de objetos `string` que representan los nombres de miembro que están asociados a la validación. Dado que esta validación comprueba `Title` y `BloggerName` , se devuelven ambos nombres de propiedad.

A diferencia de la validación proporcionada por la API fluida, el resultado de la validación será reconocido por la vista y el controlador de excepción que se usó anteriormente para agregar el error `ModelState` no es necesario. Dado que he establecido ambos nombres de propiedad en `ValidationResult` , el HtmlHelpers de MVC muestra el mensaje de error para ambas propiedades.

![figura 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext. ValidateEntity

`DbContext` tiene un método reemplazable denominado `ValidateEntity` . Cuando llame a `SaveChanges` , Entity Framework llamará a este método para cada entidad en su caché cuyo estado no sea `Unchanged` . Puede colocar la lógica de validación directamente aquí o incluso usar este método para llamar a, por ejemplo, el `Blog.Validate` método agregado en la sección anterior.

A continuación se muestra un ejemplo de una `ValidateEntity` invalidación que valida `Post` las nuevas para asegurarse de que el título de la publicación no se ha usado ya. Primero se comprueba si la entidad es una publicación y se agrega su estado. Si ese es el caso, busca en la base de datos para ver si ya existe una publicación con el mismo título. Si ya hay una publicación existente, se crea un nuevo `DbEntityValidationResult` .

`DbEntityValidationResult` aloja un `DbEntityEntry` y un `ICollection<DbValidationErrors>` para una sola entidad. Al principio de este método, `DbEntityValidationResult` se crea una instancia de y, a continuación, se agregan los errores detectados a su `ValidationErrors` colección.

``` csharp
protected override DbEntityValidationResult ValidateEntity (
    System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
    IDictionary<object, object> items)
{
    var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());

    if (entityEntry.Entity is Post post && entityEntry.State == EntityState.Added)
    {
        // Check for uniqueness of post title
        if (Posts.Where(p => p.Title == post.Title).Any())
        {
            result.ValidationErrors.Add(
                    new System.Data.Entity.Validation.DbValidationError(
                        nameof(Title),
                        "Post title must be unique."));
        }
    }

    if (result.ValidationErrors.Count > 0)
    {
        return result;
    }
    else
    {
        return base.ValidateEntity(entityEntry, items);
    }
}
```

## <a name="explicitly-triggering-validation"></a>Desencadenar explícitamente la validación

Una llamada a `SaveChanges` desencadena todas las validaciones que se describen en este artículo. Pero no es necesario que confíe en `SaveChanges` . Puede que prefiera validar cualquier otro lugar de la aplicación.

`DbContext.GetValidationErrors` desencadenará todas las validaciones, las definidas por las anotaciones o la API fluida, la validación creada en `IValidatableObject` (por ejemplo, `Blog.Validate` ) y las validaciones realizadas en el `DbContext.ValidateEntity` método.

El código siguiente llamará a `GetValidationErrors` en la instancia actual de `DbContext` . `ValidationErrors` se agrupan por tipo de entidad en `DbEntityValidationResult` . El código recorre en iteración las `DbEntityValidationResult` s devueltas por el método y, a continuación, a través de cada una de ellas `DbValidationError` .

``` csharp
foreach (var validationResult in db.GetValidationErrors())
{
    foreach (var error in validationResult.ValidationErrors)
    {
        Debug.WriteLine(
            "Entity Property: {0}, Error {1}",
            error.PropertyName,
            error.ErrorMessage);
    }
}
```

## <a name="other-considerations-when-using-validation"></a>Otras consideraciones al usar la validación

Estos son algunos aspectos que se deben tener en cuenta al usar la validación de Entity Framework:

- La carga diferida está deshabilitada durante la validación
- EF validará las anotaciones de datos en propiedades no asignadas (propiedades que no están asignadas a una columna en la base de datos)
- La validación se realiza después de que se detecten los cambios durante `SaveChanges` . Si realiza cambios durante la validación, es su responsabilidad notificar al seguimiento de cambios
- `DbUnexpectedValidationException` se produce si se producen errores durante la validación
- Las caras que Entity Framework incluye en el modelo (longitud máxima, requerida, etc.) provocarán la validación, incluso si no hay ninguna anotación de datos en las clases o si se usó el diseñador de EF para crear el modelo.
- Reglas de prioridad:
  - Las llamadas a la API fluida invalidan las anotaciones de datos correspondientes
- Orden de ejecución:
  - La validación de la propiedad tiene lugar antes de la validación de tipos
  - La validación de tipos solo se produce si la validación de la propiedad se realiza correctamente
- Si una propiedad es compleja, su validación también incluirá:
  - Validación de nivel de propiedad en las propiedades de tipo complejo
  - Validación del nivel de tipo en el tipo complejo, incluida `IValidatableObject` la validación en el tipo complejo

## <a name="summary"></a>Resumen

La API de validación en Entity Framework se reproduce muy bien con la validación del lado cliente en MVC, pero no tiene que depender de la validación del lado cliente. Entity Framework se encargará de la validación en el lado del servidor para las anotaciones o configuraciones que haya aplicado con la API fluida de Code First.

También vio una serie de puntos de extensibilidad para personalizar el comportamiento si usa la `IValidatableObject` interfaz o pulsa en el `DbContext.ValidateEntity` método. Y estos dos últimos métodos de validación están disponibles a través de `DbContext` , independientemente de que use el flujo de trabajo Code First, Model First o Database First para describir el modelo conceptual.
