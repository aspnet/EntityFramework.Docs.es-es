---
title: 'API fluida: configuración y asignación de propiedades y tipos: EF6'
description: 'API fluida: configuración y asignación de propiedades y tipos en Entity Framework 6'
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: d49da416b2df5828fce5618cc196e22dfbc2bd34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618050"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>API fluida: configuración y asignación de propiedades y tipos
Al trabajar con Entity Framework Code First el comportamiento predeterminado es asignar las clases POCO a las tablas mediante un conjunto de convenciones incorporadas en EF. Sin embargo, a veces no puede o no desea seguir estas convenciones y debe asignar entidades a un valor distinto del que dictan las convenciones.  

Hay dos formas principales de configurar EF para que use un elemento que no sea convenciones, como [anotaciones](xref:ef6/modeling/code-first/data-annotations) o API fluida de EFS. Las anotaciones solo cubren un subconjunto de la funcionalidad de la API fluida, por lo que hay escenarios de asignación que no se pueden lograr mediante anotaciones. Este artículo está diseñado para demostrar cómo usar la API fluida para configurar las propiedades.  

Normalmente, se tiene acceso a la API fluida de Code First mediante la invalidación del método [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) en [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)derivado. Los ejemplos siguientes están diseñados para mostrar cómo realizar varias tareas con la API fluida y permiten copiar el código y personalizarlo para que se adapte a su modelo; si desea ver el modelo con el que se pueden usar tal cual, se proporciona al final de este artículo.  

## <a name="model-wide-settings"></a>Configuración para todo el modelo  

### <a name="default-schema-ef6-onwards"></a>Esquema predeterminado (EF6 en adelante)  

A partir de EF6, puede usar el método HasDefaultSchema en DbModelBuilder para especificar el esquema de base de datos que se va a usar para todas las tablas, procedimientos almacenados, etc. Esta configuración predeterminada se invalidará para todos los objetos para los que se configure explícitamente un esquema diferente.  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>Convenciones personalizadas (EF6 en adelante)  

A partir de EF6, puede crear sus propias convenciones para complementar las incluidas en Code First. Para obtener más información, vea [convenciones de Code First personalizadas](xref:ef6/modeling/code-first/conventions/custom).  

## <a name="property-mapping"></a>Asignación de propiedades  

El método [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) se utiliza para configurar los atributos de cada propiedad que pertenece a una entidad o un tipo complejo. El método de propiedad se utiliza para obtener un objeto de configuración para una propiedad determinada. Las opciones del objeto de configuración son específicas del tipo que se está configurando; IsUnicode solo está disponible en las propiedades de cadena, por ejemplo.  

### <a name="configuring-a-primary-key"></a>Configuración de una clave principal  

La Convención de Entity Framework para las claves principales es:  

1. La clase define una propiedad cuyo nombre es "ID" o "ID"  
2. o un nombre de clase seguido de "ID" o "ID"  

