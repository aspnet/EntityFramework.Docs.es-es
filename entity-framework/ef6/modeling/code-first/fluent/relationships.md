---
title: API fluida-Relationships-EF6
description: 'API fluidas: relaciones en Entity Framework 6'
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 7c62dfd788afda41f4c940836152b9114aa545e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065165"
---
# <a name="fluent-api---relationships"></a>API fluidas: relaciones
> [!NOTE]
> En esta página se proporciona información sobre cómo configurar las relaciones en el modelo de Code First mediante la API fluida. Para obtener información general sobre las relaciones en EF y cómo obtener acceso a los datos y manipularlos mediante relaciones, vea [relaciones & propiedades de navegación](xref:ef6/fundamentals/relationships).  

Cuando se trabaja con Code First, se define el modelo definiendo las clases CLR del dominio. De forma predeterminada, Entity Framework utiliza las convenciones de Code First para asignar las clases al esquema de la base de datos. Si utiliza las convenciones de nomenclatura de Code First, en la mayoría de los casos puede confiar en Code First para configurar las relaciones entre las tablas en función de las claves externas y las propiedades de navegación que defina en las clases. Si no sigue las convenciones al definir las clases, o si desea cambiar la forma en que funcionan las convenciones, puede usar la API fluida o las anotaciones de datos para configurar las clases de modo que Code First pueda asignar las relaciones entre las tablas.  

## <a name="introduction"></a>Introducción  

Al configurar una relación con la API fluida, empiece con la instancia de EntityTypeConfiguration y, a continuación, use el método HasRequired, HasOptional o HasMany para especificar el tipo de relación en la que participa esta entidad. Los métodos HasRequired y HasOptional toman una expresión lambda que representa una propiedad de navegación de referencia. El método HasMany toma una expresión lambda que representa una propiedad de navegación de colección. Después, puede configurar una propiedad de navegación inversa mediante los métodos WithRequired, WithOptional y WithMany. Estos métodos tienen sobrecargas que no toman argumentos y se pueden usar para especificar la cardinalidad con navegaciones unidireccionales.  

Después, puede configurar las propiedades de clave externa mediante el método HasForeignKey. Este método toma una expresión lambda que representa la propiedad que se va a usar como clave externa.  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>Configuración de una relación requerida-to-Optional (uno a cero o uno)  

En el ejemplo siguiente se configura una relación de uno a cero o uno. OfficeAssignment tiene la propiedad InstructorID que es una clave principal y una clave externa, ya que el nombre de la propiedad no sigue la Convención que el método Haskey (utiliza para configurar la clave principal.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>Configurar una relación en la que se requieren ambos extremos (uno a uno)  

En la mayoría de los casos Entity Framework puede deducir qué tipo es el dependiente y cuál es la entidad de seguridad de una relación. Sin embargo, cuando se requieren ambos extremos de la relación o ambos lados son opcionales Entity Framework no puede identificar el dependiente y el principal. Cuando se requieran ambos extremos de la relación, use WithRequiredPrincipal o WithRequiredDependent después del método HasRequired. Cuando ambos extremos de la relación sean opcionales, use WithOptionalPrincipal o WithOptionalDependent después del método HasOptional.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>Configuración de una relación de varios a varios  

El siguiente código configura una relación de varios a varios entre los tipos Course y instructor. En el ejemplo siguiente, se usan las convenciones de Code First predeterminadas para crear una tabla de combinación. Como resultado, la tabla CourseInstructor se crea con Course_CourseID y Instructor_InstructorID columnas.  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

Si desea especificar el nombre de la tabla de combinación y los nombres de las columnas de la tabla, debe realizar una configuración adicional mediante el método map. El código siguiente genera la tabla CourseInstructor con las columnas CourseID y InstructorID.  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
    .Map(m =>
    {
        m.ToTable("CourseInstructor");
        m.MapLeftKey("CourseID");
        m.MapRightKey("InstructorID");
    });
```  

## <a name="configuring-a-relationship-with-one-navigation-property"></a>Configurar una relación con una propiedad de navegación  

Una relación unidireccional (también denominada unidireccional) es cuando se define una propiedad de navegación solo en uno de los extremos de la relación y no en ambos. Por Convención, Code First siempre interpreta una relación unidireccional como uno a varios. Por ejemplo, si desea una relación de uno a uno entre instructor y OfficeAssignment, donde tiene una propiedad de navegación solo en el tipo de instructor, debe usar la API fluida para configurar esta relación.  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>Habilitar la eliminación en cascada  

Puede configurar la eliminación en cascada en una relación mediante el método WillCascadeOnDelete. Si una clave externa de la entidad dependiente no admite valores NULL, Code First establece Cascade delete en la relación. Si una clave externa de la entidad dependiente admite valores NULL, Code First no establece Cascade delete en la relación y, cuando se elimina la entidad de seguridad, la clave externa se establecerá en NULL.  

Puede quitar estas convenciones de eliminación en cascada mediante:  

modelBuilder. Conventions. Remove \<OneToManyCascadeDeleteConvention\> ()  
modelBuilder. Conventions. Remove \<ManyToManyCascadeDeleteConvention\> ()  

El código siguiente configura la relación para que sea necesaria y, a continuación, deshabilita la eliminación en cascada.  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>Configurar una clave externa compuesta  

Si la clave principal del tipo de departamento consta de las propiedades DepartmentID y Name, debe configurar la clave principal para el Departamento y la clave externa en los tipos de curso como se indica a continuación:  

``` csharp
// Composite primary key
modelBuilder.Entity<Department>()
.HasKey(d => new { d.DepartmentID, d.Name });

// Composite foreign key
modelBuilder.Entity<Course>()  
    .HasRequired(c => c.Department)  
    .WithMany(d => d.Courses)
    .HasForeignKey(d => new { d.DepartmentID, d.DepartmentName });
```  

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Cambiar el nombre de una clave externa que no está definida en el modelo  

Si decide no definir una clave externa en el tipo CLR, pero desea especificar el nombre que debe tener en la base de datos, haga lo siguiente:  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>Configuración de un nombre de clave externa que no sigue la Convención de Code First  

Si la propiedad de clave externa de la clase Course se llama SomeDepartmentID en lugar de DepartmentID, deberá hacer lo siguiente para especificar que SomeDepartmentID será la clave externa:  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

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
