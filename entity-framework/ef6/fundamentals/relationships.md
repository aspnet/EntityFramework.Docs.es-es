---
title: 'Relaciones, propiedades de navegación y claves externas: EF6'
description: Relaciones, propiedades de navegación y claves externas en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 63349d9a81065ea4e15a5f97ef5298cb3dc67339
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618394"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relaciones, propiedades de navegación y claves externas

En este artículo se proporciona información general sobre cómo Entity Framework administra las relaciones entre entidades. También proporciona instrucciones sobre cómo asignar y manipular relaciones.

## <a name="relationships-in-ef"></a>Relaciones en EF

En las bases de datos relacionales, las relaciones (también denominadas asociaciones) entre las tablas se definen mediante claves externas. Una clave externa (FK) es una columna o combinación de columnas que se utiliza para establecer y exigir un vínculo entre los datos de dos tablas. Por lo general, hay tres tipos de relaciones: uno a uno, uno a varios y varios a varios. En una relación de uno a varios, la clave externa se define en la tabla que representa el extremo de la relación. La relación de varios a varios implica definir una tercera tabla (denominada tabla de unión o de combinación), cuya clave principal se compone de las claves externas de ambas tablas relacionadas. En una relación uno a uno, la clave principal actúa además como clave externa y no hay ninguna columna de clave externa independiente para cualquiera de las tablas.

En la imagen siguiente se muestran dos tablas que participan en una relación de uno a varios. La tabla **Course** es la tabla dependiente porque contiene la columna **departmentId** que la vincula a la tabla **Department** .

![Tablas de departamento y Course](~/ef6/media/database2.png)

En Entity Framework, una entidad se puede relacionar con otras entidades a través de una asociación o relación. Cada relación contiene dos extremos que describen el tipo de entidad y la multiplicidad del tipo (uno, cero o uno o varios) para las dos entidades de esa relación. La relación puede venir gobernada por una restricción referencial, la cual describe qué extremo de la relación constituye un rol principal y cuál es un rol dependiente.

Las propiedades de navegación proporcionan una manera de navegar por una asociación entre dos tipos de entidad. Cada objeto puede tener una propiedad de navegación para cada relación en la que participa. Las propiedades de navegación permiten navegar y administrar las relaciones en ambas direcciones, devolviendo un objeto de referencia (si la multiplicidad es uno o cero o uno) o una colección (si la multiplicidad es muchas). También puede optar por tener una navegación unidireccional, en cuyo caso definirá la propiedad de navegación solo en uno de los tipos que participan en la relación y no en ambos.

Se recomienda incluir las propiedades en el modelo que se asignan a las claves externas en la base de datos. Con las propiedades de clave externa incluidas, puede crear o cambiar una relación modificando el valor de clave externa sobre un objeto dependiente. Este tipo de asociación se denomina asociación de clave externa. El uso de claves externas es incluso más importante cuando se trabaja con entidades desconectadas. Tenga en cuenta que al trabajar con de 1 a 1 o de 1 a 0. 1 relaciones, no hay ninguna columna de clave externa independiente, la propiedad de clave principal actúa como clave externa y siempre se incluye en el modelo.

Cuando las columnas de clave externa no se incluyen en el modelo, la información de asociación se administra como un objeto independiente. Se realiza un seguimiento de las relaciones a través de referencias de objeto en lugar de propiedades de clave externa. Este tipo de asociación se denomina *asociación independiente*. La manera más común de modificar una *asociación independiente* es modificar las propiedades de navegación que se generan para cada entidad que participa en la asociación.

Puede decidir entre utilizar uno o ambos tipos de asociaciones en su modelo. Sin embargo, si tiene una relación de varios a varios pura que está conectada mediante una tabla de combinación que solo contiene claves externas, el EF usará una asociación independiente para administrar dicha relación de varios a varios.   

La siguiente imagen muestra un modelo conceptual que se creó con el Entity Framework Designer. El modelo contiene dos entidades que participan en una relación de uno a varios. Ambas entidades tienen propiedades de navegación. **Course** es la entidad dependiente y tiene definida la propiedad de clave externa **departmentId** .

![Tablas de departamento y Course con propiedades de navegación](~/ef6/media/relationshipefdesigner.png)

En el fragmento de código siguiente se muestra el mismo modelo que se creó con Code First.

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a>Configurar o asignar relaciones

En el resto de esta página se explica cómo obtener acceso a los datos y cómo manipularlos mediante relaciones. Para obtener información sobre cómo configurar las relaciones en el modelo, vea las páginas siguientes.

-   Para configurar relaciones en Code First, vea [anotaciones de datos](xref:ef6/modeling/code-first/data-annotations) y [API fluida: relaciones](xref:ef6/modeling/code-first/fluent/relationships).
-   Para configurar relaciones mediante el Entity Framework Designer, consulte [relaciones con el diseñador de EF](xref:ef6/modeling/designer/relationships).

## <a name="creating-and-modifying-relationships"></a>Crear y modificar relaciones

En una *Asociación de clave externa*, al cambiar la relación, el estado de un objeto dependiente con un `EntityState.Unchanged` estado cambia a `EntityState.Modified` . En una relación independiente, al cambiar la relación no se actualiza el estado del objeto dependiente.

En los siguientes ejemplos se muestra cómo usar las propiedades de clave externa y las propiedades de navegación para asociar los objetos relacionados. Con las asociaciones de clave externa, puede usar cualquiera de los métodos para cambiar, crear o modificar las relaciones. Con asociaciones independientes, no puede utilizar la propiedad de clave externa.

