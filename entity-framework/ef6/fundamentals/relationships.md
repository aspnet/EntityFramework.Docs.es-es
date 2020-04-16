---
title: Relaciones, propiedades de navegación y claves externas - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434331"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relaciones, propiedades de navegación y claves externas

En este artículo se proporciona información general sobre cómo Entity Framework administra las relaciones entre entidades. También proporciona algunas instrucciones sobre cómo mapear y manipular relaciones.

## <a name="relationships-in-ef"></a>Relaciones en EF

En las bases de datos relacionales, las relaciones (también denominadas asociaciones) entre tablas se definen a través de claves externas. Una clave externa (FK) es una columna o combinación de columnas que se utiliza para establecer y exigir un vínculo entre los datos de dos tablas. Generalmente hay tres tipos de relaciones: uno a uno, uno a varios y muchos a varios. En una relación de uno a varios, la clave externa se define en la tabla que representa el extremo de muchos de la relación. La relación de varios a varios implica definir una tercera tabla (denominada tabla de unión o combinación), cuya clave principal se compone de las claves externas de ambas tablas relacionadas. En una relación uno a uno, la clave principal actúa adicionalmente como una clave externa y no hay ninguna columna de clave externa independiente para ninguna de las tablas.

La siguiente imagen muestra dos tablas que participan en una relación de uno a varios. La tabla **Course** es la tabla dependiente porque contiene la columna **DepartmentID** que la vincula a la tabla **Department.**

![Tablas de departamento y curso](~/ef6/media/database2.png)

En Entity Framework, una entidad puede estar relacionada con otras entidades a través de una asociación o relación. Cada relación contiene dos extremos que describen el tipo de entidad y la multiplicidad del tipo (uno, cero o uno o varios) para las dos entidades de esa relación. La relación puede venir gobernada por una restricción referencial, la cual describe qué extremo de la relación constituye un rol principal y cuál es un rol dependiente.

Las propiedades de navegación proporcionan una manera de navegar por una asociación entre dos tipos de entidad. Cada objeto puede tener una propiedad de navegación para cada relación en la que participa. Las propiedades de navegación permiten navegar y administrar relaciones en ambas direcciones, devolviendo un objeto de referencia (si la multiplicidad es uno o cero o uno) o una colección (si la multiplicidad es muchas). También puede optar por tener navegación unidireccional, en cuyo caso se define la propiedad de navegación en solo uno de los tipos que participa en la relación y no en ambos.

Se recomienda incluir propiedades en el modelo que se asignan a claves externas en la base de datos. Con las propiedades de clave externa incluidas, puede crear o cambiar una relación modificando el valor de clave externa sobre un objeto dependiente. Este tipo de asociación se denomina asociación de clave externa. El uso de claves externas es aún más esencial cuando se trabaja con entidades desconectadas. Tenga en cuenta, que cuando se trabaja con 1-a-1 o 1-a-0.. 1 relaciones, no hay ninguna columna de clave externa independiente, la propiedad de clave principal actúa como la clave externa y siempre se incluye en el modelo.

Cuando las columnas de clave externa no se incluyen en el modelo, la información de asociación se administra como un objeto independiente. Se realiza un seguimiento de las relaciones a través de referencias a objetos en lugar de propiedades de clave externa. Este tipo de asociación se denomina *asociación independiente.* La forma más común de modificar una *asociación independiente* es modificar las propiedades de navegación que se generan para cada entidad que participa en la asociación.

Puede decidir entre utilizar uno o ambos tipos de asociaciones en su modelo. Sin embargo, si tiene una relación pura de varios a varios que está conectada por una tabla de combinación que contiene solo claves externas, EF usará una asociación independiente para administrar dicha relación de varios a varios.   

La siguiente imagen muestra un modelo conceptual que se creó con Entity Framework Designer. El modelo contiene dos entidades que participan en una relación de uno a varios. Ambas entidades tienen propiedades de navegación. **Course** es la entidad dependiente y tiene definida la propiedad de clave externa **DepartmentID.**

![Tablas de departamento y curso con propiedades de navegación](~/ef6/media/relationshipefdesigner.png)

El siguiente fragmento de código muestra el mismo modelo que se creó con Code First.

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

## <a name="configuring-or-mapping-relationships"></a>Configuración o asignación de relaciones

El resto de esta página trata cómo acceder y manipular datos mediante relaciones. Para obtener información sobre cómo configurar relaciones en el modelo, consulte las páginas siguientes.

-   Para configurar relaciones en Code First, consulte [Anotaciones](~/ef6/modeling/code-first/data-annotations.md) de datos y [API fluida: relaciones](~/ef6/modeling/code-first/fluent/relationships.md).
-   Para configurar relaciones mediante Entity Framework Designer, vea [Relaciones con EF Designer](~/ef6/modeling/designer/relationships.md).

## <a name="creating-and-modifying-relationships"></a>Creación y modificación de relaciones

En una *asociación*de clave externa , al cambiar la `EntityState.Unchanged` relación, `EntityState.Modified`el estado de un objeto dependiente con un estado cambia a . En una relación independiente, cambiar la relación no actualiza el estado del objeto dependiente.

En los ejemplos siguientes se muestra cómo utilizar las propiedades de clave externa y las propiedades de navegación para asociar los objetos relacionados. Con las asociaciones de clave externa, puede utilizar cualquiera de los métodos para cambiar, crear o modificar relaciones. Con asociaciones independientes, no puede utilizar la propiedad de clave externa.

