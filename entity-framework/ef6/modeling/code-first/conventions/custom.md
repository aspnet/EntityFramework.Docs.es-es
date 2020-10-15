---
title: 'Convenciones de Code First personalizado: EF6'
description: Convenciones de Code First personalizadas en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 5142f58d229dd7743fd4d5c7f0004c080e22c9e4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066465"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="76f59-103">Convenciones de Code First personalizadas</span><span class="sxs-lookup"><span data-stu-id="76f59-103">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="76f59-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="76f59-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="76f59-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="76f59-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="76f59-106">Al utilizar Code First el modelo se calcula a partir de las clases mediante un conjunto de convenciones.</span><span class="sxs-lookup"><span data-stu-id="76f59-106">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="76f59-107">Las [convenciones de Code First](xref:ef6/modeling/code-first/conventions/built-in) predeterminadas determinan aspectos como la propiedad que se convierte en la clave principal de una entidad, el nombre de la tabla a la que se asigna una entidad y la precisión y escala que una columna decimal tiene de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="76f59-107">The default [Code First Conventions](xref:ef6/modeling/code-first/conventions/built-in) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="76f59-108">A veces, estas convenciones predeterminadas no son ideales para el modelo y debe solucionarse configurando muchas entidades individuales mediante anotaciones de datos o la API fluida.</span><span class="sxs-lookup"><span data-stu-id="76f59-108">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="76f59-109">Las convenciones de Code First personalizadas le permiten definir sus propias convenciones que proporcionan valores predeterminados de configuración para el modelo.</span><span class="sxs-lookup"><span data-stu-id="76f59-109">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="76f59-110">En este tutorial, exploraremos los distintos tipos de convenciones personalizadas y cómo crear cada una de ellas.</span><span class="sxs-lookup"><span data-stu-id="76f59-110">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="76f59-111">Convenciones de Model-Based</span><span class="sxs-lookup"><span data-stu-id="76f59-111">Model-Based Conventions</span></span>

<span data-ttu-id="76f59-112">En esta página se tratan las convenciones personalizadas de la API de DbModelBuilder.</span><span class="sxs-lookup"><span data-stu-id="76f59-112">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="76f59-113">Esta API debe ser suficiente para la creación de la mayoría de las convenciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="76f59-113">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="76f59-114">Sin embargo, también existe la posibilidad de crear convenciones basadas en modelos: convenciones que manipulan el modelo final una vez que se crea: para controlar escenarios avanzados.</span><span class="sxs-lookup"><span data-stu-id="76f59-114">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="76f59-115">Para obtener más información, vea [convenciones basadas en modelos](xref:ef6/modeling/code-first/conventions/model).</span><span class="sxs-lookup"><span data-stu-id="76f59-115">For more information, see [Model-Based Conventions](xref:ef6/modeling/code-first/conventions/model).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="76f59-116">Nuestro modelo</span><span class="sxs-lookup"><span data-stu-id="76f59-116">Our Model</span></span>

<span data-ttu-id="76f59-117">Comencemos por definir un modelo simple que podamos usar con nuestras convenciones.</span><span class="sxs-lookup"><span data-stu-id="76f59-117">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="76f59-118">Agregue las clases siguientes al proyecto.</span><span class="sxs-lookup"><span data-stu-id="76f59-118">Add the following classes to your project.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="76f59-119">Introducción a las convenciones personalizadas</span><span class="sxs-lookup"><span data-stu-id="76f59-119">Introducing Custom Conventions</span></span>

<span data-ttu-id="76f59-120">Vamos a escribir una Convención que configura cualquier propiedad denominada clave para que sea la clave principal de su tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="76f59-120">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="76f59-121">Las convenciones se habilitan en el generador de modelos, al que se puede tener acceso invalidando OnModelCreating en el contexto.</span><span class="sxs-lookup"><span data-stu-id="76f59-121">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="76f59-122">Actualice la clase ProductContext como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="76f59-122">Update the ProductContext class as follows:</span></span>

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