- Mediante la asignación de un nuevo valor a una propiedad de clave externa, como en el ejemplo siguiente.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- En el código siguiente se quita una relación estableciendo la clave externa en **null**. Tenga en cuenta que la propiedad de clave externa debe admitir valores NULL.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Si la referencia se encuentra en el estado Added (en este ejemplo, el objeto Course), la propiedad de navegación Reference no se sincronizará con los valores de clave de un nuevo objeto hasta que se llame a SaveChanges. La sincronización no se produce porque el contexto del objeto no contiene claves permanentes para objetos agregados hasta que se guardan. Si debe tener nuevos objetos sincronizados completamente en cuanto establezca la relación, use uno de los métodos siguientes. *

- Asignando un nuevo objeto a una propiedad de navegación. En el código siguiente se crea una relación entre un curso y un `department` . Si los objetos están asociados al contexto, `course` también se agrega a la `department.Courses` colección y la propiedad de clave externa correspondiente en el `course` objeto se establece en el valor de la propiedad clave del Departamento.  
  ``` csharp
  course.Department = department;
  ```

- Para eliminar la relación, establezca la propiedad de navegación en `null` . Si está trabajando con Entity Framework basado en .NET 4,0, el extremo relacionado debe cargarse antes de establecerlo en NULL. Por ejemplo:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  A partir de Entity Framework 5,0, que se basa en .NET 4,5, puede establecer la relación en NULL sin cargar el extremo relacionado. También puede establecer el valor actual en NULL mediante el método siguiente.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Eliminando o agregando un objeto en una colección de entidades. Por ejemplo, puede Agregar un objeto de tipo `Course` a la `department.Courses` colección. Esta operación crea una relación entre un **curso** determinado y un determinado `department` . Si los objetos están asociados al contexto, la referencia de departamento y la propiedad de clave externa en el objeto **Course** se establecerán en el adecuado `department` .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Mediante el `ChangeRelationshipState` método para cambiar el estado de la relación especificada entre dos objetos entidad. Este método se utiliza normalmente cuando se trabaja con aplicaciones de N niveles y una *asociación independiente* (no se puede usar con una asociación de clave externa). Además, para usar este método, debe desplegar en `ObjectContext` , como se muestra en el ejemplo siguiente.  
En el ejemplo siguiente, hay una relación de varios a varios entre instructores y cursos. Llamar al `ChangeRelationshipState` método y pasar el `EntityState.Added` parámetro permite `SchoolContext` saber que se ha agregado una relación entre los dos objetos:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Tenga en cuenta que si está actualizando (no solo agregando) una relación, debe eliminar la relación anterior después de agregar la nueva:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Sincronizar los cambios entre las claves externas y las propiedades de navegación

Al cambiar la relación de los objetos adjuntos al contexto mediante uno de los métodos descritos anteriormente, Entity Framework necesita mantener sincronizadas las claves externas, las referencias y las colecciones. Entity Framework administra automáticamente esta sincronización (también conocida como corrección de relación) para las entidades POCO con servidores proxy. Para obtener más información, consulte [trabajar con servidores proxy](xref:ef6/fundamentals/proxies).

Si usa entidades POCO sin proxy, debe asegurarse de que se llama al método **DetectChanges** para sincronizar los objetos relacionados en el contexto. Tenga en cuenta que las siguientes API desencadenan automáticamente una llamada a **DetectChanges** .

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   Ejecutar una consulta LINQ en un `DbSet`

## <a name="loading-related-objects"></a>Cargar objetos relacionados

En Entity Framework normalmente se usan las propiedades de navegación para cargar entidades relacionadas con la entidad devuelta por la Asociación definida. Para obtener más información, vea [cargar objetos relacionados](xref:ef6/querying/related-data).

> [!NOTE]
> En una asociación de clave externa, al cargar un extremo relacionado de un objeto dependiente, el objeto relacionado se cargará dependiendo del valor de clave externa del objeto dependiente actualmente en memoria:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

En una asociación independiente, se consulta el extremo relacionado de un objeto dependiente de acuerdo con el valor de clave externa actualmente en la base de datos. Sin embargo, si se modificó la relación y la propiedad Reference del objeto dependiente apunta a un objeto principal diferente que se carga en el contexto del objeto, Entity Framework intentará crear una relación tal y como se define en el cliente.

## <a name="managing-concurrency"></a>Administrar la simultaneidad

En las asociaciones de clave externa e independiente, las comprobaciones de simultaneidad se basan en las claves de entidad y otras propiedades de entidad que se definen en el modelo. Al usar el diseñador de EF para crear un modelo, establezca el `ConcurrencyMode` atributo en **fixed** para especificar que se debe comprobar la simultaneidad de la propiedad. Al usar Code First para definir un modelo, use la `ConcurrencyCheck` anotación en las propiedades cuya simultaneidad desea comprobar. Al trabajar con Code First también puede usar la `TimeStamp` anotación para especificar que se debe comprobar la simultaneidad de la propiedad. Solo puede tener una propiedad timestamp en una clase determinada. Code First asigna esta propiedad a un campo que no acepta valores NULL en la base de datos.

Se recomienda usar siempre la Asociación de clave externa al trabajar con entidades que participan en la comprobación de simultaneidad y la resolución.

Para obtener más información, vea [controlar los conflictos de simultaneidad](xref:ef6/saving/concurrency).

## <a name="working-with-overlapping-keys"></a>Trabajar con claves superpuestas

Las claves superpuestas son claves compuestas en las que algunas propiedades de la clave también forman parte de otra clave de la entidad. No es posible tener una clave superpuesta en una asociación independiente. Para cambiar una asociación de clave externa que incluya claves superpuestas, recomendamos modificar los valores de clave externa en lugar de utilizar las referencias a objetos.