- Asignando un nuevo valor a una propiedad de clave externa, como en el ejemplo siguiente.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- El código siguiente quita una relación estableciendo la clave externa en **null**. Tenga en cuenta que la propiedad de clave externa debe tener valores NULL.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Si la referencia está en el estado agregado (en este ejemplo, el objeto course), la propiedad de navegación de referencia no se sincronizará con los valores de clave de un nuevo objeto hasta que se llame a SaveChanges. La sincronización no se produce porque el contexto del objeto no contiene claves permanentes para objetos agregados hasta que se guardan. Si debe tener nuevos objetos totalmente sincronizados tan pronto como establezca la relación, utilice uno de los métodos siguientes.*

- Asignando un nuevo objeto a una propiedad de navegación. El código siguiente crea una relación `department`entre un curso y un archivo . Si los objetos están asociados al contexto, también `course` se agrega a la `department.Courses` colección y la propiedad de clave externa correspondiente en el `course` objeto se establece en el valor de propiedad de clave del departamento.  
  ``` csharp
  course.Department = department;
  ```

- Para eliminar la relación, establezca `null`la propiedad de navegación en . Si está trabajando con Entity Framework que se basa en .NET 4.0, el extremo relacionado debe cargarse antes de establecerlo en null. Por ejemplo:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  A partir de Entity Framework 5.0, que se basa en .NET 4.5, puede establecer la relación en null sin cargar el extremo relacionado. También puede establecer el valor actual en null mediante el siguiente método.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Eliminando o agregando un objeto en una colección de entidades. Por ejemplo, puede agregar un `Course` objeto `department.Courses` de tipo a la colección. Esta operación crea una **course** relación entre `department`un curso determinado y un determinado . Si los objetos están asociados al contexto, la referencia de departamento y `department`la propiedad de clave externa del objeto de **curso** se establecerán en el archivo .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Mediante el `ChangeRelationshipState` método para cambiar el estado de la relación especificada entre dos objetos de entidad. Este método se utiliza más comúnmente cuando se trabaja con aplicaciones de n-nivel y una *asociación independiente* (no se puede utilizar con una asociación de clave externa). Además, para utilizar este `ObjectContext`método debe bajar a , como se muestra en el ejemplo siguiente.  
En el ejemplo siguiente, hay una relación de varios a varios entre instructores y cursos. Llamar `ChangeRelationshipState` al método `EntityState.Added` y pasar `SchoolContext` el parámetro, permite saber que se ha agregado una relación entre los dos objetos:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Tenga en cuenta que si está actualizando (no solo agregando) una relación, debe eliminar la relación antigua después de agregar la nueva:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Sincronización de los cambios entre las claves externas y las propiedades de navegación

Al cambiar la relación de los objetos asociados al contexto mediante uno de los métodos descritos anteriormente, Entity Framework debe mantener sincronizadas las claves externas, las referencias y las colecciones. Entity Framework administra automáticamente esta sincronización (también conocida como corrección de relaciones) para las entidades POCO con servidores proxy. Para obtener más información, consulte [Trabajar con servidores proxy](~/ef6/fundamentals/proxies.md).

Si utiliza entidades POCO sin servidores proxy, debe asegurarse de que se llama al método **DetectChanges** para sincronizar los objetos relacionados en el contexto. Tenga en cuenta que las siguientes API desencadenan automáticamente una llamada **DetectChanges.**

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
-   Ejecutar una consulta LINQ en un`DbSet`

## <a name="loading-related-objects"></a>Carga de objetos relacionados

En Entity Framework normalmente se usan propiedades de navegación para cargar entidades relacionadas con la entidad devuelta por la asociación definida. Para obtener más información, consulte [Carga de objetos relacionados](~/ef6/querying/related-data.md).

> [!NOTE]
> En una asociación de clave externa, al cargar un extremo relacionado de un objeto dependiente, el objeto relacionado se cargará dependiendo del valor de clave externa del objeto dependiente actualmente en memoria:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

En una asociación independiente, se consulta el extremo relacionado de un objeto dependiente de acuerdo con el valor de clave externa actualmente en la base de datos. Sin embargo, si se modificó la relación y la propiedad de referencia del objeto dependiente apunta a un objeto principal diferente que se carga en el contexto del objeto, Entity Framework intentará crear una relación tal como se define en el cliente.

## <a name="managing-concurrency"></a>Administrar la simultaneidad

Tanto en la clave externa como en las asociaciones independientes, las comprobaciones de simultaneidad se basan en las claves de entidad y otras propiedades de entidad definidas en el modelo. Cuando utilice ef Designer para crear `ConcurrencyMode` un modelo, establezca el atributo en **fixed** para especificar que la propiedad se debe comprobar para la simultaneidad. Cuando utilice Code First para definir `ConcurrencyCheck` un modelo, utilice la anotación en las propiedades que desea comprobar para la simultaneidad. Al trabajar con Code First, `TimeStamp` también puede usar la anotación para especificar que la propiedad se debe comprobar para la simultaneidad. Solo puede tener una propiedad timestamp en una clase determinada. Code First asigna esta propiedad a un campo que no acepta valores NULL en la base de datos.

Se recomienda usar siempre la asociación de clave externa al trabajar con entidades que participan en la comprobación y resolución de simultaneidad.

Para obtener más información, vea [Control de conflictos](~/ef6/saving/concurrency.md)de simultaneidad .

## <a name="working-with-overlapping-keys"></a>Trabajar con claves superpuestas

Las claves superpuestas son claves compuestas en las que algunas propiedades de la clave también forman parte de otra clave de la entidad. No es posible tener una clave superpuesta en una asociación independiente. Para cambiar una asociación de clave externa que incluya claves superpuestas, recomendamos modificar los valores de clave externa en lugar de utilizar las referencias a objetos.