<span data-ttu-id="76f59-123">Ahora, cualquier propiedad de nuestro modelo denominado Key se configurará como la clave principal de la entidad de la que forma parte.</span><span class="sxs-lookup"><span data-stu-id="76f59-123">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="76f59-124">También podríamos hacer que nuestras convenciones sean más específicas filtrando por el tipo de propiedad que vamos a configurar:</span><span class="sxs-lookup"><span data-stu-id="76f59-124">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="76f59-125">Esto configurará todas las propiedades que se llaman clave como la clave principal de su entidad, pero solo si son un entero.</span><span class="sxs-lookup"><span data-stu-id="76f59-125">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="76f59-126">Una característica interesante del método IsKey es que es aditiva.</span><span class="sxs-lookup"><span data-stu-id="76f59-126">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="76f59-127">Lo que significa que si llama a IsKey en varias propiedades y se convertirán en parte de una clave compuesta.</span><span class="sxs-lookup"><span data-stu-id="76f59-127">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="76f59-128">La única ADVERTENCIA es que, cuando se especifican varias propiedades para una clave, también se debe especificar un orden para esas propiedades.</span><span class="sxs-lookup"><span data-stu-id="76f59-128">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="76f59-129">Para ello, puede llamar al método HasColumnOrder como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="76f59-129">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="76f59-130">Este código configurará los tipos de nuestro modelo para tener una clave compuesta que consta de la columna de clave int y la columna de nombre de cadena.</span><span class="sxs-lookup"><span data-stu-id="76f59-130">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="76f59-131">Si vemos el modelo en el diseñador, tendría el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="76f59-131">If we view the model in the designer it would look like this:</span></span>

![Clave compuesta](~/ef6/media/compositekey.png)

<span data-ttu-id="76f59-133">Otro ejemplo de convenciones de propiedad consiste en configurar todas las propiedades de fecha y hora de mi modelo para que se asignen al tipo datetime2 en SQL Server en lugar de DateTime.</span><span class="sxs-lookup"><span data-stu-id="76f59-133">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="76f59-134">Puede conseguirlo con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="76f59-134">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="76f59-135">Clases de Convención</span><span class="sxs-lookup"><span data-stu-id="76f59-135">Convention Classes</span></span>

<span data-ttu-id="76f59-136">Otra forma de definir convenciones es usar una clase de Convención para encapsular la Convención.</span><span class="sxs-lookup"><span data-stu-id="76f59-136">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="76f59-137">Cuando se usa una clase de Convención, se crea un tipo que hereda de la clase de Convención en el espacio de nombres System. Data. Entity. ModelConfiguration. Conventions.</span><span class="sxs-lookup"><span data-stu-id="76f59-137">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="76f59-138">Se puede crear una clase de Convención con la Convención datetime2 que se mostró anteriormente haciendo lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="76f59-138">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

<span data-ttu-id="76f59-139">Para indicar a EF que use esta Convención, agréguelo a la colección de convenciones en OnModelCreating, que si ha estado siguiendo con el tutorial tendrá el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="76f59-139">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="76f59-140">Como puede ver, agregamos una instancia de nuestra Convención a la colección de convenciones.</span><span class="sxs-lookup"><span data-stu-id="76f59-140">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="76f59-141">Heredar de la Convención proporciona una manera cómoda de agrupar y compartir convenciones entre equipos o proyectos.</span><span class="sxs-lookup"><span data-stu-id="76f59-141">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="76f59-142">Por ejemplo, podría tener una biblioteca de clases con un conjunto común de convenciones que usan todos los proyectos de la organización.</span><span class="sxs-lookup"><span data-stu-id="76f59-142">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="76f59-143">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="76f59-143">Custom Attributes</span></span>

