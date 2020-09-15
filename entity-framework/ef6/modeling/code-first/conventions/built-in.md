---
title: 'Convenciones de Code First: EF6'
description: Convenciones de Code First en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: 71e865d674d6bef7aaf65d95abe1b1add96d890f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072610"
---
# <a name="code-first-conventions"></a>Convenciones de Code First
Code First le permite describir un modelo mediante el uso de clases de C# o Visual Basic .NET. La forma básica del modelo se detecta mediante el uso de convenciones. Las convenciones son conjuntos de reglas que se utilizan para configurar automáticamente un modelo conceptual basado en definiciones de clase al trabajar con Code First. Las convenciones se definen en el espacio de nombres System. Data. Entity. ModelConfiguration. Conventions.  

Puede seguir configurando el modelo con anotaciones de datos o la API fluida. Se da prioridad a la configuración a través de la API fluida, seguida de las anotaciones de datos y, a continuación, las convenciones. Para obtener más información, vea [anotaciones de datos](xref:ef6/modeling/code-first/data-annotations), [API fluidas, relaciones](xref:ef6/modeling/code-first/fluent/relationships), [tipos de API fluidas & propiedades](xref:ef6/modeling/code-first/fluent/types-and-properties) y [API fluida con VB.net](xref:ef6/modeling/code-first/fluent/vb).  

Puede encontrar una lista detallada de las convenciones de Code First en la documentación de la [API](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx). En este tema se proporciona información general sobre las convenciones utilizadas por Code First.  

## <a name="type-discovery"></a>Detección de tipos  

Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio). Además de definir las clases, también debe dejar que **DbContext** sepa qué tipos quiere incluir en el modelo. Para ello, defina una clase de contexto que derive de **DbContext** y exponga propiedades **DbSet** para los tipos que desea que formen parte del modelo. Code First incluirá estos tipos y también extraerá todos los tipos a los que se hace referencia, aunque los tipos a los que se hace referencia se hayan definido en un ensamblado diferente.  

Si los tipos participan en una jerarquía de herencia, basta con definir una propiedad **DbSet** para la clase base y los tipos derivados se incluirán automáticamente, si están en el mismo ensamblado que la clase base.  

En el ejemplo siguiente, solo hay una propiedad **DbSet** definida en la clase **SchoolEntities** (**departments**). Code First usa esta propiedad para detectar y extraer todos los tipos a los que se hace referencia.  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

Si desea excluir un tipo del modelo, use el atributo **NotMapped** o la API fluida **DbModelBuilder. ignore** .  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>Convención de clave principal  

Code First infiere que una propiedad es una clave principal si una propiedad de una clase se denomina "ID" (no distingue mayúsculas de minúsculas) o el nombre de clase seguido de "ID". Si el tipo de la propiedad de clave principal es numérico o GUID, se configurará como una columna de identidad.  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>Convención de relación  

En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad. Cada objeto puede tener una propiedad de navegación para cada relación en la que participa. Las propiedades de navegación permiten navegar y administrar las relaciones en ambas direcciones, devolviendo un objeto de referencia (si la multiplicidad es uno o cero o uno) o una colección (si la multiplicidad es muchas). Code First deduce las relaciones basadas en las propiedades de navegación definidas en los tipos.  

Además de las propiedades de navegación, se recomienda incluir las propiedades de clave externa en los tipos que representan los objetos dependientes. Cualquier propiedad con el mismo tipo de datos que la propiedad de clave principal principal y con un nombre que sigue a uno de los siguientes formatos representa una clave externa para la relación: ' \<navigation property name\> \<principal primary key property name\> ', ' \<principal class name\> \<primary key property name\> ' o ' \<principal primary key property name\> '. Si se encuentran varias coincidencias, se da prioridad en el orden indicado anteriormente. La detección de clave externa no distingue entre mayúsculas y minúsculas. Cuando se detecta una propiedad de clave externa, Code First deduce la multiplicidad de la relación en función de la nulabilidad de la clave externa. Si la propiedad admite valores NULL, la relación se registra como opcional; de lo contrario, la relación se registra según sea necesario.  

Si una clave externa de la entidad dependiente no admite valores NULL, Code First establece Cascade delete en la relación. Si una clave externa de la entidad dependiente admite valores NULL, Code First no establece Cascade delete en la relación y, cuando se elimina la entidad de seguridad, la clave externa se establecerá en NULL. El comportamiento de la multiplicidad y la eliminación en cascada detectados por la Convención se pueden invalidar mediante la API fluida.  

En el ejemplo siguiente se usan las propiedades de navegación y una clave externa para definir la relación entre las clases Department y Course.  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> Si tiene varias relaciones entre los mismos tipos (por ejemplo, supongamos que define las clases **Person** y **book** , donde la clase **Person** contiene las propiedades de navegación **ReviewedBooks** y **AuthoredBooks** y la clase **book** contiene las propiedades de navegación **autor** y **Revisor** ), debe configurar manualmente las relaciones mediante anotaciones de datos o la API fluida. Para obtener más información, vea [anotaciones de datos: relaciones](xref:ef6/modeling/code-first/data-annotations) y [API fluida](xref:ef6/modeling/code-first/fluent/relationships).  

## <a name="complex-types-convention"></a>Convención de tipos complejos  

Cuando Code First detecta una definición de clase en la que no se puede inferir una clave principal y no se registra ninguna clave principal a través de anotaciones de datos o la API fluida, el tipo se registra automáticamente como un tipo complejo. La detección de tipos complejos también requiere que el tipo no tenga propiedades que hagan referencia a los tipos de entidad y no se haga referencia a ellos desde una propiedad de colección en otro tipo. Dadas las siguientes definiciones de clase Code First deduciría que los **detalles** son un tipo complejo porque no tiene ninguna clave principal.  

``` csharp
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
```  

## <a name="connection-string-convention"></a>Convención de cadena de conexión  

Para obtener información sobre las convenciones que usa DbContext para detectar la conexión que se va a usar [, consulte conexiones y modelos](xref:ef6/fundamentals/configuring/connection-strings).  

## <a name="removing-conventions"></a>Quitar convenciones  

Puede quitar cualquiera de las convenciones definidas en el espacio de nombres System. Data. Entity. ModelConfiguration. Conventions. En el ejemplo siguiente se quita **PluralizingTableNameConvention**.  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a>Convenciones personalizadas  

Las convenciones personalizadas se admiten en EF6 en adelante. Para obtener más información, vea [convenciones de Code First personalizadas](xref:ef6/modeling/code-first/conventions/custom).
