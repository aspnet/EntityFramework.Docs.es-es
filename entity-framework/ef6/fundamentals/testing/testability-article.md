---
title: Teestabilidad y Entity Framework 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434318"
---
# <a name="testability-and-entity-framework-40"></a>Teestabilidad y Entity Framework 4.0
Scott Allen

Publicación: mayo de 2010

## <a name="introduction"></a>Introducción

En este artículo técnico se describe y se muestra cómo escribir código comprobable con ADO.NET Entity Framework 4.0 y Visual Studio 2010. Este artículo no trata de centrarse en una metodología de prueba específica, como el diseño basado en pruebas (TDD) o el diseño basado en el comportamiento (BDD). En su lugar, este artículo se centrará en cómo escribir código que usa el ADO.NET Entity Framework, pero sigue siendo fácil de aislar y probar de forma automatizada. Veremos los patrones de diseño comunes que facilitan las pruebas en escenarios de acceso a datos y veremos cómo aplicar esos patrones al usar el marco de trabajo. También veremos las características específicas del marco de trabajo para ver cómo esas características pueden funcionar en código comprobable.

## <a name="what-is-testable-code"></a>¿Qué es el código comprobable?

La capacidad de verificar una pieza de software mediante pruebas unitarias automatizadas ofrece muchos beneficios deseables. Todo el mundo sabe que las buenas pruebas reducirán el número de defectos de software en una aplicación y aumentarán la calidad de la aplicación, pero tener pruebas unitarias en su lugar va mucho más allá de simplemente encontrar errores.

Un buen conjunto de pruebas unitarias permite a un equipo de desarrollo ahorrar tiempo y mantener el control del software que crean. Un equipo puede realizar cambios en el código existente, refactorizar, rediseñar y reestructurar el software para cumplir los nuevos requisitos y agregar nuevos componentes a una aplicación, todo ello sabiendo que el conjunto de pruebas puede comprobar el comportamiento de la aplicación. Las pruebas unitarias forman parte de un ciclo de retroalimentación rápida para facilitar el cambio y preservar la capacidad de mantenimiento del software a medida que aumenta la complejidad.

Sin embargo, las pruebas unitarias tienen un precio. Un equipo tiene que invertir el tiempo para crear y mantener pruebas unitarias. La cantidad de esfuerzo necesario para crear estas pruebas está directamente relacionada con la capacidad de **prueba** del software subyacente. ¿Qué tan fácil es probar el software? Un equipo que diseña software con capacidad de prueba en mente creará pruebas efectivas más rápido que el equipo que trabaja con software no comprobable.

Microsoft diseñó el ADO.NET Entity Framework 4.0 (EF4) teniendo en cuenta la capacidad de prueba. Esto no significa que los desarrolladores escribirán pruebas unitarias en el propio código del marco de trabajo. En su lugar, los objetivos de capacidad de prueba para EF4 facilitan la creación de código comprobable que se basa en el marco de trabajo. Antes de ver ejemplos específicos, vale la pena comprender las cualidades del código comprobable.

### <a name="the-qualities-of-testable-code"></a>Las cualidades del código comprobable

El código que es fácil de probar siempre mostrará al menos dos rasgos. En primer lugar, el código comprobable es fácil de **observar.** Dado algún conjunto de entradas, debe ser fácil observar la salida del código. Por ejemplo, probar el siguiente método es fácil porque el método devuelve directamente el resultado de un cálculo.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Probar un método es difícil si el método escribe el valor calculado en un socket de red, una tabla de base de datos o un archivo como el código siguiente. La prueba tiene que realizar un trabajo adicional para recuperar el valor.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

En segundo lugar, el código comprobable es fácil **de aislar.** Vamos a usar el siguiente pseudo-código como un mal ejemplo de código comprobable.

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

El método es fácil de observar: podemos aprobar una póliza de seguro y verificar que el valor de devolución coincide con un resultado esperado. Sin embargo, para probar el método necesitaremos tener una base de datos instalada con el esquema correcto y configurar el servidor SMTP en caso de que el método intente enviar un correo electrónico.

La prueba unitaria solo desea comprobar la lógica de cálculo dentro del método, pero la prueba puede fallar porque el servidor de correo electrónico está sin conexión o porque el servidor de base de datos se movió. Ambos errores no están relacionados con el comportamiento que la prueba desea verificar. El comportamiento es difícil de aislar.

Los desarrolladores de software que se esfuerzan por escribir código comprobable a menudo se esfuerzan por mantener una separación de preocupaciones en el código que escriben. El método anterior debe centrarse en los cálculos empresariales y delegar los detalles de implementación de la base de datos y el correo electrónico a otros componentes. Robert C. Martin lo llama el Principio de Responsabilidad Única. Un objeto debe encapsular una responsabilidad única y estrecha, como calcular el valor de una directiva. Todos los demás trabajos de base de datos y notificación deben ser responsabilidad de algún otro objeto. El código escrito de esta manera es más fácil de aislar porque se centra en una sola tarea.

En .NET tenemos las abstracciones que necesitamos para seguir el principio de responsabilidad única y lograr el aislamiento. Podemos usar definiciones de interfaz y forzar el código para usar la abstracción de interfaz en lugar de un tipo concreto. Más adelante en este artículo veremos cómo un método como el mal ejemplo presentado anteriormente puede funcionar con interfaces que *parecen* hablar con la base de datos. En el momento de la prueba, sin embargo, podemos sustituir una implementación ficticia que no habla con la base de datos, sino que contiene datos en la memoria. Esta implementación ficticia aislará el código de problemas no relacionados en el código de acceso a datos o la configuración de la base de datos.

El aislamiento tiene beneficios adicionales. El cálculo empresarial en el último método solo debe tardar unos milisegundos en ejecutarse, pero la prueba en sí misma puede ejecutarse durante varios segundos a medida que el código salta por la red y se comunica con varios servidores. Las pruebas unitarias deben ejecutarse rápidamente para facilitar pequeños cambios. Las pruebas unitarias también deben ser repetibles y no fallar porque un componente no relacionado con la prueba tiene un problema. Escribir código que sea fácil de observar y aislar significa que los desarrolladores tendrán más fácil escribir pruebas para el código, pasar menos tiempo esperando a que se ejecuten las pruebas y, lo que es más importante, dedicar menos tiempo a rastrear errores que no existen.