<span data-ttu-id="76f59-144">Otro gran uso de convenciones es habilitar nuevos atributos que se van a usar al configurar un modelo.</span><span class="sxs-lookup"><span data-stu-id="76f59-144">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="76f59-145">Para ilustrar esto, vamos a crear un atributo que se puede usar para marcar las propiedades de cadena como no Unicode.</span><span class="sxs-lookup"><span data-stu-id="76f59-145">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="76f59-146">Ahora, vamos a crear una Convención para aplicar este atributo a nuestro modelo:</span><span class="sxs-lookup"><span data-stu-id="76f59-146">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="76f59-147">Con esta Convención, podemos agregar el atributo NonUnicode a cualquiera de nuestras propiedades de cadena, lo que significa que la columna de la base de datos se almacenará como varchar en lugar de nvarchar.</span><span class="sxs-lookup"><span data-stu-id="76f59-147">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="76f59-148">Una cuestión que hay que tener en cuenta sobre esta Convención es que si coloca el atributo NonUnicode en un valor distinto de una propiedad de cadena, se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="76f59-148">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="76f59-149">Esto se debe a que no se puede configurar IsUnicode en ningún tipo que no sea una cadena.</span><span class="sxs-lookup"><span data-stu-id="76f59-149">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="76f59-150">Si esto ocurre, puede hacer que la Convención sea más específica, de modo que filtre cualquier cosa que no sea una cadena.</span><span class="sxs-lookup"><span data-stu-id="76f59-150">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="76f59-151">Aunque la Convención anterior funciona para definir atributos personalizados, hay otra API que puede ser mucho más fácil de usar, especialmente cuando se desea usar propiedades de la clase de atributo.</span><span class="sxs-lookup"><span data-stu-id="76f59-151">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="76f59-152">En este ejemplo, vamos a actualizar el atributo y cambiarlo a un atributo IsUnicode, de modo que tenga este aspecto:</span><span class="sxs-lookup"><span data-stu-id="76f59-152">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

<span data-ttu-id="76f59-153">Una vez hecho esto, podemos establecer un valor bool en nuestro atributo para indicar a la Convención si una propiedad debe ser Unicode o no.</span><span class="sxs-lookup"><span data-stu-id="76f59-153">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="76f59-154">Podríamos hacer esto en la Convención que ya hemos tenido acceso a la ClrProperty de la clase de configuración de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="76f59-154">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="76f59-155">Esto es bastante sencillo, pero hay una manera más concisa de lograrlo mediante el uso del método Having de la API de convenciones.</span><span class="sxs-lookup"><span data-stu-id="76f59-155">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="76f59-156">El método having tiene un parámetro de tipo FUNC &lt; PropertyInfo, T &gt; que acepta la PropertyInfo igual que el método Where, pero se espera que devuelva un objeto.</span><span class="sxs-lookup"><span data-stu-id="76f59-156">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="76f59-157">Si el objeto devuelto es null, la propiedad no se configurará, lo que significa que puede filtrar las propiedades con ella como Where, pero es diferente en que también capturará el objeto devuelto y lo pasará al método configure.</span><span class="sxs-lookup"><span data-stu-id="76f59-157">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="76f59-158">Esto funciona de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="76f59-158">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x => x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="76f59-159">Los atributos personalizados no son la única razón para usar el método Having, es útil en cualquier lugar en el que sea necesario saber sobre algo que está filtrando al configurar los tipos o las propiedades.</span><span class="sxs-lookup"><span data-stu-id="76f59-159">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="76f59-160">Configuración de tipos</span><span class="sxs-lookup"><span data-stu-id="76f59-160">Configuring Types</span></span>