Para establecer explícitamente una propiedad como clave principal, puede usar el método Haskey (. En el ejemplo siguiente, se usa el método Haskey (para configurar la clave principal InstructorID en el tipo OfficeAssignment.  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>Configuración de una clave principal compuesta  

En el ejemplo siguiente se configuran las propiedades DepartmentID y Name para que sean la clave principal compuesta del tipo Department.  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>Desactivar la identidad de las claves principales numéricas  

En el ejemplo siguiente se establece la propiedad DepartmentID en System. ComponentModel. DataAnnotations. DatabaseGeneratedOption. None para indicar que la base de datos no generará el valor.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>Especificar la longitud máxima de una propiedad  

En el ejemplo siguiente, la propiedad Name no debe tener más de 50 caracteres. Si hace que el valor supere los 50 caracteres, recibirá una excepción [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) . Si Code First crea una base de datos a partir de este modelo, también establecerá la longitud máxima de la columna de nombre en 50 caracteres.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>Configuración de la propiedad para que sea necesaria  

En el ejemplo siguiente, se requiere la propiedad Name. Si no especifica el nombre, obtendrá una excepción DbEntityValidationException. Si Code First crea una base de datos a partir de este modelo, la columna utilizada para almacenar esta propiedad no suele ser que acepte valores NULL.  

> [!NOTE]
> En algunos casos, es posible que no sea posible que la columna de la base de datos no acepte valores NULL, aunque se requiera la propiedad. Por ejemplo, cuando se usa un dato de estrategia de herencia de TPH para varios tipos, se almacena en una sola tabla. Si un tipo derivado incluye una propiedad necesaria, no se puede hacer que la columna no acepte valores NULL, ya que no todos los tipos de la jerarquía tendrán esta propiedad.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>Configuración de un índice en una o más propiedades  

> [!NOTE]
> **EF 6.1 en adelante solo** : el atributo de índice se presentó en Entity Framework 6,1. Si usa una versión anterior, no se aplica la información de esta sección.  

La creación de índices no es compatible de forma nativa con la API fluida, pero puede usar la compatibilidad con **IndexAttribute** a través de la API fluida. Los atributos de índice se procesan mediante la inclusión de una anotación de modelo en el modelo que luego se convierte en un índice de la base de datos más adelante en la canalización. Puede Agregar manualmente estas mismas anotaciones mediante la API fluida.  

La forma más fácil de hacerlo es crear una instancia de **IndexAttribute** que contenga todos los valores para el nuevo índice. Después, puede crear una instancia de **IndexAnnotation** , que es un tipo específico de EF que convertirá la configuración de **IndexAttribute** en una anotación de modelo que se puede almacenar en el modelo de EF. A continuación, se pueden pasar al método **HasColumnAnnotation** en la API fluida, especificando el **Índice** de nombre de la anotación.  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

Para obtener una lista completa de los valores disponibles en **IndexAttribute**, consulte la sección *Índice* de [code First anotaciones de datos](xref:ef6/modeling/code-first/data-annotations). Esto incluye la personalización del nombre del índice, la creación de índices únicos y la creación de índices de varias columnas.  

Puede especificar varias anotaciones de índice en una sola propiedad pasando una matriz de **IndexAttribute** al constructor de **IndexAnnotation**.  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>Especificar no asignar una propiedad CLR a una columna en la base de datos  

En el ejemplo siguiente se muestra cómo especificar que una propiedad de un tipo CLR no está asignada a una columna de la base de datos.  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>Asignar una propiedad CLR a una columna específica en la base de datos  

En el ejemplo siguiente se asigna el nombre de la propiedad CLR a la columna de base de datos DepartmentName.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Cambiar el nombre de una clave externa que no está definida en el modelo  

Si decide no definir una clave externa en un tipo CLR, pero desea especificar el nombre que debe tener en la base de datos, haga lo siguiente:  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>Configurar si una propiedad de cadena admite contenido Unicode  

De forma predeterminada, las cadenas son Unicode (nvarchar en SQL Server). Puede usar el método IsUnicode para especificar que una cadena debe ser de tipo VARCHAR.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>Configurar el tipo de datos de una columna de base de datos  

El método [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) permite la asignación a distintas representaciones del mismo tipo básico. El uso de este método no permite realizar ninguna conversión de los datos en tiempo de ejecución. Tenga en cuenta que IsUnicode es la forma preferida de establecer columnas en VARCHAR, ya que es independiente de la base de datos.  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>Configurar propiedades en un tipo complejo  

Hay dos maneras de configurar las propiedades escalares en un tipo complejo.  

Puede llamar a la propiedad en ComplexTypeConfiguration.  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

También puede usar la notación de puntos para tener acceso a una propiedad de un tipo complejo.  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>Configuración de una propiedad que se va a usar como un token de simultaneidad optimista  

Para especificar que una propiedad de una entidad representa un token de simultaneidad, puede usar el atributo ConcurrencyCheck o el método IsConcurrencyToken.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

También puede usar el método IsRowVersion para configurar la propiedad de modo que sea una versión de fila en la base de datos. Al establecer la propiedad como una versión de fila, se configura automáticamente para que sea un token de simultaneidad optimista.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>Asignación de tipos  

### <a name="specifying-that-a-class-is-a-complex-type"></a>Especificar que una clase es un tipo complejo  

Por Convención, un tipo que no tiene ninguna clave principal especificada se trata como un tipo complejo. Hay algunos escenarios en los que Code First no detectarán un tipo complejo (por ejemplo, si tiene una propiedad denominada ID, pero no significa que sea una clave principal). En tales casos, usaría la API fluida para especificar explícitamente que un tipo es un tipo complejo.  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>Especificar no asignar un tipo de entidad CLR a una tabla de la base de datos  

En el ejemplo siguiente se muestra cómo excluir un tipo CLR para que no se asigne a una tabla de la base de datos.  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>Asignar un tipo de entidad a una tabla específica de la base de datos  

Todas las propiedades de Department se asignarán a las columnas de una tabla denominada t_ Department.  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

También puede especificar el nombre del esquema de la siguiente manera:  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>Asignar la herencia de tabla por jerarquía (TPH)  

En el escenario de asignación TPH, todos los tipos de una jerarquía de herencia se asignan a una sola tabla. Una columna de discriminador se utiliza para identificar el tipo de cada fila. Al crear el modelo con Code First, TPH es la estrategia predeterminada para los tipos que participan en la jerarquía de herencia. De forma predeterminada, la columna discriminadora se agrega a la tabla con el nombre "Discriminator" y el nombre de tipo de CLR de cada tipo de la jerarquía se usa para los valores de discriminador. Puede modificar el comportamiento predeterminado mediante la API fluida.  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>Asignación de la herencia de tabla por tipo (TPT)  

En el escenario de asignación de TPT, todos los tipos se asignan a tablas individuales. Las propiedades que pertenecen solamente a un tipo base o a un tipo derivado se almacenan en una tabla que se asigna a ese tipo. Las tablas que se asignan a tipos derivados también almacenan una clave externa que une la tabla derivada con la tabla base.  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>Asignación de la herencia de la tabla por clase concreta (TPC)  

En el escenario de asignación de TPC, todos los tipos no abstractos de la jerarquía se asignan a tablas individuales. Las tablas que se asignan a las clases derivadas no tienen ninguna relación con la tabla que se asigna a la clase base en la base de datos. Todas las propiedades de una clase, incluidas las propiedades heredadas, se asignan a las columnas de la tabla correspondiente.  

Llame al método MapInheritedProperties para configurar cada tipo derivado. MapInheritedProperties reasigna todas las propiedades heredadas de la clase base a nuevas columnas de la tabla para la clase derivada.  

> [!NOTE]
> Tenga en cuenta que, dado que las tablas que participan en la jerarquía de herencia de TPC no comparten una clave principal, habrá claves de entidad duplicadas al insertar en las tablas que se asignan a las subclases si tiene valores generados por la base de datos con el mismo valor de inicialización de identidad. Para solucionar este problema, puede especificar un valor de inicialización inicial diferente para cada tabla o desactivar la identidad en la propiedad de clave principal. Identity es el valor predeterminado para las propiedades de clave de entero cuando se trabaja con Code First.  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>Asignar propiedades de un tipo de entidad a varias tablas en la base de datos (División de entidades)  

La división de entidades permite que las propiedades de un tipo de entidad se repartan entre varias tablas. En el ejemplo siguiente, la entidad Department se divide en dos tablas: Department y DepartmentDetails. La división de entidades utiliza varias llamadas al método Map para asignar un subconjunto de propiedades a una tabla específica.  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>Asignar varios tipos de entidad a una tabla de la base de datos (División de tablas)  

En el ejemplo siguiente se asignan dos tipos de entidad que comparten una clave principal a una tabla.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>Asignar un tipo de entidad para insertar/actualizar/eliminar procedimientos almacenados (EF6 en adelante)  

A partir de EF6, puede asignar una entidad para usar procedimientos almacenados para INSERT UPDATE y DELETE. Para obtener más información, vea [code First INSERT/UPDATE/DELETE Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).  

## <a name="model-used-in-samples"></a>Modelo usado en los ejemplos  

El siguiente modelo de Code First se usa para los ejemplos de esta página.  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