Esperemos que pueda apreciar los beneficios de las pruebas y comprender las cualidades que exhibe el código comprobable. Estamos a punto de abordar cómo escribir código que funciona con EF4 para guardar datos en una base de datos mientras permanecemos observables y fáciles de aislar, pero primero vamos a limitar nuestro enfoque para discutir diseños comprobables para el acceso a datos.

## <a name="design-patterns-for-data-persistence"></a>Patrones de diseño para la persistencia de datos

Los dos malos ejemplos presentados anteriormente tenían demasiadas responsabilidades. El primer ejemplo incorrecto tenía que realizar un cálculo *y* escribir en un archivo. El segundo ejemplo incorrecto tenía que leer los datos de una base de datos *y* realizar un cálculo empresarial *y* enviar correo electrónico. Al diseñar métodos más pequeños que separan las preocupaciones y delegan la responsabilidad a otros componentes, logrará grandes avances hacia la escritura de código comprobable. El objetivo es crear funcionalidad mediante la composición de acciones a partir de abstracciones pequeñas y enfocadas.

Cuando se trata de la persistencia de datos, las abstracciones pequeñas y enfocadas que estamos buscando son tan comunes que se han documentado como patrones de diseño. El libro de Martin Fowler Patterns of Enterprise Application Architecture fue el primer trabajo en describir estos patrones en impresión. Proporcionaremos una breve descripción de estos patrones en las secciones siguientes antes de mostrar cómo estos ADO.NET Entity Framework implementa y funciona con estos patrones.

### <a name="the-repository-pattern"></a>The Repository Pattern (El modelo de repositorio)

Fowler dice que un repositorio "media entre el dominio y las capas de asignación de datos mediante una interfaz similar a una colección para acceder a objetos de dominio". El objetivo del patrón de repositorio es aislar el código de las minucias del acceso a datos, y como vimos el aislamiento anterior es un rasgo necesario para la capacidad de prueba.

La clave del aislamiento es cómo el repositorio expone objetos mediante una interfaz similar a una colección. La lógica que escriba para utilizar el repositorio no tiene idea de cómo el repositorio materializará los objetos que solicite. El repositorio puede hablar con una base de datos o simplemente devolver objetos de una colección en memoria. Todo lo que el código debe saber es que el repositorio parece mantener la colección y puede recuperar, agregar y eliminar objetos de la colección.

En las aplicaciones .NET existentes, un repositorio concreto a menudo hereda de una interfaz genérica como la siguiente:

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Realizaremos algunos cambios en la definición de interfaz cuando proporcionemos una implementación para EF4, pero el concepto básico sigue siendo el mismo. El código puede usar un repositorio concreto que implementa esta interfaz para recuperar una entidad por su valor de clave principal, para recuperar una colección de entidades basadas en la evaluación de un predicado o simplemente recuperar todas las entidades disponibles. El código también puede agregar y quitar entidades a través de la interfaz del repositorio.

Dado un IRepository de Employee objetos, el código puede realizar las siguientes operaciones.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Puesto que el código está utilizando una interfaz (IRepository of Employee), podemos proporcionar el código con diferentes implementaciones de la interfaz. Una implementación podría ser una implementación respaldada por EF4 y la conservación de objetos en una base de datos de Microsoft SQL Server. Una implementación diferente (una que usamos durante las pruebas) podría estar respaldada por una lista en memoria de objetos Employee. La interfaz ayudará a lograr el aislamiento en el código.

Observe que&lt;la&gt; interfaz IRepository T no expone una operación Save. ¿Cómo actualizamos los objetos existentes? Es posible que se encuentre con definiciones de IRepository que incluyen la operación Guardar y las implementaciones de estos repositorios tendrán que conservar inmediatamente un objeto en la base de datos. Sin embargo, en muchas aplicaciones no queremos conservar objetos individualmente. En su lugar, queremos dar vida a objetos, tal vez desde repositorios diferentes, modificar esos objetos como parte de una actividad empresarial y, a continuación, conservar todos los objetos como parte de una sola operación atómica. Afortunadamente, hay un patrón para permitir este tipo de comportamiento.

### <a name="the-unit-of-work-pattern"></a>El patrón de unidad de trabajo

Fowler dice que una unidad de trabajo "mantendrá una lista de objetos afectados por una transacción comercial y coordina la escritura de los cambios y la resolución de problemas de simultaneidad". Es responsabilidad de la unidad de trabajo realizar un seguimiento de los cambios en los objetos que damos vida desde un repositorio y conservar los cambios que hayamos realizado en los objetos cuando le decimos a la unidad de trabajo que confirme los cambios. También es responsabilidad de la unidad de trabajo tomar los nuevos objetos que hemos agregado a todos los repositorios e insertar los objetos en una base de datos, y también la eliminación de la administración.

Si alguna vez ha realizado algún trabajo con ADO.NET DataSets, ya estará familiarizado con la unidad de patrón de trabajo. ADO.NET DataSets tenía la capacidad de realizar un seguimiento de nuestras actualizaciones, eliminaciones e inserción de objetos DataRow y podía (con la ayuda de un TableAdapter) conciliar todos nuestros cambios en una base de datos. Sin embargo, dataSet objetos modelan un subconjunto desconectado de la base de datos subyacente. La unidad de patrón de trabajo muestra el mismo comportamiento, pero funciona con objetos de negocio y objetos de dominio que están aislados del código de acceso a datos y que no son conscientes de la base de datos.

Una abstracción para modelar la unidad de trabajo en código .NET podría tener el siguiente aspecto:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

Al exponer las referencias de repositorio de la unidad de trabajo, podemos garantizar que una sola unidad de objeto de trabajo tenga la capacidad de realizar un seguimiento de todas las entidades materializadas durante una transacción comercial. La implementación del método Commit para una unidad de trabajo real es donde sucede toda la magia para conciliar los cambios en memoria con la base de datos. 

Dada una referencia IUnitOfWork, el código puede realizar cambios en los objetos de negocio recuperados de uno o varios repositorios y guardar todos los cambios mediante la operación de confirmación atómica.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>El patrón de carga diferida

Fowler utiliza el nombre lazy load para describir "un objeto que no contiene todos los datos que necesita pero sabe cómo obtenerlo". La carga diferida transparente es una característica importante que debe tener al escribir código de negocio comprobable y trabajar con una base de datos relacional. Por ejemplo, considere el código siguiente.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

