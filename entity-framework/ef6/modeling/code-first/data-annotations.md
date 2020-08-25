---
title: 'Code First de anotaciones de datos: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 17370b83aee9974161b1771e8a1dc99c2de27f0f
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847623"
---
# <a name="code-first-data-annotations"></a>Anotaciones de datos de Code First
> [!NOTE]
> **EF 4.1 en adelante solo** : las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 4,1. Si usa una versión anterior, no se aplicará parte o toda esta información.

El contenido de esta página se adapta a un artículo escrito originalmente por Julia Lerman ( \<http://thedatafarm.com> ).

Entity Framework Code First le permite usar sus propias clases de dominio para representar el modelo en el que se basa EF para realizar consultas, seguimiento de cambios y funciones de actualización. Code First aprovecha un patrón de programación denominado "Convención sobre configuración". Code First asumirá que las clases siguen las convenciones de Entity Framework y, en ese caso, se descargará automáticamente de cómo realizar su trabajo. Sin embargo, si las clases no siguen esas convenciones, tiene la posibilidad de agregar configuraciones a las clases para proporcionar a EF la información necesaria.

Code First ofrece dos maneras de agregar estas configuraciones a las clases. Uno es el uso de atributos simples denominado DataAnnotations y el segundo es el uso de la API fluida de Code First, que proporciona una manera de describir las configuraciones de forma imperativa, en el código.

En este artículo se centrará en el uso de DataAnnotations (en el espacio de nombres System. ComponentModel. DataAnnotations) para configurar las clases, resaltando las configuraciones más necesarias. Las anotaciones también se entienden en una serie de aplicaciones .NET, como ASP.NET MVC, que permite a estas aplicaciones aprovechar las mismas anotaciones para las validaciones del lado cliente.


## <a name="the-model"></a>El modelo

Mostraré Code First DataAnnotations con un par sencillo de clases: blog y publicación.

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
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

Como están, las clases blog y post siguen la Convención Code First de manera cómoda y no requieren ajustes para habilitar la compatibilidad con EF. Sin embargo, también puede usar las anotaciones para proporcionar más información a EF sobre las clases y la base de datos a las que se asignan.

 

## <a name="key"></a>Clave

Entity Framework se basa en cada entidad que tiene un valor de clave que se usa para el seguimiento de entidades. Una Convención de Code First son las propiedades de clave IMPLÍCITAS; Code First buscará una propiedad denominada "ID" o una combinación de nombre de clase e "ID", como "BlogId". Esta propiedad se asignará a una columna de clave principal en la base de datos.

Las clases blog y post siguen esta Convención. ¿Qué ocurre si no? ¿Qué ocurre si el blog usa el nombre *PrimaryTrackingKey* en su lugar, o incluso *foo*? Si Code First no encuentra una propiedad que coincida con esta Convención, producirá una excepción debido al requisito de Entity Framework que debe tener una propiedad de clave. Puede usar la anotación de clave para especificar qué propiedad se va a usar como EntityKey.

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

Si usa la característica de generación de bases de datos de Code First, la tabla de blog tendrá una columna de clave principal denominada PrimaryTrackingKey, que también se define como Identity de forma predeterminada.

![Tabla de blog con clave principal](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>Claves compuestas

Entity Framework admite claves compuestas: las claves principales que se componen de más de una propiedad. Por ejemplo, podría tener una clase de Passport cuya clave principal sea una combinación de PassportNumber y IssuingCountry.

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Si se intenta usar la clase anterior en el modelo EF, se produciría una `InvalidOperationException` :

*No se puede determinar el orden de claves principales compuesto para el tipo ' Passport '. Use el método ColumnAttribute o Haskey (para especificar un orden para las claves principales compuestas.*

Para usar las claves compuestas, Entity Framework requiere definir un orden para las propiedades de clave. Puede hacerlo mediante la anotación de columna para especificar un orden.

>[!NOTE]
> El valor del orden es relativo (en lugar de basado en índice), por lo que se pueden usar los valores. Por ejemplo, 100 y 200 serían aceptables en lugar de 1 y 2.

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Si tiene entidades con claves externas compuestas, debe especificar la misma ordenación de columnas que usó para las propiedades de clave principal correspondientes.

Solo el orden relativo dentro de las propiedades de clave externa debe ser el mismo, no es necesario que coincidan los valores exactos asignados al **pedido** . Por ejemplo, en la clase siguiente, se podría usar 3 y 4 en lugar de 1 y 2.

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a>Requerido

La `Required` anotación indica a EF que se requiere una propiedad determinada.

Agregar obligatorio a la propiedad title forzará a EF (y MVC) a asegurarse de que la propiedad contiene datos.

``` csharp
    [Required]
    public string Title { get; set; }
```

Sin ningún cambio de código o marcado adicional en la aplicación, una aplicación MVC realizará la validación del lado cliente, incluso generando dinámicamente un mensaje usando los nombres de la propiedad y de las anotaciones.

![La página crear con el título es un error necesario](~/ef6/media/jj591583-figure02.png)

El atributo required también afectará a la base de datos generada haciendo que la propiedad asignada no acepte valores NULL. Observe que el campo title ha cambiado a "not null".

>[!NOTE]
> En algunos casos, es posible que no sea posible que la columna de la base de datos no acepte valores NULL, aunque se requiera la propiedad. Por ejemplo, cuando se usa un dato de estrategia de herencia de TPH para varios tipos, se almacena en una sola tabla. Si un tipo derivado incluye una propiedad necesaria, no se puede hacer que la columna no acepte valores NULL, ya que no todos los tipos de la jerarquía tendrán esta propiedad.

 

![Tabla de blogs](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength y MinLength

Los `MaxLength` `MinLength` atributos y permiten especificar validaciones de propiedades adicionales, tal como se hizo con `Required` .

Este es el BloggerName con los requisitos de longitud. En el ejemplo también se muestra cómo combinar atributos.

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

La anotación MaxLength afectará a la base de datos estableciendo la longitud de la propiedad en 10.

![Tabla de blogs que muestra la longitud máxima de la columna BloggerName](~/ef6/media/jj591583-figure04.png)

La anotación del lado cliente de MVC y la anotación del lado servidor EF 4,1 respetarán esta validación y, de nuevo, se generará dinámicamente un mensaje de error: "el campo BloggerName debe ser una cadena o un tipo de matriz con una longitud máxima de ' 10 '". Ese mensaje es un poco largo. Muchas anotaciones permiten especificar un mensaje de error con el atributo ErrorMessage.

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

También puede especificar ErrorMessage en la anotación requerida.

![Crear página con mensaje de error personalizado](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

La Convención Code First dicta que cada propiedad que es de un tipo de datos admitido se representa en la base de datos. Pero esto no siempre es el caso en las aplicaciones. Por ejemplo, podría tener una propiedad en la clase de blog que crea un código basado en los campos título y BloggerName. Esa propiedad se puede crear dinámicamente y no es necesario almacenarla. Puede marcar las propiedades que no se asignan a la base de datos con la anotación NotMapped como esta propiedad BlogCode.

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a>ComplexType

No es raro describir las entidades de dominio en un conjunto de clases y, a continuación, crear capas de esas clases para describir una entidad completa. Por ejemplo, puede Agregar una clase denominada BlogDetails al modelo.

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

Tenga en cuenta que no `BlogDetails` tiene ningún tipo de propiedad de clave. En el diseño controlado por dominios, `BlogDetails` se conoce como objeto de valor. Entity Framework hace referencia a los objetos de valor como tipos complejos.No se puede realizar un seguimiento de los tipos complejos por su cuenta.

Sin embargo, como una propiedad de la `Blog` clase, se `BlogDetails` realizará un seguimiento como parte de un `Blog` objeto. Para que Code First lo reconozca, debe marcar la `BlogDetails` clase como `ComplexType` .

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

Ahora puede Agregar una propiedad en la `Blog` clase para representar el `BlogDetails` para ese blog.

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

En la base de datos, la tabla contendrá `Blog` todas las propiedades del blog, incluidas las propiedades contenidas en su `BlogDetail` propiedad. De forma predeterminada, cada uno está precedido por el nombre del tipo complejo, "BlogDetail".

![Tabla de blog con tipo complejo](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

La `ConcurrencyCheck` anotación permite marcar una o más propiedades que se usarán para la comprobación de simultaneidad en la base de datos cuando un usuario edita o elimina una entidad. Si ha estado trabajando con EF Designer, esto se alinea con el establecimiento de la propiedad `ConcurrencyMode` en `Fixed` .

Veamos cómo `ConcurrencyCheck` funciona agregándolo a la `BloggerName` propiedad.

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

Cuando `SaveChanges` se llama a, debido a la `ConcurrencyCheck` anotación en el `BloggerName` campo, se usará el valor original de dicha propiedad en la actualización. El comando intentará buscar la fila correcta filtrando no solo en el valor de clave, sino también en el valor original de `BloggerName` .Estas son las partes fundamentales del comando UPDATE que se envía a la base de datos, donde puede ver que el comando actualizará la fila que tiene el `PrimaryTrackingKey` valor 1 y un `BloggerName` de "Julia", que era el valor original cuando el blog se recuperó de la base de datos.

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

Si un usuario ha cambiado el nombre de blogger para ese blog mientras tanto, se producirá un error en esta actualización y obtendrá un **DbUpdateConcurrencyException** que deberá controlar.

 

## <a name="timestamp"></a>TimeStamp

Es más común usar los campos rowversion o TIMESTAMP para la comprobación de simultaneidad. Pero en lugar de utilizar la `ConcurrencyCheck` anotación, puede usar la anotación más específica siempre que `TimeStamp` el tipo de la propiedad sea una matriz de bytes. Code First tratará las `Timestamp` Propiedades igual que `ConcurrencyCheck` las propiedades, pero también se asegurará de que el campo de base de datos generado por Code First no admita valores NULL. Solo puede tener una propiedad timestamp en una clase determinada.

Agregando la siguiente propiedad a la clase de blog:

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

en primer lugar, el código crea una columna de marca de tiempo que no acepta valores NULL en la tabla de base de datos.

![Tabla de blogs con columna de marca de tiempo](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>Tabla y columna

Si va a permitir que Code First cree la base de datos, puede que desee cambiar el nombre de las tablas y columnas que está creando. También puede usar Code First con una base de datos existente. Pero no siempre es el caso de que los nombres de las clases y propiedades de su dominio coincidan con los nombres de las tablas y columnas de la base de datos.

Mi clase se denomina `Blog` y por Convención, Code First presupone que se asignará a una tabla denominada `Blogs` . Si no es así, puede especificar el nombre de la tabla con el `Table` atributo. Aquí, por ejemplo, la anotación está especificando que el nombre de la tabla es **InternalBlogs**.

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

La `Column` anotación es más bien para especificar los atributos de una columna asignada. Puede estipular un nombre, un tipo de datos o incluso el orden en el que aparece una columna en la tabla. Este es un ejemplo del `Column` atributo.

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

No confunda `TypeName` el atributo de la columna con la anotación DataType. DataType es una anotación que se usa para la interfaz de usuario y Code First omitirá.

Esta es la tabla una vez regenerada. El nombre de la tabla ha cambiado a **InternalBlogs** y `Description` la columna del tipo complejo es ahora `BlogDescription` . Dado que el nombre se especificó en la anotación, Code First no usará la Convención de inicio del nombre de columna con el nombre del tipo complejo.

![Nombre de tabla y columna de blogs](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

Una característica importante de las bases de datos es la capacidad de tener propiedades calculadas. Si va a asignar las clases de Code First a tablas que contienen columnas calculadas, no desea que Entity Framework intente actualizar esas columnas. Pero quiere que EF devuelva los valores de la base de datos después de insertar o actualizar los datos. Puede usar la `DatabaseGenerated` anotación para marcar esas propiedades en la clase junto con la `Computed` enumeración. Otras enumeraciones son `None` y `Identity` .

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

Puede usar la base de datos generada en columnas de tipo byte o TIMESTAMP cuando Code First está generando la base de datos; de lo contrario, solo debería usar esta al apuntar a bases de datos existentes porque Code First no podrá determinar la fórmula de la columna calculada.

Antes de que lea de forma predeterminada, una propiedad clave que es un entero se convertirá en una clave de identidad en la base de datos. Es lo mismo que establecer `DatabaseGenerated` en `DatabaseGeneratedOption.Identity` . Si no desea que sea una clave de identidad, puede establecer el valor en `DatabaseGeneratedOption.None` .

 

## <a name="index"></a>Índice

> [!NOTE]
> **EF 6.1 en adelante solo** : el `Index` atributo se presentó en Entity Framework 6,1. Si usa una versión anterior, no se aplica la información de esta sección.

Puede crear un índice en una o más columnas mediante **IndexAttribute**. Si se agrega el atributo a una o varias propiedades, EF creará el índice correspondiente en la base de datos al crear la base de datos, o scaffolding, las llamadas **CreateIndex** correspondientes si usa migraciones de Code First.

Por ejemplo, el código siguiente generará un índice que se creará en la `Rating` columna de la `Posts` tabla de la base de datos.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

De forma predeterminada, el índice se denominará ** \_ &lt; nombre &gt; de propiedad IX** (clasificación IX \_ en el ejemplo anterior). No obstante, también puede especificar un nombre para el índice. En el ejemplo siguiente se especifica que el índice se debe denominar `PostRatingIndex` .

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

De forma predeterminada, los índices no son únicos, pero puede usar el `IsUnique` parámetro con nombre para especificar que un índice debe ser único. En el ejemplo siguiente se presenta un índice único en el `User` nombre de inicio de sesión de.

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a>Índices de varias columnas

Los índices que abarcan varias columnas se especifican utilizando el mismo nombre en varias anotaciones de índice para una tabla determinada. Al crear índices de varias columnas, debe especificar un orden para las columnas en el índice. Por ejemplo, el código siguiente crea un índice de varias columnas en `Rating` y `BlogId` denominado **IX \_ BlogIdAndRating**. `BlogId` es la primera columna del índice y `Rating` es el segundo.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>Atributos de relación: InverseProperty y ForeignKey

> [!NOTE]
> En esta página se proporciona información sobre cómo configurar las relaciones en el modelo de Code First con anotaciones de datos. Para obtener información general sobre las relaciones en EF y cómo obtener acceso a los datos y manipularlos mediante relaciones, vea [relaciones & propiedades de navegación](~/ef6/fundamentals/relationships.md). *

La Convención Code First se encargará de las relaciones más comunes del modelo, pero hay algunos casos en los que necesita ayuda.

Cambiar el nombre de la propiedad de clave en la `Blog` clase creó un problema con su relación con `Post` . 

Al generar la base de datos, Code First ve la `BlogId` propiedad en la clase post y la reconoce, por la Convención que coincide con un nombre de clase más **ID**, como una clave externa a la `Blog` clase. Pero no hay ninguna `BlogId` propiedad en la clase de blog. La solución para esto es crear una propiedad de navegación en `Post` y utilizar la anotación de datos `ForeignKey` para que el código de ayuda comprenda primero cómo crear la relación entre las dos clases (mediante la `Post.BlogId` propiedad) y cómo especificar restricciones en la base de datos.

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

La restricción en la base de datos muestra una relación entre `InternalBlogs.PrimaryTrackingKey` y `Posts.BlogId` . 

![relación entre InternalBlogs. PrimaryTrackingKey y posts. BlogId](~/ef6/media/jj591583-figure09.png)

`InverseProperty`Se usa cuando se tienen varias relaciones entre las clases.

En la `Post` clase, puede que desee realizar un seguimiento de quién escribió una entrada de blog y quién la editó. A continuación se muestran dos nuevas propiedades de navegación para la clase post.

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

También necesitará agregar en la clase a la `Person` que hacen referencia estas propiedades. La `Person` clase tiene propiedades de navegación de vuelta a `Post` , una para todas las publicaciones escritas por la persona y otra para todas las entradas actualizadas por esa persona.

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code First no puede hacer coincidir las propiedades de las dos clases por sí mismas. La tabla de base de datos de `Posts` debe tener una clave externa para la `CreatedBy` persona y otra para la `UpdatedBy` persona, pero Code First creará cuatro propiedades de clave externa: **Person \_ ID**, **Person \_ Id1**, **CreatedBy \_ ID** e **UpdatedBy \_ ID**.

![Tabla de publicaciones con claves externas adicionales](~/ef6/media/jj591583-figure10.png)

Para corregir estos problemas, puede usar la `InverseProperty` anotación para especificar la alineación de las propiedades.

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

Dado `PostsWritten` que la propiedad en persona sabe que esto hace referencia al `Post` tipo, creará la relación con `Post.CreatedBy` . Del mismo modo, se `PostsUpdated` conectará a `Post.UpdatedBy` . Y Code First no crearán las claves externas adicionales.

![Tabla de publicaciones sin claves externas adicionales](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>Resumen

Las DataAnnotations no solo permiten describir la validación del lado cliente y del servidor en las clases Code First, sino que también permiten mejorar e incluso corregir las suposiciones que Code First realizará sobre las clases en función de sus convenciones. Con DataAnnotations no solo puede controlar la generación de esquemas de base de datos, sino que también puede asignar las clases Code First a una base de datos existente previamente.

Aunque son muy flexibles, tenga en cuenta que las anotaciones solo proporcionan los cambios de configuración más necesarios que se pueden realizar en las clases Code First. Para configurar las clases para algunos de los casos extremos, debe buscar el mecanismo de configuración alternativo, la API fluida de Code First.