<span data-ttu-id="76f59-161">Hasta ahora, todas nuestras convenciones se han realizado para propiedades, pero hay otra área de la API de convenciones para configurar los tipos en el modelo.</span><span class="sxs-lookup"><span data-stu-id="76f59-161">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="76f59-162">La experiencia es similar a las convenciones que hemos encontrado hasta ahora, pero las opciones incluidas en configurar estarán en la entidad en lugar de en el nivel de propiedad.</span><span class="sxs-lookup"><span data-stu-id="76f59-162">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="76f59-163">Una de las cosas que las convenciones de nivel de tipo pueden ser realmente útiles para es cambiar la Convención de nomenclatura de tablas, ya sea para asignar a un esquema existente que difiere del valor predeterminado de EF o para crear una nueva base de datos con una Convención de nomenclatura diferente.</span><span class="sxs-lookup"><span data-stu-id="76f59-163">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="76f59-164">Para ello, primero necesitamos un método que pueda aceptar TypeInfo para un tipo en nuestro modelo y que devuelva el nombre de tabla de ese tipo:</span><span class="sxs-lookup"><span data-stu-id="76f59-164">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="76f59-165">Este método toma un tipo y devuelve una cadena que usa minúsculas con carácter de subrayado en lugar de CamelCase.</span><span class="sxs-lookup"><span data-stu-id="76f59-165">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="76f59-166">En nuestro modelo, esto significa que la clase ProductCategory se asignará a una tabla denominada Product \_ Category en lugar de ProductCategories.</span><span class="sxs-lookup"><span data-stu-id="76f59-166">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="76f59-167">Una vez que tenemos ese método, podemos llamarlo en una Convención similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="76f59-167">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="76f59-168">Esta Convención configura todos los tipos de nuestro modelo para que se asignen al nombre de tabla que se devuelve desde nuestro método GetTableName.</span><span class="sxs-lookup"><span data-stu-id="76f59-168">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="76f59-169">Esta Convención es equivalente a llamar al método ToTable para cada entidad del modelo mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="76f59-169">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="76f59-170">Un aspecto que se debe tener en cuenta es que, cuando se llama a ToTable EF, toma la cadena que se proporciona como el nombre exacto de la tabla, sin la pluralización que normalmente haría al determinar los nombres de tabla.</span><span class="sxs-lookup"><span data-stu-id="76f59-170">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="76f59-171">Esta es la razón por la que el nombre de la tabla de nuestra Convención es la \_ categoría de producto en lugar de las categorías de productos \_ .</span><span class="sxs-lookup"><span data-stu-id="76f59-171">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="76f59-172">Podemos resolver esto en nuestra Convención realizando una llamada al servicio de pluralización por nuestra parte.</span><span class="sxs-lookup"><span data-stu-id="76f59-172">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="76f59-173">En el código siguiente, usaremos la característica de [resolución de dependencias](xref:ef6/fundamentals/configuring/dependency-resolution) agregada en EF6 para recuperar el servicio de pluralización que EF habría usado y pluralando el nombre de la tabla.</span><span class="sxs-lookup"><span data-stu-id="76f59-173">In the following code we will use the [Dependency Resolution](xref:ef6/fundamentals/configuring/dependency-resolution) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> <span data-ttu-id="76f59-174">La versión genérica de GetService es un método de extensión en el espacio de nombres System. Data. Entity. Infrastructure. DependencyResolution; tendrá que agregar una instrucción using al contexto para poder usarlo.</span><span class="sxs-lookup"><span data-stu-id="76f59-174">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="76f59-175">ToTable y herencia</span><span class="sxs-lookup"><span data-stu-id="76f59-175">ToTable and Inheritance</span></span>

<span data-ttu-id="76f59-176">Otro aspecto importante de ToTable es que si asigna explícitamente un tipo a una tabla determinada, puede modificar la estrategia de asignación que utilizará EF.</span><span class="sxs-lookup"><span data-stu-id="76f59-176">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="76f59-177">Si llama a ToTable para cada tipo de una jerarquía de herencia, pasando el nombre de tipo como el nombre de la tabla como hicimos anteriormente, cambiará la estrategia de asignación predeterminada de tabla por jerarquía (TPH) a tabla por tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="76f59-177">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="76f59-178">La mejor manera de describir esto es whith un ejemplo concreto:</span><span class="sxs-lookup"><span data-stu-id="76f59-178">The best way to describe this is whith a concrete example:</span></span>

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

<span data-ttu-id="76f59-179">De forma predeterminada, tanto el empleado como el administrador están asignados a la misma tabla (empleados) en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="76f59-179">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="76f59-180">La tabla contendrá empleados y directivos con una columna de discriminador que le indicará qué tipo de instancia se almacena en cada fila.</span><span class="sxs-lookup"><span data-stu-id="76f59-180">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="76f59-181">Esta es la asignación TPH, ya que hay una sola tabla para la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="76f59-181">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="76f59-182">Sin embargo, si llama a ToTable en ambos Classe, cada tipo se asignará a su propia tabla, también conocida como TPT, ya que cada tipo tiene su propia tabla.</span><span class="sxs-lookup"><span data-stu-id="76f59-182">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="76f59-183">El código anterior se asignará a una estructura de tabla similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="76f59-183">The code above will map to a table structure that looks like the following:</span></span>