¿Cómo se rellena la colección TimeCards? Hay dos respuestas posibles. Una respuesta es que el repositorio de empleados, cuando se le pide que obtenga un empleado, emite una consulta para recuperar tanto al empleado como la información de la tarjeta de tiempo asociada del empleado. En las bases de datos relacionales, esto generalmente requiere una consulta con una cláusula JOIN y puede dar lugar a la recuperación de más información de la que necesita una aplicación. ¿Qué sucede si la aplicación nunca necesita tocar la propiedad TimeCards?

Una segunda respuesta es cargar la propiedad TimeCards "bajo demanda". Esta carga diferida es implícita y transparente para la lógica de negocios porque el código no invoca API especiales para recuperar información de tarjeta de tiempo. El código asume que la información de la tarjeta de tiempo está presente cuando es necesario. Hay algo de magia implicada en la carga diferida que generalmente implica la interceptación en tiempo de ejecución de invocaciones de método. El código de interceptación es responsable de hablar con la base de datos y recuperar información de la tarjeta de tiempo, dejando la lógica de negocios libre para ser lógica de negocios. Esta magia de carga diferida permite que el código de negocio se aísle de las operaciones de recuperación de datos y da como resultado código más comprobable.

El inconveniente de una carga diferida es que cuando una *aplicación* necesita la información de la tarjeta de tiempo, el código ejecutará una consulta adicional. Esto no es una preocupación para muchas aplicaciones, pero para las aplicaciones sensibles al rendimiento o aplicaciones que recorren en bucle una serie de objetos de empleado y ejecutan una consulta para recuperar tarjetas de tiempo durante cada iteración del bucle (un problema a menudo denominado problema de consulta N+1), la carga diferida es un arrastre. En estos escenarios, es posible que una aplicación desee cargar con entusiasmo la información de la tarjeta de tiempo de la manera más eficaz posible.

Afortunadamente, veremos cómo EF4 admite cargas diferidas implícitas y cargas diligentes eficientes a medida que pasamos a la siguiente sección e implementamos estos patrones.

## <a name="implementing-patterns-with-the-entity-framework"></a>Implementación de patrones con Entity Framework

La buena noticia es que todos los patrones de diseño que describimos en la última sección son fáciles de implementar con EF4. Para demostrar que vamos a usar una sencilla aplicación ASP.NET MVC para editar y mostrar los empleados y su información de tarjeta de tiempo asociada. Comenzaremos usando los siguientes "objetos CLR antiguos sin formato" (POCO). 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

Estas definiciones de clase cambiarán ligeramente a medida que exploramos diferentes enfoques y características de EF4, pero la intención es mantener estas clases lo más pernopeso ignorante (PI) como sea posible. Un objeto PI no sabe *cómo*, o incluso *si*, el estado que contiene vive dentro de una base de datos. PI y POCOs van de la mano con software comprobable. Los objetos que utilizan un enfoque POCO son menos restringidos, más flexibles y más fáciles de probar porque pueden funcionar sin una base de datos presente.

Con los POCO en su lugar podemos crear un Entity Data Model (EDM) en Visual Studio (consulte la figura 1). No usaremos el EDM para generar código para nuestras entidades. En su lugar, queremos utilizar las entidades que elaboramos con amor a mano. Solo usaremos el EDM para generar nuestro esquema de base de datos y proporcionar los metadatos que EF4 necesita para asignar objetos a la base de datos.

![ef test_01](~/ef6/media/eftest-01.jpg)

**Figura 1**

