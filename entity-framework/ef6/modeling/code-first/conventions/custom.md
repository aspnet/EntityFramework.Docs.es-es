---
title: 'Convenciones de Code First personalizado: EF6'
description: Convenciones de Code First personalizadas en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 69e4b0111394e83195f5c0a81624c7b9e45bda52
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617267"
---
# <a name="custom-code-first-conventions"></a>Convenciones de Code First personalizadas
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

Al utilizar Code First el modelo se calcula a partir de las clases mediante un conjunto de convenciones. Las [convenciones de Code First](xref:ef6/modeling/code-first/conventions/built-in) predeterminadas determinan aspectos como la propiedad que se convierte en la clave principal de una entidad, el nombre de la tabla a la que se asigna una entidad y la precisión y escala que una columna decimal tiene de forma predeterminada.

A veces, estas convenciones predeterminadas no son ideales para el modelo y debe solucionarse configurando muchas entidades individuales mediante anotaciones de datos o la API fluida. Las convenciones de Code First personalizadas le permiten definir sus propias convenciones que proporcionan valores predeterminados de configuración para el modelo. En este tutorial, exploraremos los distintos tipos de convenciones personalizadas y cómo crear cada una de ellas.


## <a name="model-based-conventions"></a>Convenciones basadas en modelos

En esta página se tratan las convenciones personalizadas de la API de DbModelBuilder. Esta API debe ser suficiente para la creación de la mayoría de las convenciones personalizadas. Sin embargo, también existe la posibilidad de crear convenciones basadas en modelos: convenciones que manipulan el modelo final una vez que se crea: para controlar escenarios avanzados. Para obtener más información, vea [convenciones basadas en modelos](xref:ef6/modeling/code-first/conventions/model).

 

## <a name="our-model"></a>Nuestro modelo

Comencemos por definir un modelo simple que podamos usar con nuestras convenciones. Agregue las clases siguientes al proyecto.

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

 

## <a name="introducing-custom-conventions"></a>Introducción a las convenciones personalizadas

Vamos a escribir una Convención que configura cualquier propiedad denominada clave para que sea la clave principal de su tipo de entidad.

Las convenciones se habilitan en el generador de modelos, al que se puede tener acceso invalidando OnModelCreating en el contexto. Actualice la clase ProductContext como se indica a continuación:

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

Ahora, cualquier propiedad de nuestro modelo denominado Key se configurará como la clave principal de la entidad de la que forma parte.

También podríamos hacer que nuestras convenciones sean más específicas filtrando por el tipo de propiedad que vamos a configurar:

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

Esto configurará todas las propiedades que se llaman clave como la clave principal de su entidad, pero solo si son un entero.

Una característica interesante del método IsKey es que es aditiva. Lo que significa que si llama a IsKey en varias propiedades y se convertirán en parte de una clave compuesta. La única ADVERTENCIA es que, cuando se especifican varias propiedades para una clave, también se debe especificar un orden para esas propiedades. Para ello, puede llamar al método HasColumnOrder como se indica a continuación:

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

Este código configurará los tipos de nuestro modelo para tener una clave compuesta que consta de la columna de clave int y la columna de nombre de cadena. Si vemos el modelo en el diseñador, tendría el siguiente aspecto:

![Clave compuesta](~/ef6/media/compositekey.png)

Otro ejemplo de convenciones de propiedad consiste en configurar todas las propiedades de fecha y hora de mi modelo para que se asignen al tipo datetime2 en SQL Server en lugar de DateTime. Puede conseguirlo con lo siguiente:

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>Clases de Convención

Otra forma de definir convenciones es usar una clase de Convención para encapsular la Convención. Cuando se usa una clase de Convención, se crea un tipo que hereda de la clase de Convención en el espacio de nombres System. Data. Entity. ModelConfiguration. Conventions.

Se puede crear una clase de Convención con la Convención datetime2 que se mostró anteriormente haciendo lo siguiente:

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

Para indicar a EF que use esta Convención, agréguelo a la colección de convenciones en OnModelCreating, que si ha estado siguiendo con el tutorial tendrá el siguiente aspecto:

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

