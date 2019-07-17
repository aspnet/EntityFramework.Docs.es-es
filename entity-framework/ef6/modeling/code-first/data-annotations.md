---
title: 'Código de la primera anotaciones de datos: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: fcd01aef7303573001460b352f8099b2cc6e224a
ms.sourcegitcommit: e90d6cfa3e96f10b8b5275430759a66a0c714ed1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286476"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="fe4cf-102">Anotaciones de datos de Code First</span><span class="sxs-lookup"><span data-stu-id="fe4cf-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="fe4cf-103">**EF4.1 y versiones posteriores solo** -las características, las API, etc. se describe en esta página se introdujeron en Entity Framework 4.1.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="fe4cf-104">Si utiliza una versión anterior, algunos o todos de esta información no es aplicable.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="fe4cf-105">El contenido de esta página se adaptó a partir de un artículo escrito originalmente por Julie Lerman (\<http://thedatafarm.com>).</span><span class="sxs-lookup"><span data-stu-id="fe4cf-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="fe4cf-106">Entity Framework Code First le permite usar sus propias clases de dominio para representar el modelo de EF para realizar las consultas, que se basa en cambiar seguimiento y las funciones de actualización.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="fe4cf-107">Código primero aprovecha el modelo de programación que se conoce como 'convención sobre configuración'.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="fe4cf-108">Código primero supondrá que siguen las convenciones de Entity Framework las clases y, en ese caso, funcionarán automáticamente información sobre cómo realizar su trabajo.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform it's job.</span></span> <span data-ttu-id="fe4cf-109">Sin embargo, si las clases no siguen estas convenciones, tiene la capacidad de agregar las configuraciones a las clases para proporcionar EF con la información necesaria.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="fe4cf-110">Código primero le ofrece dos maneras de agregar estas configuraciones a las clases.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="fe4cf-111">Una es usar atributos simple denominados DataAnnotations y el segundo es usar la API Fluent de Code First, que le brinda una forma de describir las configuraciones de forma imperativa, en el código.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="fe4cf-112">En este artículo se centrará en mediante DataAnnotations (en el espacio de nombres System.ComponentModel.DataAnnotations) para configurar las clases: resaltado de las configuraciones necesarias con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="fe4cf-113">DataAnnotations también se entiende por un número de aplicaciones. NET, como ASP.NET MVC, lo que permite a estas aplicaciones aprovechar las mismas anotaciones para las validaciones del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="fe4cf-114">El modelo</span><span class="sxs-lookup"><span data-stu-id="fe4cf-114">The model</span></span>

<span data-ttu-id="fe4cf-115">Voy a demostrar código DataAnnotations primero con un simple par de clases: Blog y Post.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

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

<span data-ttu-id="fe4cf-116">Tal como están, las clases de Blog y Post cómodamente siguen la convención de primer código y no requieren ajustes para habilitar la compatibilidad EF.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="fe4cf-117">Sin embargo, también puede utilizar las anotaciones para proporcionar más información a EF acerca de las clases y la base de datos a las que se asignan.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="fe4cf-118">Clave</span><span class="sxs-lookup"><span data-stu-id="fe4cf-118">Key</span></span>

<span data-ttu-id="fe4cf-119">Entity Framework se basa en todas las entidades que contienen un valor de clave que se usa para la entidad de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="fe4cf-120">Una convención de Code First es propiedades de clave implícitas; Código primero busca una propiedad denominada "Id" o una combinación de nombre de clase y "Id", por ejemplo, "BlogId".</span><span class="sxs-lookup"><span data-stu-id="fe4cf-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="fe4cf-121">Esta propiedad se asignará a una columna de clave principal en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="fe4cf-122">Las clases Blog y Post siguen esta convención.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="fe4cf-123">¿Qué ocurre si no?</span><span class="sxs-lookup"><span data-stu-id="fe4cf-123">What if they didn’t?</span></span> <span data-ttu-id="fe4cf-124">¿Qué ocurre si Blog usa el nombre *PrimaryTrackingKey* en su lugar, o incluso *foo*?</span><span class="sxs-lookup"><span data-stu-id="fe4cf-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="fe4cf-125">Si el código en primer lugar no encuentra una propiedad que coincide con esta convención producirá una excepción debido a requisitos de Entity Framework que debe tener una propiedad de clave.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="fe4cf-126">Puede usar la anotación de clave para especificar qué propiedad es que se usará como valor EntityKey.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

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

<span data-ttu-id="fe4cf-127">Si está utilizando código en primer lugar es la característica de generación de base de datos, la tabla Blog tendrá una columna de clave principal denominada PrimaryTrackingKey, que también se define como identidad de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![Blog de tabla con la clave principal](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="fe4cf-129">Claves compuestas</span><span class="sxs-lookup"><span data-stu-id="fe4cf-129">Composite keys</span></span>

<span data-ttu-id="fe4cf-130">Entity Framework admite las claves compuestas - claves principales que constan de más de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="fe4cf-131">Por ejemplo, podría tener una clase de Passport cuya clave principal es una combinación de PassportNumber y IssuingCountry.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

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

<span data-ttu-id="fe4cf-132">Al intentar utilizar la clase anterior en el modelo EF produciría un `InvalidOperationException`:</span><span class="sxs-lookup"><span data-stu-id="fe4cf-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="fe4cf-133">*No se puede determinar compuesto clave orden primario para el tipo "Passport". Utilice el ColumnAttribute o el método HasKey para especificar un orden para las claves principales compuestas.*</span><span class="sxs-lookup"><span data-stu-id="fe4cf-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="fe4cf-134">Para poder usar las claves compuestas, Entity Framework requiere que definen el orden de las propiedades de clave.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="fe4cf-135">Puede hacerlo mediante la anotación de columna para especificar un orden.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="fe4cf-136">El valor de orden es relativo (en lugar de index) por lo que se puede usar cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="fe4cf-137">Por ejemplo, 100 y 200 sería aceptable en lugar de 1 y 2.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

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

<span data-ttu-id="fe4cf-138">Si tiene entidades con claves externas compuestas, a continuación, debe especificar la misma columna de ordenación que usa para las propiedades de clave principales correspondientes.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="fe4cf-139">Solo el orden relativo dentro de las propiedades de clave externas debe ser el mismo, los valores exactos que se asigna a **orden** no es necesario hacer coincidir.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="fe4cf-140">Por ejemplo, en la siguiente clase, 3 y 4 podría usarse en lugar de 1 y 2.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

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

## <a name="required"></a><span data-ttu-id="fe4cf-141">Obligatorio</span><span class="sxs-lookup"><span data-stu-id="fe4cf-141">Required</span></span>

<span data-ttu-id="fe4cf-142">La anotación requiere indica a EF que se requiere una propiedad determinada.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-142">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="fe4cf-143">Agregar necesarios para la propiedad Title obligará a EF (y MVC) para asegurarse de que la propiedad tiene datos en ella.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="fe4cf-144">Sin código adicional ni cambios de marcado en la aplicación, una aplicación MVC realizará la validación del lado cliente, crear incluso dinámicamente un mensaje con los nombres de propiedad y la anotación.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-144">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![Crear página con el título es error necesario](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="fe4cf-146">El atributo Required también afectará a la base de datos generado mediante la realización de la propiedad asignada que no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="fe4cf-147">Tenga en cuenta que el campo de título ha cambiado a "not null".</span><span class="sxs-lookup"><span data-stu-id="fe4cf-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="fe4cf-148">En algunos casos puede no ser posible que la columna de la base de datos sea distinto de null, aunque la propiedad es obligatoria.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="fe4cf-149">Por ejemplo, cuando usa una estrategia de herencia de TPH datos para varios tipos se almacena en una sola tabla.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="fe4cf-150">Si un tipo derivado incluye una propiedad necesaria de que la columna no puede hacerse que no aceptan valores NULL ya que no todos los tipos en la jerarquía tienen esta propiedad.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Tabla blogs](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="fe4cf-152">MaxLength y MinLength</span><span class="sxs-lookup"><span data-stu-id="fe4cf-152">MaxLength and MinLength</span></span>

<span data-ttu-id="fe4cf-153">Los atributos MaxLength y MinLength le permiten especificar las validaciones de propiedad adicional, tal como hizo con los necesarios.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-153">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="fe4cf-154">Este es el BloggerName con los requisitos de longitud.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="fe4cf-155">El ejemplo también muestra cómo combinar atributos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fe4cf-156">La anotación MaxLength afectará a la base de datos estableciendo la longitud de la propiedad a 10.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![Tabla de blogs que muestra la longitud máxima en la columna BloggerName](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="fe4cf-158">Anotación de MVC del lado cliente y anotación de EF 4.1 del lado servidor respetan esta validación, crear nuevo dinámicamente un mensaje de error: "El campo BloggerName debe ser un tipo de cadena o matriz con una longitud máxima de '10'." Ese mensaje es un poco largo.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="fe4cf-159">Muchas de las anotaciones le permiten especificar un mensaje de error con el atributo de mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fe4cf-160">También puede especificar el mensaje de error en la anotación requiere.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![Crear página con el mensaje de error personalizado](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="fe4cf-162">NotMapped</span><span class="sxs-lookup"><span data-stu-id="fe4cf-162">NotMapped</span></span>

<span data-ttu-id="fe4cf-163">Convención de primer código dicta que todas las propiedades que son de un tipo de datos admitidos se representan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="fe4cf-164">Pero esto no siempre es el caso en las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="fe4cf-165">Por ejemplo podría tener una propiedad en la clase de Blog que crea un código basado en los campos título y BloggerName.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="fe4cf-166">Esa propiedad se puede crear dinámicamente y no deben almacenarse.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="fe4cf-167">Puede marcar las propiedades que no se asignan a la base de datos con la anotación NotMapped como esta propiedad BlogCode.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

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

 

## <a name="complextype"></a><span data-ttu-id="fe4cf-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="fe4cf-168">ComplexType</span></span>

<span data-ttu-id="fe4cf-169">No es raro para describir las entidades de dominio a través de un conjunto de clases y, luego, colocar esas clases para describir una entidad completa.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="fe4cf-170">Por ejemplo, puede agregar una clase denominada BlogDetails al modelo.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fe4cf-171">Tenga en cuenta que BlogDetails no tiene ningún tipo de propiedad de clave.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-171">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="fe4cf-172">En el diseño controlado por dominio BlogDetails se conoce como un objeto de valor.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-172">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="fe4cf-173">Entity Framework hace referencia a objetos de valor como tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="fe4cf-174">  Tipos complejos no pueden controlarse por sí solos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="fe4cf-175">Sin embargo como una propiedad en la clase de Blog, BlogDetails se realizará un seguimiento como parte de un objeto de Blog.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-175">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="fe4cf-176">En orden para code first para reconocer esto, debe marcar la clase BlogDetails como de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-176">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fe4cf-177">Ahora puede agregar una propiedad en la clase de Blog para representar el BlogDetails para dicho blog.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-177">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="fe4cf-178">En la base de datos, la tabla Blog contendrá todas las propiedades del blog, incluidas las propiedades contenidas en la propiedad BlogDetail.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-178">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="fe4cf-179">De forma predeterminada, cada uno de ellos está precedido por el nombre del tipo complejo, BlogDetail.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-179">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![Tabla de blog con un tipo complejo](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="fe4cf-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="fe4cf-181">ConcurrencyCheck</span></span>

<span data-ttu-id="fe4cf-182">La anotación ConcurrencyCheck le permite marcar una o varias propiedades que se usará para la comprobación de simultaneidad en la base de datos cuando un usuario edita o elimina una entidad.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-182">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="fe4cf-183">Si ha estado trabajando con EF Designer, esto se alinea con la configuración ConcurrencyMode de la propiedad en Fixed.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-183">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="fe4cf-184">Veamos cómo funciona ConcurrencyCheck agregándolo a la propiedad BloggerName.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-184">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fe4cf-185">Cuando se llama a SaveChanges, debido a la anotación ConcurrencyCheck en el campo BloggerName, el valor original de dicha propiedad se usará en la actualización.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-185">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="fe4cf-186">El comando intentará encontrar la fila correcta mediante el filtrado no solo en el valor de clave, sino también en el valor original de BloggerName.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span><span data-ttu-id="fe4cf-187">  Estas son las partes críticas del comando UPDATE enviados a la base de datos, donde puede ver el comando actualizará la fila que tiene un PrimaryTrackingKey es 1 y un BloggerName de "Julie", que es el valor original cuando se recuperó el blog de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="fe4cf-188">Si alguien ha cambiado el nombre de blogger para blog mientras tanto, se producirá un error en esta actualización y obtendrá un DbUpdateConcurrencyException que necesitará para controlar.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="fe4cf-189">Marca de tiempo</span><span class="sxs-lookup"><span data-stu-id="fe4cf-189">TimeStamp</span></span>

<span data-ttu-id="fe4cf-190">Es más habitual utilizar campos rowversion o marca de tiempo para la comprobación de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="fe4cf-191">Pero en lugar de usar la anotación ConcurrencyCheck, puede usar la anotación de marca de tiempo más específica, siempre y cuando el tipo de la propiedad es la matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-191">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="fe4cf-192">Código primero tratará las propiedades de la marca de tiempo del mismo como propiedades ConcurrencyCheck, pero también se asegurará que el campo de base de datos que genera el código en primer lugar es distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-192">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="fe4cf-193">Solo puede tener una propiedad de marca de tiempo en una clase determinada.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="fe4cf-194">Agregando la siguiente propiedad a la clase de Blog:</span><span class="sxs-lookup"><span data-stu-id="fe4cf-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="fe4cf-195">resultados en el código que empiece a crear una columna de marca de tiempo que no aceptan valores NULL en la tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![Blogs de tabla con la columna de marca de tiempo](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="fe4cf-197">Tablas y columnas</span><span class="sxs-lookup"><span data-stu-id="fe4cf-197">Table and Column</span></span>

<span data-ttu-id="fe4cf-198">Si deja que Code First crea la base de datos, desea cambiar el nombre de las tablas y columnas que se está creando.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="fe4cf-199">También puede usar Code First con una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="fe4cf-200">Pero no siempre es el caso de que los nombres de las clases y propiedades en el dominio coinciden con los nombres de las tablas y columnas en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="fe4cf-201">Mi clase se denomina Blog y por convención, código en primer lugar se da por supuesto que esto asignará a una tabla denominada Blogs.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-201">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="fe4cf-202">Si no es así puede especificar el nombre de la tabla con el atributo de tabla.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-202">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="fe4cf-203">Aquí, por ejemplo, la anotación se especifica que el nombre de tabla es InternalBlogs.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-203">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="fe4cf-204">La anotación de la columna es una Adept ha más en la especificación de los atributos de una columna asignada.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-204">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="fe4cf-205">Puede estipular un nombre, tipo de datos o incluso el orden en que una columna aparece en la tabla.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="fe4cf-206">Este es un ejemplo del atributo de columna.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-206">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="fe4cf-207">No confunda el TypeName (atributo) de la columna con el DataType DataAnnotation.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-207">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="fe4cf-208">Tipo de datos es una anotación que se usa para la interfaz de usuario y se omite por Code First.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="fe4cf-209">Esta es la tabla después de que se ha vuelto a generar.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="fe4cf-210">El nombre de la tabla ha cambiado a InternalBlogs y columna de descripción de tipo complejo es ahora BlogDescription.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-210">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="fe4cf-211">Porque se especificó el nombre de la anotación, code first no usará la convención de iniciar el nombre de columna con el nombre del tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![Cambiar el nombre de columna y tabla de blogs](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="fe4cf-213">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="fe4cf-213">DatabaseGenerated</span></span>

<span data-ttu-id="fe4cf-214">Características de una base de datos importantes es la capacidad para que se han calculado las propiedades.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="fe4cf-215">Si está asignando las clases de Code First en las tablas que contienen columnas calculan, no desea que Entity Framework al intentar actualizar las columnas.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="fe4cf-216">Pero desea EF al devolver esos valores de la base de datos después de insertar o actualizar datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="fe4cf-217">Puede usar la anotación DatabaseGenerated para marcar aquellas propiedades en la clase junto con la enumeración calculado.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-217">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="fe4cf-218">Otras enumeraciones son ninguno y la identidad.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-218">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="fe4cf-219">Puede usar la base de datos generada en las columnas byte o marca de tiempo cuando el código está generando en primer lugar la base de datos, en caso contrario, solo debe usar esto al señalar a bases de datos existentes de porque el código en primer lugar no podrá determinar la fórmula para la columna calculada.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="fe4cf-220">Mencionamos anteriormente de forma predeterminada, una propiedad de clave que es un entero se convertirá en una clave de identidad en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="fe4cf-221">Eso sería lo mismo que establecer DatabaseGenerated a DatabaseGeneratedOption.Identity.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-221">That would be the same as setting DatabaseGenerated to DatabaseGeneratedOption.Identity.</span></span> <span data-ttu-id="fe4cf-222">Si no desea que sea una clave de identidad, puede establecer el valor en DatabaseGeneratedOption.None.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-222">If you do not want it to be an identity key, you can set the value to DatabaseGeneratedOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="fe4cf-223">Índice</span><span class="sxs-lookup"><span data-stu-id="fe4cf-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="fe4cf-224">**EF6.1 y versiones posteriores solo** -atributo el índice se introdujo en Entity Framework 6.1.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-224">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="fe4cf-225">Si usa una versión anterior no se aplica la información de esta sección.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="fe4cf-226">Puede crear un índice en una o varias columnas mediante el **IndexAttribute**.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="fe4cf-227">Agregar el atributo a una o varias propiedades impedirán EF crear el índice correspondiente en la base de datos cuando crea la base de datos, o aplicar la técnica scaffolding correspondiente **CreateIndex** llama si usa migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="fe4cf-228">Por ejemplo, se producirá el siguiente código en un índice que se va a crear el **clasificación** columna de la **publicaciones** tabla en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-228">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

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

<span data-ttu-id="fe4cf-229">De forma predeterminada, el índice se denominará **IX\_&lt;nombre de la propiedad&gt;**  (IX\_clasificación en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="fe4cf-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="fe4cf-230">Aunque también puede especificar un nombre para el índice.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="fe4cf-231">El ejemplo siguiente especifica que el índice debe denominarse **PostRatingIndex**.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-231">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="fe4cf-232">De forma predeterminada, los índices no son únicas, pero puede usar el **IsUnique** con el nombre de parámetro para especificar que un índice debe ser único.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-232">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="fe4cf-233">El ejemplo siguiente presenta un índice único en un **usuario**del nombre de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-233">The following example introduces a unique index on a **User**'s login name.</span></span>

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

### <a name="multiple-column-indexes"></a><span data-ttu-id="fe4cf-234">Índices de varias columnas</span><span class="sxs-lookup"><span data-stu-id="fe4cf-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="fe4cf-235">Los índices que abarcan varias columnas se especifican con el mismo nombre en varias anotaciones de índice para una tabla determinada.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="fe4cf-236">Al crear los índices de varias columnas, deberá especificar un orden de las columnas en el índice.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="fe4cf-237">Por ejemplo, el código siguiente crea un índice de varias columna en **clasificación** y **BlogId** llamado **IX\_BlogIdAndRating**.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-237">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="fe4cf-238">**BlogId** es la primera columna en el índice y **clasificación** es el segundo.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-238">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="fe4cf-239">Atributos de relación: InverseProperty y ForeignKey</span><span class="sxs-lookup"><span data-stu-id="fe4cf-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="fe4cf-240">Esta página proporciona información sobre cómo configurar las relaciones en el modelo de Code First usando anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="fe4cf-241">Para obtener información general acerca de las relaciones de EF y cómo obtener acceso y manipular datos mediante relaciones, vea [& Propiedades de navegación de relaciones](~/ef6/fundamentals/relationships.md). \*</span><span class="sxs-lookup"><span data-stu-id="fe4cf-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="fe4cf-242">Convención de primer código que se encargará de las relaciones en el modelo más común, pero hay algunos casos donde necesita ayuda.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="fe4cf-243">Cambiar el nombre de la propiedad de clave en la clase Blog supuso un problema con su relación con Post.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-243">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="fe4cf-244">Al generar la base de datos, código primero ve la propiedad BlogId en la clase de publicación y lo reconoce, la convención que coincide con un nombre de la clase más "Id", como una clave externa a la clase de Blog.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-244">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="fe4cf-245">Pero no hay ninguna propiedad BlogId en la clase de blog.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-245">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="fe4cf-246">La solución para esto consiste en crear una propiedad de navegación en la publicación y use el DataAnnotation externa para código entender primero cómo crear la relación entre las dos clases: mediante la propiedad Post.BlogId —, así como también especificar restricciones en el base de datos.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-246">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

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

<span data-ttu-id="fe4cf-247">La restricción en la base de datos muestra una relación entre InternalBlogs.PrimaryTrackingKey y Posts.BlogId.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-247">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![relación entre InternalBlogs.PrimaryTrackingKey y Posts.BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="fe4cf-249">Se usa el InverseProperty cuando tiene varias relaciones entre las clases.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-249">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="fe4cf-250">En la clase de publicación, puede realizar un seguimiento de quién escribió una entrada de blog, así como quién editó.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-250">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="fe4cf-251">Presentamos dos nuevas propiedades de navegación para la clase de publicación.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="fe4cf-252">También deberá agregar en la clase Person que se hace referencia a estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-252">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="fe4cf-253">La clase de persona tiene propiedades de navegación a la publicación, uno para todas las entradas escritas por la persona y otra para todas las publicaciones actualizadas por esa persona.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-253">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="fe4cf-254">En primer lugar no es capaz de hacer coincidir las propiedades en las dos clases en su propio código.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="fe4cf-255">La tabla de base de datos de publicaciones debe tener una clave externa para la persona CreatedBy y otra para la persona UpdatedBy pero código primero creará cuatro propiedades de clave externa: Persona\_Id, persona\_Id1, CreatedBy\_Id y UpdatedBy\_identificador.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-255">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![Tabla de entradas con claves externas adicionales](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="fe4cf-257">Para corregir estos problemas, puede usar la anotación InverseProperty para especificar la alineación de las propiedades.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-257">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="fe4cf-258">Dado que la propiedad PostsWritten en persona sabe que esto hace referencia al tipo de publicación, compilará la relación para Post.CreatedBy.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-258">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="fe4cf-259">Del mismo modo, se conectará PostsUpdated a Post.UpdatedBy.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-259">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="fe4cf-260">Y código primero no creará las claves externas adicionales.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-260">And code first will not create the extra foreign keys.</span></span>

![Entradas de tabla sin claves externas adicionales](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="fe4cf-262">Resumen</span><span class="sxs-lookup"><span data-stu-id="fe4cf-262">Summary</span></span>

<span data-ttu-id="fe4cf-263">DataAnnotations no sólo permiten describir la validación del lado de cliente y servidor en las clases de código primero, pero también permiten mejorar y corregir incluso las suposiciones que código hará que primero sobre las clases según las convenciones.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="fe4cf-264">Con DataAnnotations puede no solo unidad generación de esquema de base de datos, pero también puede asignar las clases de código primero a una base de datos ya existente.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="fe4cf-265">Mientras están muy flexibles, tenga en cuenta que DataAnnotations se proporcionan solo más normalmente, son necesarios cambios de configuración que puede realizar en las clases de código primero.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="fe4cf-266">Para configurar las clases para algunos de los casos extremos, deberá tener en cuenta el mecanismo de configuración alternativa, la API Fluent de Code First.</span><span class="sxs-lookup"><span data-stu-id="fe4cf-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