Nota: si desea desarrollar primero el modelo EDM, es posible generar código POCO limpio desde el EDM. Puede hacerlo con una extensión de Visual Studio 2010 proporcionada por el equipo de programación de datos. Para descargar la extensión, inicie el Administrador de extensiones desde el menú Herramientas de Visual Studio y busque "POCO" en la galería en línea de plantillas (consulte la figura 2). Hay varias plantillas POCO disponibles para EF. Para obtener más información sobre el uso de la plantilla, vea ["Tutorial: Plantilla POCO para Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".

![ef test_02](~/ef6/media/eftest-02.png)

**Figura 2**

Desde este punto de partida poco exploraremos dos enfoques diferentes para el código comprobable. El primer enfoque que llamo el enfoque EF porque aprovecha las abstracciones de la API de Entity Framework para implementar unidades de trabajo y repositorios. En el segundo enfoque crearemos nuestras propias abstracciones de repositorio personalizadas y luego veremos las ventajas y desventajas de cada enfoque. Comenzaremos explorando el enfoque de EF.  

### <a name="an-ef-centric-implementation"></a>Una implementación de EF Centric

Considere la siguiente acción de controlador de un proyecto de ASP.NET MVC. La acción recupera un objeto Employee y devuelve un resultado para mostrar una vista detallada del empleado.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

¿Se puede probar el código? Hay al menos dos pruebas que necesitamos para verificar el comportamiento de la acción. En primer lugar, nos gustaría comprobar que la acción devuelve la vista correcta, una prueba fácil. También queremos escribir una prueba para comprobar que la acción recupera el empleado correcto y nos gustaría hacerlo sin ejecutar código para consultar la base de datos. Recuerde que queremos aislar el código bajo prueba. El aislamiento garantizará que la prueba no falle debido a un error en el código de acceso a datos o en la configuración de la base de datos. Si la prueba falla, sabremos que tenemos un error en la lógica del controlador, y no en algún componente del sistema de nivel inferior.

Para lograr el aislamiento necesitaremos algunas abstracciones como las interfaces que presentamos anteriormente para repositorios y unidades de trabajo. Recuerde que el patrón de repositorio está diseñado para mediar entre los objetos de dominio y la capa de asignación de datos. En este escenario EF4 *es* la capa de asignación de datos y&lt;&gt; ya proporciona una abstracción similar a un repositorio denominada IObjectSet T (desde el espacio de nombres System.Data.Objects). La definición de interfaz es similar a la siguiente.

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

IObjectSet&lt;&gt; T cumple los requisitos de un repositorio porque se&lt;asemeja&gt;a una colección de objetos (a través de IEnumerable T ) y proporciona métodos para agregar y quitar objetos de la colección simulada. Los métodos Attach y Detach exponen capacidades adicionales de la API de EF4. Para utilizar IObjectSet&lt;T&gt; como interfaz para repositorios necesitamos una unidad de abstracción de trabajo para enlazar repositorios.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Una implementación concreta de esta interfaz se hablará con SQL Server y es fácil de crear mediante la clase ObjectContext de EF4. La clase ObjectContext es la verdadera unidad de trabajo de la API de EF4.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

Dar vida a&lt;&gt; un IObjectSet T es tan fácil como invocar el método CreateObjectSet del objeto ObjectContext. En segundo plano, el marco de trabajo utilizará los metadatos&lt;que&gt;proporcionamos en el EDM para producir un ObjectSet T concreto. Seguiremos con devolver la interfaz&lt;&gt; IObjectSet T porque ayudará a conservar la capacidad de prueba en el código de cliente.

Esta implementación concreta es útil en la producción, pero tenemos que centrarnos en cómo usaremos nuestra abstracción IUnitOfWork para facilitar las pruebas.

### <a name="the-test-doubles"></a>Los dobles de la prueba

Para aislar la acción del controlador necesitaremos la capacidad de cambiar entre la unidad de trabajo real (respaldada por un ObjectContext) y una unidad de trabajo doble o "falsa" de prueba (realizando operaciones en memoria). El enfoque común para realizar este tipo de conmutación es no permitir que el controlador MVC crear una instancia de una unidad de trabajo, sino pasar la unidad de trabajo en el controlador como un parámetro constructor.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

El código anterior es un ejemplo de inserción de dependencias. No permitimos que el controlador cree su dependencia (la unidad de trabajo) sino que inyecte la dependencia en el controlador. En un proyecto MVC es común usar un generador de controladores personalizados en combinación con un contenedor de inversión de control (IoC) para automatizar la inserción de dependencias. Estos temas están fuera del ámbito de este artículo, pero puede leer más siguiendo las referencias al final de este artículo.

Una unidad falsa de implementación de trabajo que podemos usar para las pruebas podría tener el siguiente aspecto.

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

Observe que la unidad de trabajo falsa expone una propiedad Commited. A veces es útil agregar características a una clase falsa que faciliten las pruebas. En este caso, es fácil observar si el código confirma una unidad de trabajo comprobando la propiedad Commited.

También necesitaremos un IObjectSet&lt;&gt; T falso para mantener los objetos Employee y TimeCard en la memoria. Podemos proporcionar una sola implementación mediante genéricos.

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

Esta prueba delega la mayor parte de&lt;&gt; su trabajo en un objeto HashSet T subyacente. Tenga en cuenta&lt;&gt; que IObjectSet T requiere una restricción genérica que aplica T como&lt;una&gt;clase (un tipo de referencia) y también nos obliga a implementar IQueryable T . Es fácil hacer que una colección en memoria&lt;&gt; aparezca como un IQueryable T mediante el operador LINQ estándar AsQueryable.

### <a name="the-tests"></a>Las pruebas

Las pruebas unitarias tradicionales usarán una sola clase de prueba para contener todas las pruebas de todas las acciones en un único controlador MVC. Podemos escribir estas pruebas, o cualquier tipo de prueba unitaria, usando las falsificaciones en memoria que hemos construido. Sin embargo, para este artículo evitaremos el enfoque de clase de prueba monolítica y en su lugar agruparemos nuestras pruebas para centrarnos en una parte específica de la funcionalidad.Por ejemplo, "crear nuevo empleado" podría ser la funcionalidad que queremos probar, por lo que usaremos una sola clase de prueba para comprobar la acción de controlador único responsable de crear un nuevo empleado.

Hay algún código de configuración común que necesitamos para todas estas clases de prueba de grano fino. Por ejemplo, siempre necesitamos crear nuestros repositorios en memoria y una unidad de trabajo falsa. También necesitamos una instancia del controlador del empleado con la unidad falsa de trabajo inyectada. Compartiremos este código de configuración común entre clases de prueba mediante una clase base.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

El "objeto madre" que usamos en la clase base es un patrón común para crear datos de prueba. Una madre de objeto contiene métodos de fábrica para crear instancias de entidades de prueba para su uso en varios accesorios de prueba.

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

Podemos usar EmployeeControllerTestBase como la clase base para una serie de accesorios de prueba (consulte la figura 3). Cada accesorio de prueba probará una acción específica del controlador. Por ejemplo, un accesorio de prueba se centrará en probar la acción Crear utilizada durante una solicitud HTTP GET (para mostrar la vista para crear un empleado) y un accesorio diferente se centrará en la acción Crear utilizada en una solicitud HTTP POST (para tomar la información enviada por el usuario para crear un empleado). Cada clase derivada solo es responsable de la configuración necesaria en su contexto específico y de proporcionar las aserciones necesarias para comprobar los resultados de su contexto de prueba específico.

![ef test_03](~/ef6/media/eftest-03.png)

**Figura 3**

La convención de nomenclatura y el estilo de prueba que se presentan aquí no son necesarios para el código comprobable: es solo un enfoque. La Figura 4 muestra las pruebas que se ejecutan en el complemento Jet Brains Resharper test runner para Visual Studio 2010.

![ef test_04](~/ef6/media/eftest-04.png)

**Figura 4**

Con una clase base para controlar el código de configuración compartido, las pruebas unitarias para cada acción del controlador son pequeñas y fáciles de escribir. Las pruebas se ejecutarán rápidamente (ya que estamos realizando operaciones en memoria) y no deberían fallar debido a problemas ambientales o de infraestructura no relacionados (porque hemos aislado la unidad bajo prueba).

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

En estas pruebas, la clase base realiza la mayor parte del trabajo de configuración. Recuerde que el constructor de la clase base crea el repositorio en memoria, una unidad de trabajo falsa y una instancia de la clase EmployeeController. La clase de prueba se deriva de esta clase base y se centra en los detalles de la prueba de la Create método. En este caso, los detalles se reducen a los pasos de "organizar, actuar y afirmar" que verá en cualquier procedimiento de prueba unitaria:

-   Cree un objeto newEmployee para simular los datos entrantes.
-   Invoque la acción Crear de EmployeeController y pase el newEmployee.
-   Compruebe que la acción Crear produce los resultados esperados (el empleado aparece en el repositorio).

Lo que hemos creado nos permite probar cualquiera de las acciones de EmployeeController. Por ejemplo, cuando escribimos pruebas para la acción Index del controlador Employee podemos heredar de la clase base de prueba para establecer la misma configuración base para nuestras pruebas. De nuevo, la clase base creará el repositorio en memoria, la unidad de trabajo falsa y una instancia de EmployeeController. Las pruebas para la acción Index solo tienen que centrarse en invocar la acción Index y probar las cualidades del modelo que devuelve la acción.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

Las pruebas que estamos creando con falsificaciones en memoria están orientadas a probar el *estado* del software. Por ejemplo, al probar la acción Crear queremos inspeccionar el estado del repositorio después de ejecutar la acción de creación: ¿el repositorio contiene al nuevo empleado?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

Más adelante veremos las pruebas basadas en la interacción. Las pruebas basadas en interacciones preguntarán si el código sometido a prueba invocó los métodos adecuados en nuestros objetos y pasó los parámetros correctos. Por ahora vamos a mover en la cubierta otro patrón de diseño - la carga diferida.

## <a name="eager-loading-and-lazy-loading"></a>Carga ansiosa y carga diferida

En algún momento de la aplicación web ASP.NET MVC, es posible que deseemos mostrar la información de un empleado e incluir las tarjetas de tiempo asociadas del empleado. Por ejemplo, es posible que tengamos una visualización de resumen de tarjeta de tiempo que muestre el nombre del empleado y el número total de tarjetas de tiempo en el sistema. Hay varios enfoques que podemos tomar para implementar esta característica.

### <a name="projection"></a>Proyección

Un enfoque fácil para crear el resumen es construir un modelo dedicado a la información que queremos mostrar en la vista. En este escenario, el modelo podría tener el siguiente aspecto.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Tenga en cuenta que EmployeeSummaryViewModel no es una entidad, en otras palabras, no es algo que queremos conservar en la base de datos. Sólo vamos a usar esta clase para mezclar datos en la vista de una manera fuertemente tipada. El modelo de vista es como un objeto de transferencia de datos (DTO) porque no contiene ningún comportamiento (sin métodos) – solo propiedades. Las propiedades contendrán los datos que necesitamos mover. Es fácil crear instancias de este modelo de vista mediante el operador de proyección estándar de LINQ: el operador Select.

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

Hay dos características notables en el código anterior. En primer lugar, el código es fácil de probar porque todavía es fácil de observar y aislar. El operador Select funciona tan bien contra nuestras falsificaciones en memoria como contra la unidad real de trabajo.

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

La segunda característica notable es cómo el código permite que EF4 genere una sola consulta eficaz para ensamblar la información de los empleados y de la tarjeta de tiempo. Hemos cargado la información de los empleados y la información de la tarjeta de tiempo en el mismo objeto sin usar ninguna API especial. El código simplemente expresa la información que requiere mediante operadores LINQ estándar que funcionan con orígenes de datos en memoria, así como orígenes de datos remotos. EF4 pudo traducir los árboles de expresión generados por la consulta LINQ y el compilador de C\# en una única y eficaz consulta T-SQL.

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

Hay otras ocasiones en las que no queremos trabajar con un modelo de vista o un objeto DTO, pero con entidades reales. Cuando sabemos que necesitamos un empleado *y* las tarjetas de tiempo del empleado, podemos cargar con entusiasmo los datos relacionados de una manera discreta y eficiente.

### <a name="explicit-eager-loading"></a>Carga explícita de Eager

Cuando queremos cargar con entusiasmo la información de entidad relacionada, necesitamos algún mecanismo para la lógica de negocios (o en este escenario, lógica de acción del controlador) para expresar su deseo al repositorio. La clase T&lt;&gt; de ObjectQuery de EF4 define un método Include para especificar los objetos relacionados que se van a recuperar durante una consulta. Recuerde que EF4 ObjectContext expone entidades&lt;a&gt; través de la&lt;&gt;clase ObjectSet T concreta que hereda de ObjectQuery T .Si estuviéramos usando referencias de ObjectSet&lt;T&gt; en nuestra acción de controlador podríamos escribir el siguiente código para especificar una carga diligente de información de tarjeta de tiempo para cada empleado.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Sin embargo, puesto que estamos tratando de mantener&lt;&gt; nuestro código comprobable no estamos exponiendo ObjectSet T desde fuera de la unidad real de la clase de trabajo. En su lugar, nos&lt;basamos en la interfaz IObjectSet T&gt; que es más fácil de falsificar, pero IObjectSet&lt;T&gt; no define un método Include. La belleza de LINQ LINQ es que podemos crear nuestro propio operador Include.

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

Observe que este operador Include se define&lt;como&gt; un método&lt;de&gt;extensión para IQueryable T en lugar de IObjectSet T . Esto nos da la capacidad de utilizar el método con una&lt;&gt;gama más&lt;amplia&gt;de&lt;tipos&gt;posibles,&lt;incluidos&gt;IQueryable T , IObjectSet T , ObjectQuery T y ObjectSet T . En caso de que la secuencia subyacente no&lt;&gt;sea una verdadera Query T de EF4 , no hay ningún daño realizado y el operador Include no es op. Si la secuencia *is* subyacente es&lt;&gt; un ObjectQuery T&lt;(o derivado de ObjectQuery T&gt;), EF4 verá nuestro requisito de datos adicionales y formulará la consulta SQL adecuada.

Con este nuevo operador en su lugar podemos solicitar explícitamente una carga diligente de información de la tarjeta de tiempo del repositorio.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Cuando se ejecuta en un ObjectContext real, el código genera la siguiente consulta única. La consulta recopila suficiente información de la base de datos en un viaje para materializar los objetos de empleado y rellenar completamente su propiedad TimeCards.

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

La gran noticia es que el código dentro del método de acción sigue siendo totalmente comprobable. No necesitamos proporcionar ninguna característica adicional para nuestras falsificaciones para apoyar al operador Include. La mala noticia es que tuvimos que usar el operador Include dentro del código que queríamos mantener la persistencia ignorante. Este es un ejemplo excelente del tipo de compensaciones que deberá evaluar al crear código comprobable. Hay ocasiones en las que debe dejar que los problemas de persistencia se filtren fuera de la abstracción del repositorio para cumplir los objetivos de rendimiento.

La alternativa a la carga diligente es la carga diferida. La carga diferida significa que *no* necesitamos nuestro código de negocio para anunciar explícitamente el requisito de los datos asociados. En su lugar, usamos nuestras entidades en la aplicación y si se necesitan datos adicionales Entity Framework cargará los datos a petición.

### <a name="lazy-loading"></a>Carga diferida

Es fácil imaginar un escenario en el que no sepamos qué datos necesitará una pieza de lógica empresarial. Es posible que sepamos que la lógica necesita un objeto de empleado, pero podemos bifurcarnos en diferentes rutas de ejecución donde algunas de esas rutas requieren información de tarjeta de tiempo del empleado y otras no. Escenarios como este son perfectos para la carga diferida implícita porque los datos aparecen mágicamente según sea necesario.

La carga diferida, también conocida como carga diferida, coloca algunos requisitos en nuestros objetos de entidad. Los POCO con verdadera ignorancia de persistencia no enfrentarían ningún requisito de la capa de persistencia, pero la verdadera ignorancia de persistencia es prácticamente imposible de lograr.En su lugar, medimos la ignorancia de persistencia en grados relativos. Sería desafortunado si necesitábamos heredar de una clase base orientada a la persistencia o usar una colección especializada para lograr la carga diferida en LOS POCO. Afortunadamente, EF4 tiene una solución menos intrusiva.

### <a name="virtually-undetectable"></a>Prácticamente indetectable

Cuando se utilizan objetos POCO, EF4 puede generar dinámicamente proxies en tiempo de ejecución para entidades. Estos proxies envuelven de forma invisible los POCO materializados y proporcionan servicios adicionales interceptando cada propiedad get y set operation para realizar trabajo adicional. Uno de estos servicios es la función de carga diferida que estamos buscando. Otro servicio es un mecanismo de seguimiento de cambios eficaz que puede registrar cuando el programa cambia los valores de propiedad de una entidad. ObjectContext utiliza la lista de cambios durante el método SaveChanges para conservar las entidades modificadas mediante comandos UPDATE.

Para que estos servidores proxy funcionen, sin embargo, necesitan una forma de enlazar a las operaciones get y set de propiedad en una entidad, y los servidores proxy logran este objetivo reemplazando a los miembros virtuales. Por lo tanto, si queremos tener carga diferida implícita y seguimiento de cambios eficiente, tenemos que volver a nuestras definiciones de clase POCO y marcar las propiedades como virtuales.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Todavía podemos decir que la entidad Employee es principalmente ignorante de persistencia. El único requisito es usar miembros virtuales y esto no afecta a la capacidad de prueba del código. No necesitamos derivar de ninguna clase base especial, ni siquiera usar una colección especial dedicada a la carga diferida. Como se muestra en el código, cualquier clase que implementa ICollection&lt;T&gt; está disponible para contener entidades relacionadas.

También hay un cambio menor que necesitamos hacer dentro de nuestra unidad de trabajo. La carga diferida está *desactivada* de forma predeterminada cuando se trabaja directamente con un objeto ObjectContext. Hay una propiedad que podemos establecer en la propiedad ContextOptions para habilitar la carga diferida, y podemos establecer esta propiedad dentro de nuestra unidad de trabajo real si queremos habilitar la carga diferida en todas partes.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

Con la carga diferida implícita habilitada, el código de aplicación puede usar un empleado y las tarjetas de tiempo asociadas del empleado sin dejar de tener conocimiento del trabajo necesario para que EF cargue los datos adicionales.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

La carga diferida hace que el código de la aplicación sea más fácil de escribir, y con la magia del proxy el código sigue siendo completamente comprobable. Las falsificaciones en memoria de la unidad de trabajo pueden simplemente precargar entidades falsas con datos asociados cuando sea necesario durante una prueba.

En este punto, centraremos nuestra atención en&lt;la&gt; creación de repositorios mediante IObjectSet T y examinaremos las abstracciones para ocultar todos los signos del marco de persistencia.

## <a name="custom-repositories"></a>Repositorios personalizados

Cuando presentamos por primera vez el patrón de diseño de unidad de trabajo en este artículo, proporcionamos un código de ejemplo para el aspecto que podría tener la unidad de trabajo. Vamos a volver a presentar esta idea original usando el escenario de tarjeta de tiempo de empleado y empleado con el que hemos estado trabajando.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

La principal diferencia entre esta unidad de trabajo y la unidad de trabajo que creamos en la última sección es cómo esta&lt;unidad&gt;de trabajo no utiliza ninguna abstracción del marco EF4 (no hay IObjectSet T). IObjectSet&lt;&gt; T funciona bien como una interfaz de repositorio, pero la API que expone podría no alinearse perfectamente con las necesidades de nuestra aplicación. En este próximo enfoque representaremos repositorios&lt;mediante&gt; una abstracción IRepository T personalizada.

Muchos desarrolladores que siguen el diseño basado en pruebas, el diseño&lt;basado&gt; en el comportamiento y el diseño de metodologías basadas en dominios prefieren el enfoque IRepository T por varias razones. En primer lugar, la interfaz IRepository&lt;T&gt; representa una capa "anticorrupción". Como describe Eric Evans en su libro Domain Driven Design, una capa anticorrupción mantiene el código de dominio alejado de las API de infraestructura, como una API de persistencia. En segundo lugar, los desarrolladores pueden crear métodos en el repositorio que satisfagan las necesidades exactas de una aplicación (como se descubrió al escribir pruebas). Por ejemplo, es posible que necesitemos con frecuencia buscar una sola entidad mediante un valor ID, por lo que podemos agregar un método FindById a la interfaz del repositorio.Nuestra definición de IRepository&lt;T&gt; tendrá el siguiente aspecto.

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Tenga en cuenta que volveremos a&lt;&gt; usar una interfaz T IQueryable para exponer colecciones de entidades. IQueryable&lt;&gt; T permite que los árboles de expresión LINQ fluyan en el proveedor EF4 y proporcionan al proveedor una vista holística de la consulta. Una segunda opción sería devolver&lt;IEnumerable T&gt;, lo que significa que el proveedor LINQ de EF4 solo verá las expresiones compiladas dentro del repositorio. Cualquier agrupación, ordenación y proyección realizada fuera del repositorio no se compondrá en el comando SQL enviado a la base de datos, lo que puede perjudicar el rendimiento. Por otro lado, un repositorio que&lt;devuelve&gt; solo los resultados de IEnumerable T nunca le sorprenderá con un nuevo comando SQL. Ambos enfoques funcionarán, y ambos enfoques siguen siendo comprobables.

Es sencillo proporcionar una única implementación&lt;de&gt; la interfaz IRepository T mediante genéricos y la API ObjectContext de EF4.

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

El enfoque&lt;&gt; IRepository T nos da un cierto control adicional sobre nuestras consultas porque un cliente tiene que invocar un método para llegar a una entidad. Dentro del método podríamos proporcionar comprobaciones adicionales y operadores LINQ para aplicar restricciones de aplicación. Observe que la interfaz tiene dos restricciones en el parámetro de tipo genérico. La primera restricción es la clase cons&lt;taint requerida por ObjectSet T&gt;y la segunda restricción obliga a nuestras entidades a implementar IEntity: una abstracción creada para la aplicación. La interfaz IEntity obliga a las entidades a tener una propiedad Id legible y, a continuación, podemos usar esta propiedad en el método FindById. IEntity se define con el código siguiente.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity podría considerarse una pequeña infracción de la ignorancia de persistencia, ya que nuestras entidades están obligadas a implementar esta interfaz. Recuerde que la ignorancia de persistencia tiene que ver con las compensaciones y, para muchos, la funcionalidad FindById superará la restricción impuesta por la interfaz. La interfaz no tiene ningún impacto en la capacidad de prueba.

La creación de instancias&lt;&gt; de un IRepository T activo requiere un ObjectContext de EF4, por lo que una unidad concreta de implementación de trabajo debe administrar la creación de instancias.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a>Uso del repositorio personalizado

El uso de nuestro repositorio personalizado no es significativamente&lt;&gt;diferente del uso del repositorio basado en IObjectSet T . En lugar de aplicar operadores LINQ directamente a una propiedad, primero tendremos que&lt;invocar&gt; uno de los métodos del repositorio para tomar una referencia T IQueryable.

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Observe que el operador Include personalizado que implementamos anteriormente funcionará sin cambios. El método FindById del repositorio quita la lógica duplicada de las acciones que intentan recuperar una sola entidad.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

No hay diferencia significativa en la capacidad de prueba de los dos enfoques que hemos examinado. Podríamos proporcionar implementaciones&lt;falsas&gt; de IRepository T mediante&lt;&gt; la creación de clases concretas respaldadas por HashSet Employee - al igual que lo que hicimos en la última sección. Sin embargo, algunos desarrolladores prefieren usar objetos ficticios y marcos de objetos ficticios en lugar de crear falsificaciones. Veremos el uso de simulacros para probar nuestra implementación y discutir las diferencias entre simulacros y falsificaciones en la siguiente sección.

### <a name="testing-with-mocks"></a>Pruebas con simulacros

Hay diferentes enfoques para construir lo que Martin Fowler llama un "doble de prueba". Un doble de prueba (como un doble de acrobacias de película) es un objeto que se construye para "permanecer" para objetos reales de producción durante las pruebas. Los repositorios en memoria que creamos son duplicaciones de prueba para los repositorios que hablan con SQL Server. Hemos visto cómo usar estos dobles de prueba durante las pruebas unitarias para aislar el código y mantener las pruebas en ejecución rápidamente.

Los dobles de prueba que hemos construido tienen implementaciones reales y de trabajo. En segundo plano, cada uno almacena una colección concreta de objetos, y agregarán y eliminarán objetos de esta colección a medida que manipulamos el repositorio durante una prueba. A algunos desarrolladores les gusta crear sus duplicaciones de prueba de esta manera, con código real e implementaciones de trabajo.Estos dobles de prueba son lo que llamamos *falsificaciones.* Tienen implementaciones de trabajo, pero no son lo suficientemente reales para su uso en producción. El repositorio falso no escribe realmente en la base de datos. El servidor SMTP falso no envía realmente un mensaje de correo electrónico a través de la red.

### <a name="mocks-versus-fakes"></a>Mocks versus Fakes

Hay otro tipo de doble prueba conocido como *simulado*. Mientras que las falsificaciones tienen implementaciones de trabajo, los simulacros vienen sin implementación. Con la ayuda de un marco de objetos ficticios, construimos estos objetos ficticios en tiempo de ejecución y los usamos como dobles de prueba. En esta sección usaremos el marco de simulación de código abierto Moq. Este es un ejemplo sencillo de uso de Moq para crear dinámicamente un doble de prueba para un repositorio de empleados.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Le pedimos a Moq una implementación de IRepository&lt;Employee&gt; y crea una dinámicamente. Podemos llegar al objeto que&lt;&gt; implementa IRepository Employee accediendo a la propiedad Object del objeto Mock&lt;T.&gt; Es este objeto interno que podemos pasar a nuestros controladores, y no sabrán si se trata de una prueba doble o el repositorio real. Podemos invocar métodos en el objeto del mismo lado que invocaríamos métodos en un objeto con una implementación real.

Debe estar preguntándose qué hará el repositorio ficticio cuando invoquemos el método Add. Puesto que no hay ninguna implementación detrás del objeto ficticio, Add no hace nada. No hay una colección concreta detrás de las escenas como la que tuvimos con las falsificaciones que escribimos, por lo que el empleado es descartado. ¿Qué pasa con el valor devuelto de FindById? En este caso, el objeto ficticio hace lo único que puede hacer, que es devolver un valor predeterminado. Puesto que estamos devolviendo un tipo de referencia (un Employee), el valor devuelto es un valor nulo.

Las burlas pueden sonar inútiles; sin embargo, hay dos características más de simulacros de los que no hemos hablado. En primer lugar, el marco Moq registra todas las llamadas realizadas en el objeto ficticio. Más adelante en el código podemos preguntar a Moq si alguien invocó el método Add o si alguien invocó el método FindById. Veremos más adelante cómo podemos usar esta función de grabación de "caja negra" en las pruebas.

La segunda gran característica es cómo podemos usar Moq para programar un objeto ficticio con *expectativas.* Una expectativa indica al objeto ficticio cómo responder a cualquier interacción dada. Por ejemplo, podemos programar una expectativa en nuestro simulacro y decirle que devuelva un objeto employee cuando alguien invoca FindById. El marco de trabajo de Moq utiliza una API de instalación y expresiones lambda para programar estas expectativas.

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

En este ejemplo le pedimos a Moq que cree dinámicamente un repositorio y, a continuación, programamos el repositorio con una expectativa. La expectativa indica al objeto ficticio que devuelva un nuevo objeto employee con un valor Id de 5 cuando alguien invoca el método FindById pasando un valor de 5. Esta prueba pasa y no necesitábamos crear una implementación&lt;completa&gt;para falsificar IRepository T .

Repasemos las pruebas que escribimos anteriormente y reelaboremos para usar simulacros en lugar de falsificaciones. Al igual que antes, usaremos una clase base para configurar las piezas comunes de infraestructura que necesitamos para todas las pruebas del controlador.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

El código de configuración sigue siendo principalmente el mismo. En lugar de usar falsificaciones, usaremos Moq para construir objetos ficticios. La clase base organiza que la unidad de trabajo simulada devuelva un repositorio ficticio cuando el código invoca la propiedad Employees. El resto de la configuración simulada tendrá lugar dentro de los accesorios de prueba dedicados a cada escenario específico. Por ejemplo, el accesorio de prueba para la acción Index configurará el repositorio ficticio para devolver una lista de empleados cuando la acción invoque el método FindAll del repositorio ficticio.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

Excepto por las expectativas, nuestras pruebas son similares a las que teníamos antes. Sin embargo, con la capacidad de grabación de un marco ficticio podemos abordar las pruebas desde un ángulo diferente. Veremos esta nueva perspectiva en la siguiente sección.

### <a name="state-versus-interaction-testing"></a>Pruebas de Estado versus Interacciones

Hay diferentes técnicas que puede utilizar para probar software con objetos ficticios. Un enfoque es utilizar pruebas basadas en el estado, que es lo que hemos hecho en este documento hasta ahora. Las pruebas basadas en estado hacen afirmaciones sobre el estado del software. En la última prueba invocamos un método de acción en el controlador y realizamos una aserción sobre el modelo que debe compilar. Estos son algunos otros ejemplos de estado de prueba:

-   Compruebe que el repositorio contiene el nuevo objeto employee después de ejecutar Create.
-   Compruebe que el modelo contiene una lista de todos los empleados después de que se ejecute Index.
-   Compruebe que el repositorio no contiene un empleado determinado después de ejecutar Delete.

Otro enfoque que verá con objetos ficticios es verificar las *interacciones.* Mientras que las pruebas basadas en estado hacen aserciones sobre el estado de los objetos, las pruebas basadas en interacciones hacen aserciones sobre cómo interactúan los objetos. Por ejemplo:

-   Compruebe que el controlador invoca el método Add del repositorio cuando Se ejecuta Create.
-   Compruebe que el controlador invoca el método FindAll del repositorio cuando se ejecuta Index.
-   Compruebe que el controlador invoca el método Commit de la unidad de trabajo para guardar los cambios cuando se ejecuta Edit.

Las pruebas de interacción a menudo requieren menos datos de prueba, porque no estamos husmeando dentro de las colecciones y verificando los recuentos. Por ejemplo, si sabemos que la acción Details invoca el método FindById de un repositorio con el valor correcto, es probable que la acción se comporte correctamente. Podemos comprobar este comportamiento sin configurar ningún dato de prueba para volver de FindById.

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

La única configuración requerida en el accesorio de prueba anterior es la configuración proporcionada por la clase base. Cuando invocamos la acción del controlador, Moq registrará las interacciones con el repositorio ficticio. Mediante la API Verify de Moq, podemos preguntar a Moq si el controlador invocó FindById con el valor de ID adecuado. Si el controlador no invocó el método o invocó el método con un valor de parámetro inesperado, el método Verify producirá una excepción y se producirá un error en la prueba.

Este es otro ejemplo para comprobar que la acción Crear invoca Confirmar en la unidad de trabajo actual.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Un peligro con las pruebas de interacción es la tendencia a especificar interacciones en exceso. La capacidad del objeto ficticio para registrar y comprobar cada interacción con el objeto ficticio no significa que la prueba debe intentar comprobar cada interacción. Algunas interacciones son detalles de implementación y solo debe comprobar las interacciones *necesarias* para satisfacer la prueba actual.

La elección entre simulacros o falsificaciones depende en gran medida del sistema que esté probando y de sus preferencias personales (o de equipo). Los objetos ficticios pueden reducir drásticamente la cantidad de código que necesita para implementar dobles de prueba, pero no todo el mundo se siente cómodo programando las expectativas y verificando las interacciones.

## <a name="conclusions"></a>Conclusiones

En este artículo hemos demostrado varios enfoques para crear código comprobable mientras se usa el ADO.NET Entity Framework para la persistencia de datos. Podemos aprovechar las abstracciones integradas como&lt;&gt;IObjectSet T , o crear&lt;nuestras propias abstracciones como IRepository T&gt;.En ambos casos, la compatibilidad con POCO en la ADO.NET Entity Framework 4.0 permite a los consumidores de estas abstracciones permanecer ignorantes persistentes y altamente comprobables. Las características adicionales de EF4, como la carga diferida implícita, permiten que el código de servicio empresarial y de aplicaciones funcione sin preocuparse por los detalles de un almacén de datos relacional. Por último, las abstracciones que creamos son fáciles de simular o falsas dentro de las pruebas unitarias, y podemos usar estos dobles de prueba para lograr pruebas de funcionamiento rápido, altamente aisladas y confiables.

### <a name="additional-resources"></a>Recursos adicionales

-   Robert C. Martin, "El principio de [responsabilidad única"](https://www.objectmentor.com/resources/articles/srp.pdf)
-   Martin Fowler, [Catálogo de patrones](https://www.martinfowler.com/eaaCatalog/index.html) de patrones de arquitectura de aplicaciones *empresariales*
-   Griffin Caprio, ["Inyección](https://msdn.microsoft.com/magazine/cc163739.aspx)de dependencia"
-   Blog de programación de datos, " [Tutorial: Desarrollo controlado por pruebas con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".
-   Blog de programación de datos, " Uso de patrones de [repositorio y unidad de trabajo con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   Aaron Jensen, ["Introduciendo las Especificaciones de la Máquina"](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)
-   Eric Lee, ["BDD con MSTest"](https://saintgimp.org/2009/01/20/bdd-with-mstest/)
-   Eric Evans, " [Diseño impulsado por dominio](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, ["Los bofitos no son stubs"](https://martinfowler.com/articles/mocksArentStubs.html)
-   Martin Fowler, " [Prueba Doble](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>Biografía

Scott Allen es miembro del personal técnico de Pluralsight y fundador de OdeToCode.com. En 15 años de desarrollo de software comercial, Scott ha trabajado en soluciones para todo, desde dispositivos integrados de 8 bits hasta aplicaciones web ASP.NET altamente escalables. Puede comunicarse con Scott en su blog en [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)OdeToCode, o en Twitter en .