Como puede ver, agregamos una instancia de nuestra Convención a la colección de convenciones. Heredar de la Convención proporciona una manera cómoda de agrupar y compartir convenciones entre equipos o proyectos. Por ejemplo, podría tener una biblioteca de clases con un conjunto común de convenciones que usan todos los proyectos de la organización.

 

## <a name="custom-attributes"></a>Atributos personalizados

Otro gran uso de convenciones es habilitar nuevos atributos que se van a usar al configurar un modelo. Para ilustrar esto, vamos a crear un atributo que se puede usar para marcar las propiedades de cadena como no Unicode.

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

Ahora, vamos a crear una Convención para aplicar este atributo a nuestro modelo:

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

Con esta Convención, podemos agregar el atributo NonUnicode a cualquiera de nuestras propiedades de cadena, lo que significa que la columna de la base de datos se almacenará como varchar en lugar de nvarchar.

Una cuestión que hay que tener en cuenta sobre esta Convención es que si coloca el atributo NonUnicode en un valor distinto de una propiedad de cadena, se producirá una excepción. Esto se debe a que no se puede configurar IsUnicode en ningún tipo que no sea una cadena. Si esto ocurre, puede hacer que la Convención sea más específica, de modo que filtre cualquier cosa que no sea una cadena.

Aunque la Convención anterior funciona para definir atributos personalizados, hay otra API que puede ser mucho más fácil de usar, especialmente cuando se desea usar propiedades de la clase de atributo.

En este ejemplo, vamos a actualizar el atributo y cambiarlo a un atributo IsUnicode, de modo que tenga este aspecto:

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

Una vez hecho esto, podemos establecer un valor bool en nuestro atributo para indicar a la Convención si una propiedad debe ser Unicode o no. Podríamos hacer esto en la Convención que ya hemos tenido acceso a la ClrProperty de la clase de configuración de la siguiente manera:

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

Esto es bastante sencillo, pero hay una manera más concisa de lograrlo mediante el uso del método Having de la API de convenciones. El método having tiene un parámetro de tipo FUNC &lt; PropertyInfo, T &gt; que acepta la PropertyInfo igual que el método Where, pero se espera que devuelva un objeto. Si el objeto devuelto es null, la propiedad no se configurará, lo que significa que puede filtrar las propiedades con ella como Where, pero es diferente en que también capturará el objeto devuelto y lo pasará al método configure. Esto funciona de la siguiente manera:

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

Los atributos personalizados no son la única razón para usar el método Having, es útil en cualquier lugar en el que sea necesario saber sobre algo que está filtrando al configurar los tipos o las propiedades.

 

## <a name="configuring-types"></a>Configuración de tipos

Hasta ahora, todas nuestras convenciones se han realizado para propiedades, pero hay otra área de la API de convenciones para configurar los tipos en el modelo. La experiencia es similar a las convenciones que hemos encontrado hasta ahora, pero las opciones incluidas en configurar estarán en la entidad en lugar de en el nivel de propiedad.

Una de las cosas que las convenciones de nivel de tipo pueden ser realmente útiles para es cambiar la Convención de nomenclatura de tablas, ya sea para asignar a un esquema existente que difiere del valor predeterminado de EF o para crear una nueva base de datos con una Convención de nomenclatura diferente. Para ello, primero necesitamos un método que pueda aceptar TypeInfo para un tipo en nuestro modelo y que devuelva el nombre de tabla de ese tipo:

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

Este método toma un tipo y devuelve una cadena que usa minúsculas con carácter de subrayado en lugar de CamelCase. En nuestro modelo, esto significa que la clase ProductCategory se asignará a una tabla denominada Product \_ Category en lugar de ProductCategories.

Una vez que tenemos ese método, podemos llamarlo en una Convención similar a la siguiente:

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

Esta Convención configura todos los tipos de nuestro modelo para que se asignen al nombre de tabla que se devuelve desde nuestro método GetTableName. Esta Convención es equivalente a llamar al método ToTable para cada entidad del modelo mediante la API fluida.

Un aspecto que se debe tener en cuenta es que, cuando se llama a ToTable EF, toma la cadena que se proporciona como el nombre exacto de la tabla, sin la pluralización que normalmente haría al determinar los nombres de tabla. Esta es la razón por la que el nombre de la tabla de nuestra Convención es la \_ categoría de producto en lugar de las categorías de productos \_ . Podemos resolver esto en nuestra Convención realizando una llamada al servicio de pluralización por nuestra parte.

