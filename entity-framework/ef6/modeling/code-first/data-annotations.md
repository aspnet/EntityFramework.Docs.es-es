---
title: 'Code First de anotaciones de datos: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 9fac2a90c46d78ff5fd632800cc0050276467773
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415860"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="b229d-102">Code First de las anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="b229d-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="b229d-103">**EF 4.1 en adelante solo** : las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 4,1.</span><span class="sxs-lookup"><span data-stu-id="b229d-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="b229d-104">Si usa una versión anterior, no se aplicará parte o toda esta información.</span><span class="sxs-lookup"><span data-stu-id="b229d-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="b229d-105">El contenido de esta página se adapta a un artículo escrito originalmente por Julia Lerman (\<http://thedatafarm.com>).</span><span class="sxs-lookup"><span data-stu-id="b229d-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="b229d-106">Entity Framework Code First le permite usar sus propias clases de dominio para representar el modelo en el que se basa EF para realizar consultas, seguimiento de cambios y funciones de actualización.</span><span class="sxs-lookup"><span data-stu-id="b229d-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="b229d-107">Code First aprovecha un patrón de programación denominado "Convención sobre configuración".</span><span class="sxs-lookup"><span data-stu-id="b229d-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="b229d-108">Code First asumirá que las clases siguen las convenciones de Entity Framework y, en ese caso, se descargará automáticamente de cómo realizar su trabajo.</span><span class="sxs-lookup"><span data-stu-id="b229d-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="b229d-109">Sin embargo, si las clases no siguen esas convenciones, tiene la posibilidad de agregar configuraciones a las clases para proporcionar a EF la información necesaria.</span><span class="sxs-lookup"><span data-stu-id="b229d-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="b229d-110">Code First ofrece dos maneras de agregar estas configuraciones a las clases.</span><span class="sxs-lookup"><span data-stu-id="b229d-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="b229d-111">Uno es el uso de atributos simples denominado DataAnnotations y el segundo es el uso de la API fluida de Code First, que proporciona una manera de describir las configuraciones de forma imperativa, en el código.</span><span class="sxs-lookup"><span data-stu-id="b229d-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="b229d-112">En este artículo se centrará en el uso de DataAnnotations (en el espacio de nombres System. ComponentModel. DataAnnotations) para configurar las clases, resaltando las configuraciones más necesarias.</span><span class="sxs-lookup"><span data-stu-id="b229d-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="b229d-113">Las anotaciones también se entienden en una serie de aplicaciones .NET, como ASP.NET MVC, que permite a estas aplicaciones aprovechar las mismas anotaciones para las validaciones del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="b229d-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="b229d-114">El modelo</span><span class="sxs-lookup"><span data-stu-id="b229d-114">The model</span></span>

<span data-ttu-id="b229d-115">Mostraré Code First DataAnnotations con un par sencillo de clases: blog y publicación.</span><span class="sxs-lookup"><span data-stu-id="b229d-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

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

<span data-ttu-id="b229d-116">Como están, las clases blog y post siguen la Convención Code First de manera cómoda y no requieren ajustes para habilitar la compatibilidad con EF.</span><span class="sxs-lookup"><span data-stu-id="b229d-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="b229d-117">Sin embargo, también puede usar las anotaciones para proporcionar más información a EF sobre las clases y la base de datos a las que se asignan.</span><span class="sxs-lookup"><span data-stu-id="b229d-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="b229d-118">Clave</span><span class="sxs-lookup"><span data-stu-id="b229d-118">Key</span></span>

<span data-ttu-id="b229d-119">Entity Framework se basa en cada entidad que tiene un valor de clave que se usa para el seguimiento de entidades.</span><span class="sxs-lookup"><span data-stu-id="b229d-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="b229d-120">Una Convención de Code First son las propiedades de clave IMPLÍCITAS; Code First buscará una propiedad denominada "ID" o una combinación de nombre de clase e "ID", como "BlogId".</span><span class="sxs-lookup"><span data-stu-id="b229d-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="b229d-121">Esta propiedad se asignará a una columna de clave principal en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="b229d-122">Las clases blog y post siguen esta Convención.</span><span class="sxs-lookup"><span data-stu-id="b229d-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="b229d-123">¿Qué ocurre si no?</span><span class="sxs-lookup"><span data-stu-id="b229d-123">What if they didn’t?</span></span> <span data-ttu-id="b229d-124">¿Qué ocurre si el blog usa el nombre *PrimaryTrackingKey* en su lugar, o incluso *foo*?</span><span class="sxs-lookup"><span data-stu-id="b229d-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="b229d-125">Si Code First no encuentra una propiedad que coincida con esta Convención, producirá una excepción debido al requisito de Entity Framework que debe tener una propiedad de clave.</span><span class="sxs-lookup"><span data-stu-id="b229d-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="b229d-126">Puede usar la anotación de clave para especificar qué propiedad se va a usar como EntityKey.</span><span class="sxs-lookup"><span data-stu-id="b229d-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

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

<span data-ttu-id="b229d-127">Si usa la característica de generación de bases de datos de Code First, la tabla de blog tendrá una columna de clave principal denominada PrimaryTrackingKey, que también se define como Identity de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b229d-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![Tabla de blog con clave principal](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="b229d-129">Claves compuestas</span><span class="sxs-lookup"><span data-stu-id="b229d-129">Composite keys</span></span>

<span data-ttu-id="b229d-130">Entity Framework admite claves compuestas: las claves principales que se componen de más de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="b229d-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="b229d-131">Por ejemplo, podría tener una clase de Passport cuya clave principal sea una combinación de PassportNumber y IssuingCountry.</span><span class="sxs-lookup"><span data-stu-id="b229d-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

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

<span data-ttu-id="b229d-132">Si se intenta usar la clase anterior en el modelo EF, se produciría una `InvalidOperationException`:</span><span class="sxs-lookup"><span data-stu-id="b229d-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="b229d-133">*No se puede determinar el orden de claves principales compuesto para el tipo ' Passport '. Use el método ColumnAttribute o Haskey (para especificar un orden para las claves principales compuestas.*</span><span class="sxs-lookup"><span data-stu-id="b229d-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="b229d-134">Para usar las claves compuestas, Entity Framework requiere definir un orden para las propiedades de clave.</span><span class="sxs-lookup"><span data-stu-id="b229d-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="b229d-135">Puede hacerlo mediante la anotación de columna para especificar un orden.</span><span class="sxs-lookup"><span data-stu-id="b229d-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="b229d-136">El valor del orden es relativo (en lugar de basado en índice), por lo que se pueden usar los valores.</span><span class="sxs-lookup"><span data-stu-id="b229d-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="b229d-137">Por ejemplo, 100 y 200 serían aceptables en lugar de 1 y 2.</span><span class="sxs-lookup"><span data-stu-id="b229d-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

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

<span data-ttu-id="b229d-138">Si tiene entidades con claves externas compuestas, debe especificar la misma ordenación de columnas que usó para las propiedades de clave principal correspondientes.</span><span class="sxs-lookup"><span data-stu-id="b229d-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="b229d-139">Solo el orden relativo dentro de las propiedades de clave externa debe ser el mismo, no es necesario que coincidan los valores exactos asignados al **pedido** .</span><span class="sxs-lookup"><span data-stu-id="b229d-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="b229d-140">Por ejemplo, en la clase siguiente, se podría usar 3 y 4 en lugar de 1 y 2.</span><span class="sxs-lookup"><span data-stu-id="b229d-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

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

## <a name="required"></a><span data-ttu-id="b229d-141">Obligatorio</span><span class="sxs-lookup"><span data-stu-id="b229d-141">Required</span></span>

<span data-ttu-id="b229d-142">La anotación required indica a EF que se requiere una propiedad determinada.</span><span class="sxs-lookup"><span data-stu-id="b229d-142">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="b229d-143">Agregar obligatorio a la propiedad title forzará a EF (y MVC) a asegurarse de que la propiedad contiene datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="b229d-144">Sin ningún cambio de código o marcado adicional en la aplicación, una aplicación MVC realizará la validación del lado cliente, incluso generando dinámicamente un mensaje usando los nombres de la propiedad y de las anotaciones.</span><span class="sxs-lookup"><span data-stu-id="b229d-144">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![La página crear con el título es un error necesario](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="b229d-146">El atributo required también afectará a la base de datos generada haciendo que la propiedad asignada no acepte valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b229d-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="b229d-147">Observe que el campo title ha cambiado a "not null".</span><span class="sxs-lookup"><span data-stu-id="b229d-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="b229d-148">En algunos casos, es posible que no sea posible que la columna de la base de datos no acepte valores NULL, aunque se requiera la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b229d-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="b229d-149">Por ejemplo, cuando se usa un dato de estrategia de herencia de TPH para varios tipos, se almacena en una sola tabla.</span><span class="sxs-lookup"><span data-stu-id="b229d-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="b229d-150">Si un tipo derivado incluye una propiedad necesaria, no se puede hacer que la columna no acepte valores NULL, ya que no todos los tipos de la jerarquía tendrán esta propiedad.</span><span class="sxs-lookup"><span data-stu-id="b229d-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Tabla de blogs](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="b229d-152">MaxLength y MinLength</span><span class="sxs-lookup"><span data-stu-id="b229d-152">MaxLength and MinLength</span></span>

<span data-ttu-id="b229d-153">Los atributos MaxLength y MinLength permiten especificar validaciones de propiedades adicionales, tal y como hizo con required.</span><span class="sxs-lookup"><span data-stu-id="b229d-153">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="b229d-154">Este es el BloggerName con los requisitos de longitud.</span><span class="sxs-lookup"><span data-stu-id="b229d-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="b229d-155">En el ejemplo también se muestra cómo combinar atributos.</span><span class="sxs-lookup"><span data-stu-id="b229d-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="b229d-156">La anotación MaxLength afectará a la base de datos estableciendo la longitud de la propiedad en 10.</span><span class="sxs-lookup"><span data-stu-id="b229d-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![Tabla de blogs que muestra la longitud máxima de la columna BloggerName](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="b229d-158">La anotación del lado cliente de MVC y la anotación del lado servidor EF 4,1 respetarán esta validación y, de nuevo, se generará dinámicamente un mensaje de error: "el campo BloggerName debe ser una cadena o un tipo de matriz con una longitud máxima de ' 10 '". Ese mensaje es un poco largo.</span><span class="sxs-lookup"><span data-stu-id="b229d-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="b229d-159">Muchas anotaciones permiten especificar un mensaje de error con el atributo ErrorMessage.</span><span class="sxs-lookup"><span data-stu-id="b229d-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="b229d-160">También puede especificar ErrorMessage en la anotación requerida.</span><span class="sxs-lookup"><span data-stu-id="b229d-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![Crear página con mensaje de error personalizado](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="b229d-162">NotMapped</span><span class="sxs-lookup"><span data-stu-id="b229d-162">NotMapped</span></span>

<span data-ttu-id="b229d-163">La Convención Code First dicta que cada propiedad que es de un tipo de datos admitido se representa en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="b229d-164">Pero esto no siempre es el caso en las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="b229d-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="b229d-165">Por ejemplo, podría tener una propiedad en la clase de blog que crea un código basado en los campos título y BloggerName.</span><span class="sxs-lookup"><span data-stu-id="b229d-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="b229d-166">Esa propiedad se puede crear dinámicamente y no es necesario almacenarla.</span><span class="sxs-lookup"><span data-stu-id="b229d-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="b229d-167">Puede marcar las propiedades que no se asignan a la base de datos con la anotación NotMapped como esta propiedad BlogCode.</span><span class="sxs-lookup"><span data-stu-id="b229d-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

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

 

## <a name="complextype"></a><span data-ttu-id="b229d-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="b229d-168">ComplexType</span></span>

<span data-ttu-id="b229d-169">No es raro describir las entidades de dominio en un conjunto de clases y, a continuación, crear capas de esas clases para describir una entidad completa.</span><span class="sxs-lookup"><span data-stu-id="b229d-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="b229d-170">Por ejemplo, puede Agregar una clase denominada BlogDetails al modelo.</span><span class="sxs-lookup"><span data-stu-id="b229d-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="b229d-171">Observe que BlogDetails no tiene ningún tipo de propiedad de clave.</span><span class="sxs-lookup"><span data-stu-id="b229d-171">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="b229d-172">En el diseño controlado por dominios, BlogDetails se conoce como un objeto de valor.</span><span class="sxs-lookup"><span data-stu-id="b229d-172">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="b229d-173">Entity Framework hace referencia a los objetos de valor como tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="b229d-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="b229d-174">  No se puede realizar un seguimiento de los tipos complejos por su cuenta.</span><span class="sxs-lookup"><span data-stu-id="b229d-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="b229d-175">Sin embargo, como una propiedad de la clase de blog, BlogDetails se realizará un seguimiento como parte de un objeto de blog.</span><span class="sxs-lookup"><span data-stu-id="b229d-175">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="b229d-176">Para que Code First lo reconozca, debe marcar la clase BlogDetails como ComplexType.</span><span class="sxs-lookup"><span data-stu-id="b229d-176">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="b229d-177">Ahora puede Agregar una propiedad en la clase de blog para representar el BlogDetails de ese blog.</span><span class="sxs-lookup"><span data-stu-id="b229d-177">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="b229d-178">En la base de datos, la tabla de blog contendrá todas las propiedades del blog, incluidas las propiedades contenidas en su propiedad BlogDetail.</span><span class="sxs-lookup"><span data-stu-id="b229d-178">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="b229d-179">De forma predeterminada, cada uno está precedido por el nombre del tipo complejo, BlogDetail.</span><span class="sxs-lookup"><span data-stu-id="b229d-179">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![Tabla de blog con tipo complejo](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="b229d-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="b229d-181">ConcurrencyCheck</span></span>

<span data-ttu-id="b229d-182">La anotación ConcurrencyCheck permite marcar una o varias propiedades que se usarán para la comprobación de simultaneidad en la base de datos cuando un usuario modifica o elimina una entidad.</span><span class="sxs-lookup"><span data-stu-id="b229d-182">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="b229d-183">Si ha trabajado con el diseñador de EF, esto se alinea con el establecimiento de la propiedad ConcurrencyMode en Fixed.</span><span class="sxs-lookup"><span data-stu-id="b229d-183">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="b229d-184">Vamos a ver cómo funciona ConcurrencyCheck agregándolo a la propiedad BloggerName.</span><span class="sxs-lookup"><span data-stu-id="b229d-184">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="b229d-185">Cuando se llama a SaveChanges, debido a la anotación ConcurrencyCheck en el campo BloggerName, se usará el valor original de dicha propiedad en la actualización.</span><span class="sxs-lookup"><span data-stu-id="b229d-185">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="b229d-186">El comando intentará buscar la fila correcta filtrando no solo en el valor de clave, sino también en el valor original de BloggerName.</span><span class="sxs-lookup"><span data-stu-id="b229d-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span><span data-ttu-id="b229d-187">  Estas son las partes fundamentales del comando UPDATE que se envía a la base de datos, donde puede ver que el comando actualizará la fila que tiene un PrimaryTrackingKey es 1 y un BloggerName de "Julia", que era el valor original cuando el blog se recuperó de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="b229d-188">Si un usuario ha cambiado el nombre de blogger para ese blog mientras tanto, se producirá un error en esta actualización y obtendrá un DbUpdateConcurrencyException que deberá controlar.</span><span class="sxs-lookup"><span data-stu-id="b229d-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="b229d-189">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="b229d-189">TimeStamp</span></span>

<span data-ttu-id="b229d-190">Es más común usar los campos rowversion o TIMESTAMP para la comprobación de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="b229d-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="b229d-191">Pero en lugar de utilizar la anotación ConcurrencyCheck, puede utilizar la anotación de marca de tiempo más específica siempre que el tipo de la propiedad sea una matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="b229d-191">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="b229d-192">Code First tratará las propiedades timestamp igual que las propiedades ConcurrencyCheck, pero también se asegurará de que el campo de base de datos generado por Code First no admita valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b229d-192">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="b229d-193">Solo puede tener una propiedad timestamp en una clase determinada.</span><span class="sxs-lookup"><span data-stu-id="b229d-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="b229d-194">Agregando la siguiente propiedad a la clase de blog:</span><span class="sxs-lookup"><span data-stu-id="b229d-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="b229d-195">en primer lugar, el código crea una columna de marca de tiempo que no acepta valores NULL en la tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![Tabla de blogs con columna de marca de tiempo](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="b229d-197">Tabla y columna</span><span class="sxs-lookup"><span data-stu-id="b229d-197">Table and Column</span></span>

<span data-ttu-id="b229d-198">Si va a permitir que Code First cree la base de datos, puede que desee cambiar el nombre de las tablas y columnas que está creando.</span><span class="sxs-lookup"><span data-stu-id="b229d-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="b229d-199">También puede usar Code First con una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="b229d-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="b229d-200">Pero no siempre es el caso de que los nombres de las clases y propiedades de su dominio coincidan con los nombres de las tablas y columnas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="b229d-201">Mi clase se denomina blog y por Convención, Code First presupone que se asignará a una tabla denominada blogs.</span><span class="sxs-lookup"><span data-stu-id="b229d-201">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="b229d-202">Si no es así, puede especificar el nombre de la tabla con el atributo de tabla.</span><span class="sxs-lookup"><span data-stu-id="b229d-202">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="b229d-203">Aquí, por ejemplo, la anotación está especificando que el nombre de la tabla es InternalBlogs.</span><span class="sxs-lookup"><span data-stu-id="b229d-203">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="b229d-204">La anotación de columna es un complemento más bien para especificar los atributos de una columna asignada.</span><span class="sxs-lookup"><span data-stu-id="b229d-204">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="b229d-205">Puede estipular un nombre, un tipo de datos o incluso el orden en el que aparece una columna en la tabla.</span><span class="sxs-lookup"><span data-stu-id="b229d-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="b229d-206">Este es un ejemplo del atributo de columna.</span><span class="sxs-lookup"><span data-stu-id="b229d-206">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="b229d-207">No confunda el atributo TypeName de la columna con la DataAnnotations DataType.</span><span class="sxs-lookup"><span data-stu-id="b229d-207">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="b229d-208">DataType es una anotación que se usa para la interfaz de usuario y Code First omitirá.</span><span class="sxs-lookup"><span data-stu-id="b229d-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="b229d-209">Esta es la tabla una vez regenerada.</span><span class="sxs-lookup"><span data-stu-id="b229d-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="b229d-210">El nombre de la tabla ha cambiado a InternalBlogs y la columna Descripción del tipo complejo ahora es BlogDescription.</span><span class="sxs-lookup"><span data-stu-id="b229d-210">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="b229d-211">Dado que el nombre se especificó en la anotación, Code First no usará la Convención de inicio del nombre de columna con el nombre del tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="b229d-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![Nombre de tabla y columna de blogs](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="b229d-213">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="b229d-213">DatabaseGenerated</span></span>

<span data-ttu-id="b229d-214">Una característica importante de las bases de datos es la capacidad de tener propiedades calculadas.</span><span class="sxs-lookup"><span data-stu-id="b229d-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="b229d-215">Si va a asignar las clases de Code First a tablas que contienen columnas calculadas, no desea que Entity Framework intente actualizar esas columnas.</span><span class="sxs-lookup"><span data-stu-id="b229d-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="b229d-216">Pero quiere que EF devuelva los valores de la base de datos después de insertar o actualizar los datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="b229d-217">Puede usar la anotación DatabaseGenerated para marcar esas propiedades en la clase junto con la enumeración calculada.</span><span class="sxs-lookup"><span data-stu-id="b229d-217">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="b229d-218">Otras enumeraciones son none e Identity.</span><span class="sxs-lookup"><span data-stu-id="b229d-218">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="b229d-219">Puede usar la base de datos generada en columnas de tipo byte o TIMESTAMP cuando Code First está generando la base de datos; de lo contrario, solo debería usar esta al apuntar a bases de datos existentes porque Code First no podrá determinar la fórmula de la columna calculada.</span><span class="sxs-lookup"><span data-stu-id="b229d-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="b229d-220">Antes de que lea de forma predeterminada, una propiedad clave que es un entero se convertirá en una clave de identidad en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="b229d-221">Es lo mismo que establecer DatabaseGenerated en DatabaseGeneratedOption. Identity.</span><span class="sxs-lookup"><span data-stu-id="b229d-221">That would be the same as setting DatabaseGenerated to DatabaseGeneratedOption.Identity.</span></span> <span data-ttu-id="b229d-222">Si no desea que sea una clave de identidad, puede establecer el valor en DatabaseGeneratedOption. None.</span><span class="sxs-lookup"><span data-stu-id="b229d-222">If you do not want it to be an identity key, you can set the value to DatabaseGeneratedOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="b229d-223">Índice</span><span class="sxs-lookup"><span data-stu-id="b229d-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="b229d-224">**EF 6.1 en adelante solo** : el atributo de índice se presentó en Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="b229d-224">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="b229d-225">Si usa una versión anterior, no se aplica la información de esta sección.</span><span class="sxs-lookup"><span data-stu-id="b229d-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="b229d-226">Puede crear un índice en una o más columnas mediante **IndexAttribute**.</span><span class="sxs-lookup"><span data-stu-id="b229d-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="b229d-227">Si se agrega el atributo a una o varias propiedades, EF creará el índice correspondiente en la base de datos al crear la base de datos, o scaffolding, las llamadas **CreateIndex** correspondientes si usa migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="b229d-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="b229d-228">Por ejemplo, el código siguiente generará un índice que se creará en la columna **rating** de la tabla **posts** en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-228">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

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

<span data-ttu-id="b229d-229">De forma predeterminada, el índice se denominará **IX\_&lt;nombre de propiedad&gt;** (IX\_clasificación en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="b229d-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="b229d-230">No obstante, también puede especificar un nombre para el índice.</span><span class="sxs-lookup"><span data-stu-id="b229d-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="b229d-231">En el ejemplo siguiente se especifica que el índice se debe denominar **PostRatingIndex**.</span><span class="sxs-lookup"><span data-stu-id="b229d-231">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="b229d-232">De forma predeterminada, los índices no son únicos, pero puede usar el parámetro con nombre de **IsUnique** para especificar que un índice debe ser único.</span><span class="sxs-lookup"><span data-stu-id="b229d-232">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="b229d-233">En el ejemplo siguiente se presenta un índice único en el nombre de inicio de sesión de un **usuario**.</span><span class="sxs-lookup"><span data-stu-id="b229d-233">The following example introduces a unique index on a **User**'s login name.</span></span>

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

### <a name="multiple-column-indexes"></a><span data-ttu-id="b229d-234">Índices de varias columnas</span><span class="sxs-lookup"><span data-stu-id="b229d-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="b229d-235">Los índices que abarcan varias columnas se especifican utilizando el mismo nombre en varias anotaciones de índice para una tabla determinada.</span><span class="sxs-lookup"><span data-stu-id="b229d-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="b229d-236">Al crear índices de varias columnas, debe especificar un orden para las columnas en el índice.</span><span class="sxs-lookup"><span data-stu-id="b229d-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="b229d-237">Por ejemplo, el código siguiente crea un índice de varias columnas en **rating** y **BlogId** denominado **IX\_BlogIdAndRating**.</span><span class="sxs-lookup"><span data-stu-id="b229d-237">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="b229d-238">**BlogId** es la primera columna del índice y la **clasificación** es la segunda.</span><span class="sxs-lookup"><span data-stu-id="b229d-238">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="b229d-239">Atributos de relación: InverseProperty y ForeignKey</span><span class="sxs-lookup"><span data-stu-id="b229d-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="b229d-240">En esta página se proporciona información sobre cómo configurar las relaciones en el modelo de Code First con anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="b229d-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="b229d-241">Para obtener información general sobre las relaciones en EF y cómo obtener acceso a los datos y manipularlos mediante relaciones, vea [relaciones & propiedades de navegación](~/ef6/fundamentals/relationships.md). \*</span><span class="sxs-lookup"><span data-stu-id="b229d-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="b229d-242">La Convención Code First se encargará de las relaciones más comunes del modelo, pero hay algunos casos en los que necesita ayuda.</span><span class="sxs-lookup"><span data-stu-id="b229d-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="b229d-243">Cambiar el nombre de la propiedad de clave en la clase de blog creó un problema con su relación para publicar.</span><span class="sxs-lookup"><span data-stu-id="b229d-243">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="b229d-244">Al generar la base de datos, Code First ve la propiedad BlogId en la clase post y la reconoce, por la Convención de que coincide con un nombre de clase más "ID", como una clave externa a la clase de blog.</span><span class="sxs-lookup"><span data-stu-id="b229d-244">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="b229d-245">Pero no hay ninguna propiedad BlogId en la clase de blog.</span><span class="sxs-lookup"><span data-stu-id="b229d-245">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="b229d-246">La solución para esto es crear una propiedad de navegación en la publicación y utilizar la anotación de la información externa para que el código de ayuda entienda primero cómo crear la relación entre las dos clases (mediante la propiedad post. BlogId), así como la forma de especificar restricciones en el base.</span><span class="sxs-lookup"><span data-stu-id="b229d-246">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

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

<span data-ttu-id="b229d-247">La restricción en la base de datos muestra una relación entre InternalBlogs. PrimaryTrackingKey y posts. BlogId.</span><span class="sxs-lookup"><span data-stu-id="b229d-247">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![relación entre InternalBlogs. PrimaryTrackingKey y posts. BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="b229d-249">El InverseProperty se usa cuando se tienen varias relaciones entre las clases.</span><span class="sxs-lookup"><span data-stu-id="b229d-249">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="b229d-250">En la clase post, puede que desee realizar un seguimiento de quién escribió una entrada de blog y quién la editó.</span><span class="sxs-lookup"><span data-stu-id="b229d-250">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="b229d-251">A continuación se muestran dos nuevas propiedades de navegación para la clase post.</span><span class="sxs-lookup"><span data-stu-id="b229d-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="b229d-252">También necesitará agregar en la clase Person a la que hacen referencia estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="b229d-252">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="b229d-253">La clase Person tiene propiedades de navegación de vuelta a la publicación, una para todas las publicaciones escritas por la persona y otra para todas las entradas actualizadas por esa persona.</span><span class="sxs-lookup"><span data-stu-id="b229d-253">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="b229d-254">Code First no puede hacer coincidir las propiedades de las dos clases por sí mismas.</span><span class="sxs-lookup"><span data-stu-id="b229d-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="b229d-255">La tabla de base de datos para las publicaciones debe tener una clave externa para la persona de CreatedBy y otra para la persona UpdatedBy, pero Code First creará cuatro propiedades de clave externa: person\_ID, Person\_Id1, CreatedBy\_ID y UpdatedBy\_ID.</span><span class="sxs-lookup"><span data-stu-id="b229d-255">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![Tabla de publicaciones con claves externas adicionales](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="b229d-257">Para corregir estos problemas, puede usar la anotación InverseProperty para especificar la alineación de las propiedades.</span><span class="sxs-lookup"><span data-stu-id="b229d-257">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="b229d-258">Dado que la propiedad PostsWritten de Person sabe que esto hace referencia al tipo de envío, generará la relación con post. CreatedBy.</span><span class="sxs-lookup"><span data-stu-id="b229d-258">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="b229d-259">Del mismo modo, PostsUpdated se conectará a post. UpdatedBy.</span><span class="sxs-lookup"><span data-stu-id="b229d-259">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="b229d-260">Y Code First no crearán las claves externas adicionales.</span><span class="sxs-lookup"><span data-stu-id="b229d-260">And code first will not create the extra foreign keys.</span></span>

![Tabla de publicaciones sin claves externas adicionales](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="b229d-262">Resumen</span><span class="sxs-lookup"><span data-stu-id="b229d-262">Summary</span></span>

<span data-ttu-id="b229d-263">Las DataAnnotations no solo permiten describir la validación del lado cliente y del servidor en las clases Code First, sino que también permiten mejorar e incluso corregir las suposiciones que Code First realizará sobre las clases en función de sus convenciones.</span><span class="sxs-lookup"><span data-stu-id="b229d-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="b229d-264">Con DataAnnotations no solo puede controlar la generación de esquemas de base de datos, sino que también puede asignar las clases Code First a una base de datos existente previamente.</span><span class="sxs-lookup"><span data-stu-id="b229d-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="b229d-265">Aunque son muy flexibles, tenga en cuenta que las anotaciones solo proporcionan los cambios de configuración más necesarios que se pueden realizar en las clases Code First.</span><span class="sxs-lookup"><span data-stu-id="b229d-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="b229d-266">Para configurar las clases para algunos de los casos extremos, debe buscar el mecanismo de configuración alternativo, la API fluida de Code First.</span><span class="sxs-lookup"><span data-stu-id="b229d-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