![Ejemplo de TPT](~/ef6/media/tptexample.jpg)

<span data-ttu-id="76f59-185">Puede evitar esto y mantener la asignación de TPH predeterminada de dos maneras:</span><span class="sxs-lookup"><span data-stu-id="76f59-185">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="76f59-186">Llame a ToTable con el mismo nombre de tabla para cada tipo de la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="76f59-186">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="76f59-187">Llame a ToTable solo en la clase base de la jerarquía, en el ejemplo que sería Employee.</span><span class="sxs-lookup"><span data-stu-id="76f59-187">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="76f59-188">Orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="76f59-188">Execution Order</span></span>

<span data-ttu-id="76f59-189">Las convenciones funcionan en la última manera de WINS, igual que la API fluida.</span><span class="sxs-lookup"><span data-stu-id="76f59-189">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="76f59-190">Esto significa que, si escribe dos convenciones que configuran la misma opción de la misma propiedad, la última que se ejecuta gana.</span><span class="sxs-lookup"><span data-stu-id="76f59-190">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="76f59-191">Por ejemplo, en el código que aparece debajo de la longitud máxima de todas las cadenas se establece en 500, pero después se configuran todas las propiedades denominadas nombre del modelo para que tengan una longitud máxima de 250.</span><span class="sxs-lookup"><span data-stu-id="76f59-191">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="76f59-192">Dado que la Convención para establecer la longitud máxima en 250 es después de la que establece todas las cadenas en 500, todas las propiedades denominadas Name en nuestro modelo tendrán una MaxLength de 250 mientras que cualquier otra cadena, como descripciones, sería 500.</span><span class="sxs-lookup"><span data-stu-id="76f59-192">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="76f59-193">El uso de convenciones de esta manera significa que puede proporcionar una Convención General para tipos o propiedades en el modelo y, a continuación, reemplazarlos para subconjuntos que son diferentes.</span><span class="sxs-lookup"><span data-stu-id="76f59-193">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="76f59-194">La API fluida y las anotaciones de datos también se pueden usar para invalidar una Convención en casos concretos.</span><span class="sxs-lookup"><span data-stu-id="76f59-194">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="76f59-195">En el ejemplo anterior, si hubiéramos usado la API fluida para establecer la longitud máxima de una propiedad, podríamos haber colocado antes o después de la Convención, ya que la API fluida más específica ganará más allá de la Convención de configuración más general.</span><span class="sxs-lookup"><span data-stu-id="76f59-195">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="76f59-196">Convenciones integradas</span><span class="sxs-lookup"><span data-stu-id="76f59-196">Built-in Conventions</span></span>

<span data-ttu-id="76f59-197">Dado que las convenciones personalizadas podrían verse afectadas por las convenciones de Code First predeterminadas, puede ser útil agregar convenciones para que se ejecuten antes o después de otra convención.</span><span class="sxs-lookup"><span data-stu-id="76f59-197">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="76f59-198">Para ello, puede usar los métodos AddBefore y AddAfter de la colección Conventions en su DbContext derivado.</span><span class="sxs-lookup"><span data-stu-id="76f59-198">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="76f59-199">El código siguiente agregaría la clase de Convención que hemos creado anteriormente para que se ejecute antes de la Convención de detección de claves integrada.</span><span class="sxs-lookup"><span data-stu-id="76f59-199">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="76f59-200">Este será el más utilizado al agregar convenciones que deban ejecutarse antes o después de las convenciones integradas. puede encontrar una lista de las convenciones integradas aquí: [System. Data. Entity. ModelConfiguration. Conventions (espacio de nombres](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)).</span><span class="sxs-lookup"><span data-stu-id="76f59-200">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="76f59-201">También puede quitar las convenciones que no desea que se apliquen al modelo.</span><span class="sxs-lookup"><span data-stu-id="76f59-201">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="76f59-202">Para quitar una Convención, use el método Remove.</span><span class="sxs-lookup"><span data-stu-id="76f59-202">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="76f59-203">Este es un ejemplo de cómo quitar el PluralizingTableNameConvention.</span><span class="sxs-lookup"><span data-stu-id="76f59-203">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