En el código siguiente, usaremos la característica de [resolución de dependencias](xref:ef6/fundamentals/configuring/dependency-resolution) agregada en EF6 para recuperar el servicio de pluralización que EF habría usado y pluralando el nombre de la tabla.

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
> La versión genérica de GetService es un método de extensión en el espacio de nombres System. Data. Entity. Infrastructure. DependencyResolution; tendrá que agregar una instrucción using al contexto para poder usarlo.

### <a name="totable-and-inheritance"></a>ToTable y herencia

Otro aspecto importante de ToTable es que si asigna explícitamente un tipo a una tabla determinada, puede modificar la estrategia de asignación que utilizará EF. Si llama a ToTable para cada tipo de una jerarquía de herencia, pasando el nombre de tipo como el nombre de la tabla como hicimos anteriormente, cambiará la estrategia de asignación predeterminada de tabla por jerarquía (TPH) a tabla por tipo (TPT). La mejor manera de describir esto es whith un ejemplo concreto:

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

De forma predeterminada, tanto el empleado como el administrador están asignados a la misma tabla (empleados) en la base de datos. La tabla contendrá empleados y directivos con una columna de discriminador que le indicará qué tipo de instancia se almacena en cada fila. Esta es la asignación TPH, ya que hay una sola tabla para la jerarquía. Sin embargo, si llama a ToTable en ambos Classe, cada tipo se asignará a su propia tabla, también conocida como TPT, ya que cada tipo tiene su propia tabla.

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

El código anterior se asignará a una estructura de tabla similar a la siguiente:

![Ejemplo de TPT](~/ef6/media/tptexample.jpg)

Puede evitar esto y mantener la asignación de TPH predeterminada de dos maneras:

1.  Llame a ToTable con el mismo nombre de tabla para cada tipo de la jerarquía.
2.  Llame a ToTable solo en la clase base de la jerarquía, en el ejemplo que sería Employee.

 

## <a name="execution-order"></a>Orden de ejecución

Las convenciones funcionan en la última manera de WINS, igual que la API fluida. Esto significa que, si escribe dos convenciones que configuran la misma opción de la misma propiedad, la última que se ejecuta gana. Por ejemplo, en el código que aparece debajo de la longitud máxima de todas las cadenas se establece en 500, pero después se configuran todas las propiedades denominadas nombre del modelo para que tengan una longitud máxima de 250.

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

Dado que la Convención para establecer la longitud máxima en 250 es después de la que establece todas las cadenas en 500, todas las propiedades denominadas Name en nuestro modelo tendrán una MaxLength de 250 mientras que cualquier otra cadena, como descripciones, sería 500. El uso de convenciones de esta manera significa que puede proporcionar una Convención General para tipos o propiedades en el modelo y, a continuación, reemplazarlos para subconjuntos que son diferentes.

La API fluida y las anotaciones de datos también se pueden usar para invalidar una Convención en casos concretos. En el ejemplo anterior, si hubiéramos usado la API fluida para establecer la longitud máxima de una propiedad, podríamos haber colocado antes o después de la Convención, ya que la API fluida más específica ganará más allá de la Convención de configuración más general.

 

## <a name="built-in-conventions"></a>Convenciones integradas

Dado que las convenciones personalizadas podrían verse afectadas por las convenciones de Code First predeterminadas, puede ser útil agregar convenciones para que se ejecuten antes o después de otra convención. Para ello, puede usar los métodos AddBefore y AddAfter de la colección Conventions en su DbContext derivado. El código siguiente agregaría la clase de Convención que hemos creado anteriormente para que se ejecute antes de la Convención de detección de claves integrada.

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

Este será el más utilizado al agregar convenciones que deban ejecutarse antes o después de las convenciones integradas. puede encontrar una lista de las convenciones integradas aquí: [System. Data. Entity. ModelConfiguration. Conventions (espacio de nombres](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)).

También puede quitar las convenciones que no desea que se apliquen al modelo. Para quitar una Convención, use el método Remove. Este es un ejemplo de cómo quitar el PluralizingTableNameConvention.

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
