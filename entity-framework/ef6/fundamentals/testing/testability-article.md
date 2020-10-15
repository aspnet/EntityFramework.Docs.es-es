---
title: Capacidad de prueba y Entity Framework 4,0-EF6
description: Capacidad de prueba y Entity Framework 4,0
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: 8a9c8fbf7d6131498bd9b37567fdb90010b808cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062989"
---
# <a name="testability-and-entity-framework-40"></a>Capacidad de prueba y Entity Framework 4,0
Scott Allen

Publicación: mayo de 2010

## <a name="introduction"></a>Introducción

En estas notas del producto se describe y se muestra cómo escribir código que se pueda probar con ADO.NET Entity Framework 4,0 y Visual Studio 2010. Este documento no intenta centrarse en una metodología de prueba específica, como el diseño basado en pruebas (TDD) o el diseño controlado por comportamientos (BDD). En su lugar, este documento se centrará en cómo escribir código que use el Entity Framework de ADO.NET, pero sigue siendo fácil aislar y probar de forma automatizada. Veremos patrones de diseño comunes que facilitan las pruebas en escenarios de acceso a datos y ven cómo aplicar esos patrones al usar el marco de trabajo. También veremos características específicas del marco de trabajo para ver cómo estas características pueden funcionar en código comprobable.

## <a name="what-is-testable-code"></a>¿Qué es el código que se pueda probar?

La capacidad de comprobar una parte del software mediante pruebas unitarias automatizadas ofrece muchas ventajas deseadas. Todo el mundo sabe que las buenas pruebas reducirán el número de defectos de software de una aplicación y aumentarán la calidad de la aplicación, pero si tiene pruebas unitarias en su lugar, más allá de buscar errores.

Un buen conjunto de pruebas unitarias permite que un equipo de desarrollo Ahorre tiempo y mantenga el control del software que crean. Un equipo puede realizar cambios en el código existente, refactorizar, rediseñar y reestructurar el software para cumplir los requisitos nuevos y agregar nuevos componentes a una aplicación, al tiempo que sabe que el conjunto de pruebas puede comprobar el comportamiento de la aplicación. Las pruebas unitarias forman parte de un ciclo de comentarios rápido para facilitar el cambio y preservar el mantenimiento del software a medida que aumenta la complejidad.

Sin embargo, las pruebas unitarias incluyen un precio. Un equipo tiene que invertir el tiempo de creación y mantenimiento de las pruebas unitarias. La cantidad de esfuerzo necesaria para crear estas pruebas está directamente relacionada con la capacidad de **prueba** del software subyacente. ¿Es fácil probar el software? Un equipo que diseñe software con capacidad de prueba en mente creará pruebas eficaces más rápido que el equipo que trabaja con un software no comprobable.

Microsoft diseñó el ADO.NET Entity Framework 4,0 (EF4), teniendo en cuenta la capacidad de prueba. Esto no significa que los desarrolladores vayan a escribir pruebas unitarias en el propio código del marco. En su lugar, los objetivos de capacidad de prueba de EF4 facilitan la creación de código comprobable que se basa en el marco de trabajo. Antes de ver ejemplos específicos, merece la pena comprender las cualidades del código comprobable.

### <a name="the-qualities-of-testable-code"></a>Las cualidades del código comprobable

El código que es fácil de probar siempre presentará al menos dos rasgos. En primer lugar, el código comprobable se **observa**fácilmente. Dado un conjunto de entradas, debe ser fácil observar la salida del código. Por ejemplo, es fácil probar el método siguiente porque el método devuelve directamente el resultado de un cálculo.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Probar un método es difícil si el método escribe el valor calculado en un socket de red, una tabla de base de datos o un archivo como el código siguiente. La prueba tiene que realizar trabajo adicional para recuperar el valor.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

En segundo lugar, el código comprobable es fácil de **aislar**. Vamos a usar el siguiente pseudocódigo como ejemplo incorrecto de código comprobable.

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

El método es fácil de observar: podemos pasar una directiva de seguros y comprobar que el valor devuelto coincide con un resultado esperado. Sin embargo, para probar el método, es necesario tener una base de datos de instalada con el esquema correcto y configurar el servidor SMTP en caso de que el método intente enviar un correo electrónico.

La prueba unitaria solo desea comprobar la lógica de cálculo dentro del método, pero puede producirse un error en la prueba porque el servidor de correo electrónico está sin conexión o porque se ha desconectado el servidor de base de datos. Ambos errores no están relacionados con el comportamiento que la prueba desea comprobar. Es difícil aislar el comportamiento.

Los desarrolladores de software que se esfuerzan por escribir código comprobable a menudo esfuerzan por mantener una separación de preocupaciones en el código que escriben. El método anterior debe centrarse en los cálculos empresariales y delegar la base de datos y los detalles de implementación de correo electrónico a otros componentes. Robert C. Martin llama a este principio de responsabilidad única. Un objeto debe encapsular una única responsabilidad estrecha, como calcular el valor de una directiva. El resto de la base de datos y el trabajo de notificación deben ser responsabilidad de algún otro objeto. El código escrito de este modo es más fácil de aislar porque se centra en una sola tarea.

En .NET tenemos las abstracciones que necesitamos seguir el principio de responsabilidad única y conseguir el aislamiento. Podemos usar las definiciones de interfaz y forzar que el código use la abstracción de la interfaz en lugar de un tipo concreto. Más adelante en este documento veremos cómo un método como el ejemplo incorrecto presentado anteriormente puede funcionar con interfaces que *parecen* que se comunicarán con la base de datos. Sin embargo, en el momento de la prueba, podemos sustituir una implementación ficticia que no se comunica con la base de datos, sino que almacena los datos en la memoria. Esta implementación ficticia aislará el código de problemas no relacionados en el código de acceso a datos o la configuración de la base de datos.

Existen ventajas adicionales para el aislamiento. El cálculo empresarial en el último método solo debe tardar unos milisegundos en ejecutarse, pero la propia prueba podría ejecutarse durante varios segundos a medida que el código se salto alrededor de la red y se comunique con varios servidores. Las pruebas unitarias se deben ejecutar rápidamente para facilitar pequeños cambios. Las pruebas unitarias también se deben repetir y no generar un error porque un componente no relacionado con la prueba tiene un problema. Escribir código que sea fácil de observar y aislar significa que los desarrolladores tendrán un tiempo más sencillo escribiendo las pruebas para el código, dedique menos tiempo a esperar a que se ejecuten las pruebas y, lo que es más importante, dedique menos tiempo a realizar un seguimiento de los errores que no existen.

Espero que pueda apreciar las ventajas de las pruebas y comprender las cualidades que exhibe el código. Estamos a punto de tratar cómo escribir código que funcione con EF4 para guardar datos en una base de datos, mientras que el resto es visible y fácil aislar, pero en primer lugar limitaremos nuestro enfoque para analizar los diseños que se pueden probar para el acceso a los datos.

## <a name="design-patterns-for-data-persistence"></a>Modelos de diseño para la persistencia de datos

Los dos ejemplos no válidos que se presentaron anteriormente tenían demasiadas responsabilidades. El primer ejemplo incorrecto tenía que realizar un cálculo *y* escribir en un archivo. El segundo ejemplo incorrecto tenía que leer los datos de una base de datos *y* realizar un cálculo empresarial *y* enviar un correo electrónico. Al diseñar métodos más pequeños que separan los problemas y delegar la responsabilidad en otros componentes, hará grandes progresos en la escritura de código comprobable. El objetivo es crear la funcionalidad mediante la composición de acciones a partir de abstracciones pequeñas y centradas.

En cuanto a la persistencia de los datos, las abstracciones pequeñas y centradas que se buscan son tan comunes que se han documentado como modelos de diseño. Los patrones de libros de Martin Fowler de la arquitectura de aplicaciones empresariales fueron el primer trabajo para describir estos patrones en la impresión. Proporcionaremos una breve descripción de estos patrones en las secciones siguientes antes de mostrar cómo estos ADO.NET Entity Framework implementan y funcionan con estos patrones.

### <a name="the-repository-pattern"></a>The Repository Pattern (El modelo de repositorio)

Fowler indica un repositorio "media entre las capas de asignación de datos y dominio mediante una interfaz similar a la colección para tener acceso a los objetos de dominio". El objetivo del patrón de repositorio es aislar el código del Minutiae de acceso a datos y, como vimos, el aislamiento anterior es un rasgo necesario para la prueba.

La clave del aislamiento es cómo expone los objetos el repositorio mediante una interfaz similar a la de una colección. La lógica que escriba para usar el repositorio no tiene ninguna idea de cómo el repositorio materializará los objetos que solicite. El repositorio puede comunicarse con una base de datos o simplemente devolver objetos de una colección en memoria. Todo el código debe saber que el repositorio parece mantener la colección, y puede recuperar, agregar y eliminar objetos de la colección.

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

Realizaremos algunos cambios en la definición de interfaz cuando se proporciona una implementación para EF4, pero el concepto básico sigue siendo el mismo. El código puede usar un repositorio concreto que implemente esta interfaz para recuperar una entidad por su valor de clave principal, para recuperar una colección de entidades en función de la evaluación de un predicado, o simplemente para recuperar todas las entidades disponibles. El código también puede Agregar y quitar entidades a través de la interfaz del repositorio.

Dado un IRepository de objetos de empleado, el código puede realizar las siguientes operaciones.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Dado que el código está usando una interfaz (IRepository de Employee), podemos proporcionar el código con diferentes implementaciones de la interfaz. Una implementación puede ser una implementación respaldada por EF4 y almacenar objetos en una base de datos Microsoft SQL Server. Una implementación diferente (una que usamos durante las pruebas) puede estar respaldada por una lista en memoria de objetos de empleado. La interfaz le ayudará a lograr el aislamiento en el código.

Observe que la &lt; interfaz IRepository T &gt; no expone una operación de guardar. ¿Cómo se actualizan los objetos existentes? Puede que se encuentre entre las definiciones de IRepository que incluyen la operación de guardar, y las implementaciones de estos repositorios deberán conservar de forma inmediata un objeto en la base de datos. Sin embargo, en muchas aplicaciones no queremos conservar los objetos individualmente. En su lugar, queremos traer objetos a la vida, quizás desde diferentes repositorios, modificar esos objetos como parte de una actividad económica y, a continuación, conservar todos los objetos como parte de una única operación atómica. Afortunadamente, hay un patrón que permite este tipo de comportamiento.

### <a name="the-unit-of-work-pattern"></a>Patrón de unidad de trabajo

Fowler indica que una unidad de trabajo "mantendrá una lista de objetos afectados por una transacción empresarial y coordina la escritura de los cambios y la resolución de los problemas de simultaneidad". Es responsabilidad de la unidad de trabajo realizar un seguimiento de los cambios en los objetos que aportamos a la vida desde un repositorio y que conservan los cambios realizados en los objetos cuando se indica a la unidad de trabajo que confirme los cambios. También es responsabilidad de la unidad de trabajo realizar los nuevos objetos que hemos agregado a todos los repositorios e insertar los objetos en una base de datos, así como administrar la eliminación.

Si alguna vez ha realizado algún trabajo con conjuntos de valores de ADO.NET, ya estará familiarizado con el patrón de unidad de trabajo. Los conjuntos de datos de ADO.NET tenían la capacidad de realizar un seguimiento de las actualizaciones, eliminaciones e inserción de objetos DataRow y podrían (con la ayuda de un TableAdapter) conciliar todos los cambios en una base de datos. Sin embargo, los objetos DataSet modelan un subconjunto desconectado de la base de datos subyacente. El patrón de unidad de trabajo exhibe el mismo comportamiento, pero funciona con objetos de negocio y objetos de dominio que están aislados del código de acceso a datos y sin tener en cuenta la base de datos.

Una abstracción para modelar la unidad de trabajo en código .NET podría ser similar a la siguiente:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

Al exponer las referencias del repositorio a partir de la unidad de trabajo, se puede asegurar de que un solo objeto de unidad de trabajo tiene la capacidad de realizar un seguimiento de todas las entidades materializadas durante una transacción empresarial. La implementación del método commit para una unidad de trabajo real es donde se produce toda la instrucción mágica para conciliar los cambios en memoria con la base de datos. 

Dada una referencia IUnitOfWork, el código puede realizar cambios en los objetos comerciales recuperados de uno o varios repositorios y guardar todos los cambios mediante la operación de confirmación atómica.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>El modelo de carga diferida

Fowler usa el nombre Lazy LOAD para describir "un objeto que no contiene todos los datos que necesita, pero sabe cómo obtenerlo". La carga diferida transparente es una característica importante que se debe tener al escribir código empresarial comprobable y al trabajar con una base de datos relacional. Como ejemplo, considere el siguiente código.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

¿Cómo se rellena la colección de tarjetas de horas? Hay dos posibles respuestas. Una respuesta es que el repositorio del empleado, cuando se le pide que capture un empleado, emite una consulta para recuperar el empleado junto con la información de la tarjeta de tiempo asociada al empleado. En las bases de datos relacionales, esto normalmente requiere una consulta con una cláusula JOIN y puede dar lugar a la recuperación de más información de la que necesita una aplicación. ¿Qué ocurre si la aplicación no necesita tocar la propiedad de las tarjetas de información.

Una segunda respuesta es cargar la propiedad "a petición" de las tarjetas de horas. Esta carga diferida es implícita y transparente para la lógica de negocios, ya que el código no invoca API especiales para recuperar la información de la tarjeta de tiempo. El código asume que la información de la tarjeta de tiempo está presente cuando sea necesario. Hay una especial participación en la carga diferida que generalmente implica la interceptación en tiempo de ejecución de las invocaciones de método. El código de interceptación es responsable de comunicarse con la base de datos y recuperar la información de la tarjeta de tiempo, a la vez que la lógica de negocios queda libre para ser lógica empresarial. Esta magia de carga diferida permite al código de negocio aislarse de las operaciones de recuperación de datos y da como resultado un código más comprobable.

El inconveniente de una carga diferida es que cuando *una aplicación necesita* la información de la tarjeta de tiempo, el código ejecutará una consulta adicional. Esto no supone un problema para muchas aplicaciones, pero para las aplicaciones o aplicaciones sensibles al rendimiento se repiten por un número de objetos de empleado y la ejecución de una consulta para recuperar las tarjetas de tiempo durante cada iteración del bucle (un problema a menudo conocido como el problema de consulta N + 1), la carga diferida es un arrastre. En estos escenarios, es posible que una aplicación quiera cargar la información de la tarjeta de tiempo de la manera más eficaz posible.

Afortunadamente, veremos cómo EF4 admite las cargas diferidas implícitas y las cargas diligentes eficaces a medida que avanzamos en la siguiente sección e implementamos estos patrones.

## <a name="implementing-patterns-with-the-entity-framework"></a>Implementar patrones con el Entity Framework

La buena noticia es que todos los patrones de diseño que se describen en la última sección son sencillos de implementar con EF4. Para demostrar que vamos a usar una sencilla aplicación ASP.NET MVC para editar y mostrar los empleados y la información de su tarjeta de tiempo asociada. Comenzaremos usando los siguientes "objetos CLR antiguos sin formato" (POCO). 

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

Estas definiciones de clase cambiarán ligeramente a medida que exploramos diferentes enfoques y características de EF4, pero el objetivo es mantener estas clases como la persistencia ignorada (PI) como sea posible. Un objeto PI no sabe *Cómo*, o incluso *si*, el estado que contiene reside en una base de datos. PI y POCO go están a mano con el software que se pueda probar. Los objetos que usan un enfoque POCO son menos restrictivos, más flexibles y fáciles de probar porque pueden funcionar sin una base de datos presente.

Con los POCO en vigor, podemos crear un Entity Data Model (EDM) en Visual Studio (vea la ilustración 1). No usaremos el EDM para generar código para nuestras entidades. En su lugar, queremos usar las entidades que lovinglymos de forma manual. Solo usaremos el EDM para generar el esquema de la base de datos y proporcionar los metadatos que EF4 necesita para asignar objetos a la base de datos.

![test_01 EF](~/ef6/media/eftest-01.jpg)

**Ilustración 1**

Nota: Si desea desarrollar el modelo EDM en primer lugar, es posible generar código limpio y POCO a partir del EDM. Puede hacerlo con una extensión de Visual Studio 2010 proporcionada por el equipo de programación de datos. Para descargar la extensión, inicie el administrador de extensiones desde el menú herramientas de Visual Studio y busque "POCO" en la galería en línea de plantillas (consulte la figura 2). Hay varias plantillas POCO disponibles para EF. Para obtener más información sobre el uso de la plantilla, vea " [Tutorial: poco plantilla para el Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".

![test_02 EF](~/ef6/media/eftest-02.png)

**Ilustración 2**

Desde este punto de partida POCO, exploraremos dos enfoques diferentes para el código comprobable. El primer enfoque que se llama al enfoque EF porque aprovecha las abstracciones de la API de Entity Framework para implementar unidades de trabajo y repositorios. En el segundo enfoque, crearemos nuestras propias abstracciones de repositorio personalizadas y, a continuación, veremos las ventajas y desventajas de cada enfoque. Comenzaremos explorando el enfoque de EF.  

### <a name="an-ef-centric-implementation"></a>Una implementación centrada en EF

Considere la siguiente acción del controlador de un proyecto de MVC de ASP.NET. La acción recupera un objeto de empleado y devuelve un resultado para mostrar una vista detallada del empleado.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

¿Se va a probar el código? Hay al menos dos pruebas que necesitamos para comprobar el comportamiento de la acción. En primer lugar, nos gustaría comprobar que la acción devuelve la vista correcta (una prueba sencilla). También deseamos escribir una prueba para comprobar que la acción recupera el empleado correcto y nos gustaría hacerlo sin ejecutar código para consultar la base de datos. Recuerde que queremos aislar el código sometido a prueba. El aislamiento garantizará que la prueba no produzca un error debido a un error en el código de acceso a datos o la configuración de la base de datos. Si se produce un error en la prueba, se sabrá que tenemos un error en la lógica del controlador y no en un componente del sistema de nivel inferior.

Para lograr el aislamiento, necesitamos algunas abstracciones como las interfaces que presentamos anteriormente para los repositorios y las unidades de trabajo. Recuerde que el patrón de repositorio está diseñado para mediar entre objetos de dominio y la capa de asignación de datos. En este escenario, EF4 *es* la capa de asignación de datos y ya proporciona una abstracción similar a la del repositorio denominada IObjectSet &lt; T &gt; (del espacio de nombres System. Data. Objects). La definición de la interfaz es similar a la siguiente.

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

IObjectSet &lt; T &gt; cumple los requisitos de un repositorio porque se parece a una colección de objetos (a través de IEnumerable &lt; T &gt; ) y proporciona métodos para agregar y quitar objetos de la colección simulada. Los métodos Attach y detach exponen funcionalidades adicionales de la API de EF4. Para usar IObjectSet &lt; T &gt; como interfaz para los repositorios, se necesita una abstracción de unidad de trabajo para enlazar repositorios juntos.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Una implementación concreta de esta interfaz se comunicará con SQL Server y es fácil de crear mediante la clase ObjectContext desde EF4. La clase ObjectContext es la unidad real de trabajo de la API de EF4.

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

Llevar un IObjectSet &lt; T &gt; a la vida es tan sencillo como invocar el método método createobjectset del objeto ObjectContext. En segundo plano, el marco de trabajo usará los metadatos proporcionados en el EDM para producir un ObjectSet &lt; T concreto &gt; . Nos centraremos en la devolución de &lt; la &gt; interfaz IObjectSet T porque le ayudará a mantener la capacidad de prueba en el código de cliente.

Esta implementación concreta es útil en producción, pero es necesario centrarnos en cómo usaremos nuestra abstracción IUnitOfWork para facilitar las pruebas.

### <a name="the-test-doubles"></a>La prueba se duplica

Para aislar la acción del controlador, se necesita la capacidad de cambiar entre la unidad de trabajo real (respaldada por un ObjectContext) y una unidad de trabajo de prueba doble o "falsa" (realizando operaciones en memoria). El método común para realizar este tipo de conmutación es no permitir que el controlador de MVC cree una instancia de una unidad de trabajo, sino que en su lugar pasa la unidad de trabajo al controlador como un parámetro de constructor.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

El código anterior es un ejemplo de inserción de dependencias. No permitimos que el controlador cree su dependencia (la unidad de trabajo), sino que inserte la dependencia en el controlador. En un proyecto de MVC, es habitual usar un generador de controlador personalizado en combinación con un contenedor de inversión de control (IoC) para automatizar la inserción de dependencias. Estos temas están fuera del ámbito de este artículo, pero puede leer más mediante las referencias que se indican al final de este artículo.

Una implementación de unidad de trabajo falsa que se puede usar para las pruebas podría ser similar a la siguiente.

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

Observe que la unidad de trabajo falsa expone una propiedad confirmada. A veces resulta útil agregar características a una clase falsa que facilitan las pruebas. En este caso, es fácil observar si el código confirma una unidad de trabajo mediante la comprobación de la propiedad confirmada.

También se necesitará una IObjectSet falsa &lt; &gt; para almacenar los objetos de los empleados y las tarjetas de la tarjeta de la memoria. Se puede proporcionar una implementación única mediante genéricos.

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

Esta prueba Double delega la mayor parte de su trabajo en un &lt; objeto HashSet T subyacente &gt; . Tenga en cuenta que IObjectSet &lt; T &gt; requiere una restricción genérica que aplique t como clase (un tipo de referencia) y también nos obliga a implementar IQueryable &lt; T &gt; . Es fácil hacer que una colección en memoria aparezca como IQueryable &lt; T &gt; mediante el operador estándar LINQ que se pueda consultar.

### <a name="the-tests"></a>Las pruebas

Las pruebas unitarias tradicionales usarán una sola clase de prueba para contener todas las pruebas de todas las acciones en un solo controlador de MVC. Podemos escribir estas pruebas, o cualquier tipo de prueba unitaria, mediante el uso de las falsificaciones de memoria que hemos creado. Sin embargo, en este artículo se evitará el enfoque de la clase de prueba monolítica y, en su lugar, se agruparán las pruebas para centrarse en una parte específica de la funcionalidad.Por ejemplo, "crear nuevo empleado" podría ser la funcionalidad que queremos probar, por lo que usaremos una sola clase de prueba para comprobar la acción de controlador único responsable de crear un nuevo empleado.

Hay algún código de instalación común que necesitamos para todas estas clases de prueba concretas. Por ejemplo, siempre tenemos que crear los repositorios en memoria y la unidad de trabajo falsa. También se necesita una instancia del controlador de empleados con la unidad falsa de trabajo insertada. Se compartirá este código de instalación común en todas las clases de prueba mediante una clase base.

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

El "objeto madre" que usamos en la clase base es un patrón común para crear datos de prueba. Un objeto Mother contiene métodos de generador para crear instancias de entidades de prueba para su uso en varios extras de prueba.

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

Podemos usar EmployeeControllerTestBase como la clase base para una serie de accesorios de prueba (vea la figura 3). Cada accesorio de prueba probará una acción de controlador específica. Por ejemplo, un accesorio de prueba se centrará en probar la acción de creación que se usa durante una solicitud GET de HTTP (para mostrar la vista de creación de un empleado) y un accesorio diferente se centrará en la acción de creación usada en una solicitud HTTP POST (para que el usuario envíe la información para crear un empleado). Cada clase derivada solo es responsable de la configuración necesaria en su contexto específico y de proporcionar las aserciones necesarias para comprobar los resultados de su contexto de prueba específico.

![test_03 EF](~/ef6/media/eftest-03.png)

**Ilustración 3**

La Convención de nomenclatura y el estilo de prueba presentados aquí no son necesarios para el código comprobable, sino solo un enfoque. En la figura 4 se muestran las pruebas que se ejecutan en el complemento del Ejecutor de pruebas de jet cerebro ReSharper para Visual Studio 2010.

![test_04 EF](~/ef6/media/eftest-04.png)

**Figura 4**

Con una clase base para controlar el código compartido de la instalación, las pruebas unitarias para cada acción de controlador son pequeñas y fáciles de escribir. Las pruebas se ejecutarán rápidamente (dado que se están realizando operaciones en memoria) y no se debería producir un error debido a la infraestructura no relacionada o a problemas ambientales (porque hemos aislado la unidad en pruebas).

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

En estas pruebas, la clase base realiza la mayor parte del trabajo de configuración. Recuerde que el constructor de clase base crea el repositorio en memoria, una unidad de trabajo falsa y una instancia de la clase EmployeeController. La clase de prueba se deriva de esta clase base y se centra en los detalles de la prueba del método Create. En este caso, las características específicas se reducen a los pasos "Arrange, Act y Assert" que verá en cualquier procedimiento de prueba unitaria:

-   Cree un objeto newEmployee para simular los datos entrantes.
-   Invocar la acción de creación de EmployeeController y pasar newEmployee.
-   Compruebe que la acción crear produce los resultados esperados (el empleado aparece en el repositorio).

Lo que hemos creado nos permite probar cualquiera de las acciones de EmployeeController. Por ejemplo, cuando se escriben pruebas para la acción de índice del controlador de empleado, se puede heredar de la clase base de prueba para establecer la misma configuración base para nuestras pruebas. De nuevo, la clase base creará el repositorio en memoria, la unidad de trabajo falsa y una instancia de EmployeeController. Las pruebas para la acción de índice solo deben centrarse en la invocación de la acción de índice y en la prueba de las cualidades del modelo que devuelve la acción.

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

Las pruebas que creamos con las falsificaciones en memoria están orientadas a probar el *Estado* del software. Por ejemplo, al probar la acción de creación, queremos inspeccionar el estado del repositorio después de que se ejecute la acción de creación: ¿el repositorio mantiene el nuevo empleado?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

Más adelante veremos las pruebas basadas en la interacción. Las pruebas basadas en interacción le preguntarán si el código sometido a prueba invocó los métodos adecuados en nuestros objetos y pasa los parámetros correctos. Por ahora, pasaremos a la portada otro patrón de diseño: la carga diferida.

## <a name="eager-loading-and-lazy-loading"></a>Carga diligente y carga diferida

En algún momento de la aplicación web MVC de ASP.NET podríamos querer Mostrar la información de un empleado e incluir las tarjetas de tiempo asociadas del empleado. Por ejemplo, es posible que tengamos una pantalla de Resumen de tarjeta de tiempo que muestre el nombre del empleado y el número total de tarjetas de tiempo del sistema. Existen varios enfoques que se pueden seguir para implementar esta característica.

### <a name="projection"></a>Proyección

Un enfoque sencillo para crear el resumen es construir un modelo dedicado a la información que queremos mostrar en la vista. En este escenario, el modelo podría ser similar al siguiente.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Tenga en cuenta que EmployeeSummaryViewModel no es una entidad; es decir, no es algo que queremos conservar en la base de datos. Solo vamos a usar esta clase para ordenar los datos en la vista de forma fuertemente tipada. El modelo de vista es como un objeto de transferencia de datos (DTO) porque no contiene ningún comportamiento (sin métodos): solo propiedades. Las propiedades contendrán los datos que necesitamos trasladar. Es fácil crear instancias de este modelo de vista mediante el operador de proyección estándar de LINQ: el operador Select.

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

Hay dos características importantes para el código anterior. En primer lugar, el código es fácil de probar porque todavía es fácil de observar y aislar. El operador Select funciona igual que en las falsificaciones en memoria que en la unidad de trabajo real.

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

La segunda característica importante es la forma en que el código permite a EF4 generar una consulta única y eficaz para ensamblar la información de los empleados y la tarjeta de tiempo conjuntamente. Hemos cargado información de empleados e información de la tarjeta de tiempo en el mismo objeto sin usar ninguna API especial. El código simplemente expresó la información necesaria para usar operadores LINQ estándar que funcionan con orígenes de datos en memoria y con orígenes de datos remotos. EF4 pudo traducir los árboles de expresión generados por la consulta LINQ y el \# compilador de C en una consulta T-SQL eficaz y única.

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

Hay otras ocasiones en las que no se desea trabajar con un modelo de vista o un objeto DTO, sino con entidades reales. Cuando sabemos que necesitamos un empleado *y* las tarjetas de tiempo del empleado, podemos cargar diligentemente los datos relacionados de manera discreta y eficaz.

### <a name="explicit-eager-loading"></a>Carga diligente explícita

Cuando queremos cargar diligentemente la información relacionada de la entidad, necesitamos algún mecanismo para la lógica de negocios (o en este escenario, la lógica de acción del controlador) para expresar su deseo en el repositorio. La clase EF4 ObjectQuery &lt; T &gt; define un método include para especificar los objetos relacionados que se van a recuperar durante una consulta. Recuerde que EF4 ObjectContext expone entidades a través de la &lt; clase de ObjectSet t concreta, &gt; que hereda de ObjectQuery &lt; t &gt; .Si usamos &lt; referencias de ObjectSet T &gt; en nuestra acción del controlador, podríamos escribir el código siguiente para especificar una carga diligente de información de la tarjeta de tiempo para cada empleado.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Sin embargo, puesto que estamos intentando mantener el código comprobable, no exponemos &lt; el ObjectSet T &gt; desde fuera de la clase real de la unidad de trabajo. En su lugar, confiamos en la &lt; interfaz IObjectSet T &gt; , que es más fácil de falsificar, pero IObjectSet &lt; T &gt; no define un método include. La belleza de LINQ es que podemos crear nuestro propio operador include.

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

Observe que este operador include se define como un método de extensión para IQueryable &lt; t &gt; en lugar de IObjectSet &lt; T &gt; . Esto nos da la posibilidad de usar el método con una gama más amplia de tipos posibles, incluidos IQueryable &lt; t &gt; , IObjectSet &lt; t &gt; , ObjectQuery &lt; t &gt; y ObjectSet &lt; t &gt; . En el caso de que la secuencia subyacente no sea una copia de EF4 original de ObjectQuery &lt; T &gt; , no habrá ningún daño y el operador include es una operación no operativa. Si la secuencia subyacente *es* un ObjectQuery &lt; t &gt; (o se deriva de OBJECTQUERY &lt; t &gt; ), EF4 verá nuestro requisito de datos adicionales y formulará la consulta SQL adecuada.

Con este nuevo operador en su lugar, podemos solicitar explícitamente una carga diligente de información de la tarjeta de tiempo del repositorio.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Cuando se ejecuta en un ObjectContext real, el código genera la siguiente consulta única. La consulta recopila suficiente información de la base de datos en un recorrido para materializar los objetos de empleado y rellenar completamente su propiedad de tarjetas de seguridad.

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

La gran noticia es que el código dentro del método de acción sigue siendo totalmente comprobable. No es necesario proporcionar ninguna característica adicional para nuestras falsificaciones para admitir el operador include. La mala noticia es que teníamos que usar el operador include en el código que deseamos para mantener la persistencia. Este es un buen ejemplo del tipo de inconvenientes que debe evaluar al compilar código comprobable. Hay ocasiones en las que es necesario dejar que la persistencia tenga pérdidas fuera de la abstracción del repositorio para satisfacer los objetivos de rendimiento.

La alternativa a la carga diligente es la carga diferida. La carga diferida significa que *no* es necesario que nuestro código de negocio anuncie explícitamente el requisito de los datos asociados. En su lugar, usamos nuestras entidades en la aplicación y si se necesitan datos adicionales Entity Framework cargarán los datos a petición.

### <a name="lazy-loading"></a>Carga diferida

Es fácil imaginar un escenario en el que no se sepa qué datos necesitará una lógica de negocios. Podríamos saber que la lógica necesita un objeto de empleado, pero podemos crear una rama en diferentes rutas de ejecución, donde algunas de esas rutas requieren información de tarjeta de tiempo del empleado y otras no. Los escenarios como este son idóneos para la carga diferida implícita porque los datos aparecen de forma mágica según sea necesario.

La carga diferida, también conocida como carga aplazada, impone algunos requisitos en nuestros objetos entidad. Un POCO con persistencia real omisión no tendría ningún requisito de la capa de persistencia, pero la persistencia real omisión es prácticamente imposible de lograr.En su lugar, medimos la persistencia omisión en grados relativos. Sería desafortunable si era necesario heredar de una clase base orientada a la persistencia o usar una colección especializada para lograr la carga diferida en POCO. Afortunadamente, EF4 tiene una solución menos intrusiva.

### <a name="virtually-undetectable"></a>Prácticamente no detectable

Cuando se usan objetos POCO, EF4 puede generar dinámicamente servidores proxy en tiempo de ejecución para las entidades. Estos proxies invisiblemente encapsulan los POCO materializados y proporcionan servicios adicionales interceptando cada operación get y set de cada propiedad para realizar trabajo adicional. Uno de estos servicios es la característica de carga diferida que estamos buscando. Otro servicio es un mecanismo de seguimiento de cambios eficaz que puede grabar cuando el programa cambia los valores de propiedad de una entidad. El ObjectContext usa la lista de cambios durante el método SaveChanges para conservar las entidades modificadas mediante comandos UPDATE.

Sin embargo, para que estos proxies funcionen, necesitan una manera de enlazar las operaciones GET y set de la propiedad en una entidad, y los proxies logran este objetivo mediante la invalidación de los miembros virtuales. Por lo tanto, si queremos tener una carga diferida implícita y un seguimiento de cambios eficaz, es necesario volver a las definiciones de clase POCO y marcar las propiedades como virtuales.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Todavía podemos decir que la entidad Employee es la que ignora la persistencia. El único requisito es usar miembros virtuales y esto no afecta a la capacidad de prueba del código. No es necesario derivar de ninguna clase base especial ni siquiera usar una colección especial dedicada a la carga diferida. Como se muestra en el código, cualquier clase que implemente ICollection &lt; T &gt; está disponible para contener entidades relacionadas.

También hay un pequeño cambio que necesitamos hacer dentro de nuestra unidad de trabajo. La carga diferida está *desactivada* de forma predeterminada cuando se trabaja directamente con un objeto ObjectContext. Hay una propiedad que se puede establecer en la propiedad ContextOptions para habilitar la carga aplazada y podemos establecer esta propiedad dentro de nuestra unidad real de trabajo si queremos habilitar la carga diferida en todas partes.

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

Con la carga diferida implícita habilitada, el código de aplicación puede usar un empleado y las tarjetas de tiempo asociadas del empleado, mientras que el resto de completamente no es consciente del trabajo necesario para que EF cargue los datos adicionales.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

La carga diferida facilita la escritura del código de la aplicación y, con el proxy mágico, el código sigue siendo totalmente comprobable. Las falsificaciones en memoria de la unidad de trabajo pueden simplemente precargar las entidades falsas con datos asociados cuando sea necesario durante una prueba.

Llegados a este punto, nos centraremos en la creación de repositorios con IObjectSet &lt; T &gt; y veremos las abstracciones para ocultar todos los signos del marco de persistencia.

## <a name="custom-repositories"></a>Repositorios personalizados

La primera vez que se presentó el patrón de diseño de unidad de trabajo en este artículo se proporciona código de ejemplo para el aspecto que podría tener la unidad de trabajo. Vamos a presentar esta idea original con el escenario de tarjeta de tiempo empleado y empleado con el que hemos trabajado.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

La principal diferencia entre esta unidad de trabajo y la unidad de trabajo que creamos en la última sección es cómo esta unidad de trabajo no usa ninguna abstracción del marco de EF4 (no hay IObjectSet &lt; T &gt; ). IObjectSet &lt; T &gt; funciona bien como una interfaz de repositorio, pero es posible que la API que expone no se alinee perfectamente con las necesidades de la aplicación. En este próximo enfoque, se representarán los repositorios con una &lt; abstracción de IRepository T personalizada &gt; .

Muchos desarrolladores que siguen el diseño basado en pruebas, el diseño controlado por el comportamiento y las metodologías controladas por el dominio prefieren el &lt; enfoque IRepository T &gt; por varias razones. En primer lugar, &lt; la &gt; interfaz IRepository T representa una capa "contra daños". Tal como se describe en Eric Evans en su libro de diseño controlado por dominios, una capa contra daños mantiene el código de dominio fuera de las API de infraestructura, como una API de persistencia. En segundo lugar, los desarrolladores pueden crear métodos en el repositorio que satisfagan las necesidades exactas de una aplicación (como se detectó al escribir pruebas). Por ejemplo, con frecuencia es necesario buscar una sola entidad con un valor de identificador, por lo que podemos agregar un método FindById a la interfaz de repositorio.Nuestra &lt; &gt; definición de IRepository T tendrá un aspecto similar al siguiente.

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

Observe que volveremos a usar una interfaz IQueryable &lt; T &gt; para exponer las colecciones de entidades. IQueryable &lt; T &gt; permite que los árboles de expresión LINQ fluyan al proveedor EF4 y proporcione al proveedor una vista holística de la consulta. Una segunda opción sería devolver IEnumerable &lt; T &gt; , lo que significa que el proveedor EF4 LINQ solo verá las expresiones compiladas dentro del repositorio. Cualquier agrupación, ordenación y proyección realizadas fuera del repositorio no se creará en el comando SQL enviado a la base de datos, lo que puede afectar negativamente al rendimiento. Por otro lado, un repositorio que devuelva solo &lt; los resultados de IEnumerable T &gt; nunca le sorprenderá con un nuevo comando SQL. Ambos enfoques funcionarán y ambos enfoques seguirán siendo comprobables.

Es sencillo proporcionar una única implementación de la &lt; interfaz IRepository T &gt; mediante genéricos y la API de OBJECTCONTEXT de EF4.

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

El &lt; enfoque IRepository T &gt; nos proporciona un control adicional sobre nuestras consultas porque un cliente tiene que invocar un método para llegar a una entidad. Dentro del método, podríamos proporcionar comprobaciones adicionales y operadores LINQ para aplicar las restricciones de la aplicación. Observe que la interfaz tiene dos restricciones en el parámetro de tipo genérico. La primera restricción es la clase cons que requiere el ObjectSet &lt; T &gt; y la segunda restricción obliga a nuestras entidades a implementar IEntity: una abstracción creada para la aplicación. La interfaz IEntity fuerza a las entidades a tener una propiedad de identificador legible y, a continuación, podemos usar esta propiedad en el método FindById. IEntity se define con el código siguiente.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity podría considerarse una pequeña infracción de la persistencia omisión, ya que las entidades necesitan implementar esta interfaz. Recuerde que la persistencia omisión se refiere a las ventajas y, para muchas de las funciones de FindById, la restricción impuesta por la interfaz. La interfaz no tiene ningún impacto en la capacidad de prueba.

La creación de una instancia &lt; de Live IRepository T &gt; requiere un OBJECTCONTEXT de EF4, por lo que una implementación concreta de la unidad de trabajo debe administrar la creación de instancias.

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

El uso de nuestro repositorio personalizado no es significativamente diferente del uso del repositorio basado en IObjectSet &lt; T &gt; . En lugar de aplicar operadores LINQ directamente a una propiedad, primero debemos invocar los métodos del repositorio para obtener una &lt; referencia IQueryable T &gt; .

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Observe que el operador de inclusión personalizado implementado anteriormente funcionará sin cambios. El método FindById del repositorio quita la lógica duplicada de acciones que intentan recuperar una sola entidad.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

No hay ninguna diferencia significativa en la capacidad de prueba de los dos métodos que hemos examinado. Podríamos proporcionar implementaciones falsas de IRepository &lt; T &gt; mediante la creación de clases concretas respaldadas por &lt; &gt; un empleado de HashSet, exactamente igual que en la última sección. Sin embargo, algunos desarrolladores prefieren usar objetos ficticios y marcos de objetos ficticios en lugar de crear simulaciones. Veremos el uso de simulacros para probar nuestra implementación y analizar las diferencias entre los simulacros y las simulaciones en la sección siguiente.

### <a name="testing-with-mocks"></a>Pruebas con simulacros

Hay diferentes enfoques para crear lo que Martin Fowler llama "Double de prueba". Una prueba Double (como una película Stunt Double) es un objeto que se compila en "out" para objetos reales de producción durante las pruebas. Los repositorios en memoria creados son dobles de pruebas para los repositorios que se comunican con SQL Server. Hemos visto cómo usar estas pruebas dobles durante las pruebas unitarias para aislar el código y mantener la ejecución rápida de las pruebas.

Los dobles de pruebas que hemos creado son implementaciones reales y en funcionamiento. En segundo plano, cada uno almacena una colección concreta de objetos y agrega y quita objetos de esta colección mientras se manipula el repositorio durante una prueba. Algunos desarrolladores como para compilar la prueba se duplican de esta manera: con código real y implementaciones de trabajo.Estos dobles de pruebas son lo que llamamos *imitaciones*. Tienen implementaciones en funcionamiento, pero no son lo suficientemente reales para su uso en producción. El repositorio falso no escribe realmente en la base de datos. El servidor SMTP falso no envía un mensaje de correo electrónico a través de la red.

### <a name="mocks-versus-fakes"></a>Simuladores frente a falsificaciones

Hay otro tipo de prueba doble conocido como *ficticio*. Mientras que las simulaciones tienen implementaciones en funcionamiento, los simulacros se incluyen sin implementación. Con la ayuda de un marco de objeto ficticio, construimos estos objetos ficticios en tiempo de ejecución y los usamos como dobles de pruebas. En esta sección vamos a usar el marco de trabajo ficticio de código abierto MOQ. Este es un ejemplo sencillo del uso de MOQ para crear dinámicamente una prueba Double para un repositorio de empleados.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Solicitamos MOQ para una implementación de empleados de IRepository &lt; &gt; y creamos una dinámicamente. Podemos obtener el objeto que implementa IRepository &lt; Employee &gt; mediante el acceso a la propiedad Object del &lt; objeto ficticio T &gt; . Es este objeto interno que se puede pasar a nuestros controladores y no sabrá si se trata de un doble de prueba o del repositorio real. Se pueden invocar métodos en el objeto del mismo modo que se invocan los métodos en un objeto con una implementación real.

Debe preguntarse lo que hará el repositorio ficticio cuando se invoque el método Add. Dado que no hay ninguna implementación detrás del objeto ficticio, Add no hace nada. No hay ninguna colección concreta en segundo plano como teníamos con las falsificaciones que escribimos, por lo que el empleado se descarta. ¿Qué ocurre con el valor devuelto de FindById? En este caso, el objeto ficticio hace lo único que puede hacer, que devuelve un valor predeterminado. Dado que se devuelve un tipo de referencia (un empleado), el valor devuelto es un valor null.

Los simulacros podrían parecer no útil; sin embargo, hay dos características más de los simulacros que no hemos hablado. En primer lugar, el marco MOQ registra todas las llamadas realizadas en el objeto ficticio. Más adelante en el código, se puede preguntar a MOQ si alguien invocó el método Add o si alguien invocó el método FindById. Más adelante veremos cómo podemos usar esta característica de grabación de "caja negra" en las pruebas.

La segunda característica excelente es cómo se puede usar MOQ para programar un objeto ficticio con las *expectativas*. Una expectativa indica al objeto ficticio cómo responder a cualquier interacción determinada. Por ejemplo, se puede programar una expectativa en nuestro simulacro e indicar a que devuelva un objeto de empleado cuando alguien invoque FindById. El marco de trabajo de MOQ usa una API de instalación y expresiones lambda para programar estas expectativas.

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

En este ejemplo, se pide a MOQ que cree de forma dinámica un repositorio y, a continuación, programemos el repositorio con una expectativa. La expectativa indica al objeto ficticio que devuelva un nuevo objeto de empleado con un valor de identificador de 5 cuando alguien invoque el método FindById pasando un valor de 5. Esta prueba se supera y no es necesario crear una implementación completa para falsificar IRepository &lt; t &gt; .

Vamos a revisar las pruebas que hemos escrito anteriormente y a trabajar con ellas para usar simulacros en lugar de simulaciones. Al igual que antes, usaremos una clase base para configurar los componentes comunes de la infraestructura que necesitamos para todas las pruebas del controlador.

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

El código de instalación se mantiene principalmente igual. En lugar de usar falsificaciones, usaremos MOQ para construir objetos ficticios. La clase base organiza la unidad de trabajo simulada para devolver un repositorio ficticio cuando el código invoca la propiedad Employees. El resto de la configuración del simulacro tendrá lugar dentro de los extras de prueba dedicados a cada escenario específico. Por ejemplo, el accesorio de prueba para la acción de índice configurará el repositorio ficticio para devolver una lista de empleados cuando la acción invoca el método FindAll del repositorio ficticio.

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

A excepción de las expectativas, nuestras pruebas son similares a las pruebas que teníamos antes. Sin embargo, con la capacidad de grabación de un marco ficticio, podemos enfocar las pruebas desde un ángulo diferente. Veremos esta nueva perspectiva en la sección siguiente.

### <a name="state-versus-interaction-testing"></a>Pruebas de estado frente a interacción

Hay distintas técnicas que puede usar para probar el software con objetos ficticios. Un enfoque consiste en usar pruebas basadas en el estado, que es lo que hemos hecho en este documento hasta ahora. Las pruebas basadas en el estado realizan aserciones sobre el estado del software. En la última prueba, se ha invocado un método de acción en el controlador y se ha realizado una aserción sobre el modelo que debe compilar. Estos son algunos ejemplos de estado de prueba:

-   Compruebe que el repositorio contiene el nuevo objeto de empleado después de que se ejecute Create.
-   Compruebe que el modelo contiene una lista de todos los empleados después de que se ejecute el índice.
-   Compruebe que el repositorio no contiene un empleado determinado después de que se ejecute la eliminación.

Otro enfoque que verá con objetos ficticios es comprobar las *interacciones*. Mientras que las pruebas basadas en el estado realizan aserciones sobre el estado de los objetos, las pruebas basadas en la interacción realizan aserciones sobre cómo interactúan los objetos. Por ejemplo:

-   Compruebe que el controlador invoca el método Add del repositorio cuando se ejecuta Create.
-   Compruebe que el controlador invoca el método FindAll del repositorio cuando se ejecuta el índice.
-   Compruebe que el controlador invoca el método commit de la unidad del trabajo para guardar los cambios cuando se ejecuta la edición.

A menudo, las pruebas de interacción requieren menos datos de prueba, porque no se Poking dentro de las colecciones y se comprueban los recuentos. Por ejemplo, si sabemos que la acción de detalles invoca el método FindById de un repositorio con el valor correcto, es probable que la acción se comporte correctamente. Podemos comprobar este comportamiento sin configurar ningún dato de prueba para que se devuelva desde FindById.

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

La única configuración necesaria en el accesorio de prueba anterior es la que proporciona la clase base. Cuando se invoca la acción del controlador, MOQ registrará las interacciones con el repositorio ficticio. Mediante la comprobación de la API de MOQ, podemos preguntar a MOQ si el controlador invocó FindById con el valor de ID. adecuado. Si el controlador no invocó el método o invocó el método con un valor de parámetro inesperado, el método verify producirá una excepción y se producirá un error en la prueba.

Este es otro ejemplo para comprobar que la acción de creación invoca la confirmación en la unidad de trabajo actual.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Un peligro con las pruebas de interacción es la tendencia de especificar las interacciones. La capacidad del objeto ficticio de registrar y comprobar cada interacción con el objeto ficticio no significa que la prueba debe intentar comprobar cada interacción. Algunas interacciones son detalles de la implementación y solo se deben comprobar las interacciones *necesarias* para satisfacer la prueba actual.

La elección entre simulacros o falsificaciones depende en gran medida del sistema que se está probando y de sus preferencias personales (o de equipo). Los objetos ficticios pueden reducir drásticamente la cantidad de código que necesita para implementar los dobles de pruebas, pero no todos se sienten cómodos con la programación de las expectativas y la comprobación de las interacciones.

## <a name="conclusions"></a>Conclusiones

En este documento hemos mostrado varios enfoques para crear código comprobable al usar el Entity Framework ADO.NET para la persistencia de datos. Podemos aprovechar las abstracciones integradas como IObjectSet &lt; t &gt; o crear nuestras propias abstracciones como IRepository &lt; t &gt; .En ambos casos, la compatibilidad POCO en la Entity Framework 4,0 de ADO.NET permite a los consumidores de estas abstracciones seguir siendo ignorables y muy comprobables. Características adicionales de EF4 como la carga diferida implícita permite que el código del servicio de aplicaciones y del negocio funcione sin preocuparse por los detalles de un almacén de datos relacional. Por último, las abstracciones que creamos son fáciles de simular o falsificar dentro de las pruebas unitarias, y podemos usar estos dobles de pruebas para lograr pruebas de ejecución rápida, muy aisladas y confiables.

### <a name="additional-resources"></a>Recursos adicionales

-   Robert C. Martin, " [el principio de responsabilidad única](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler, [Catálogo de patrones](https://www.martinfowler.com/eaaCatalog/index.html) de *patrones de arquitectura de aplicaciones empresariales*
-   Griffin Caprio, " [inyección de dependencia](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   Blog de programación de datos, " [Tutorial: desarrollo controlado por pruebas con el Entity Framework 4,0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".
-   Blog de programación de datos, " [uso de patrones de repositorio y unidad de trabajo con Entity Framework 4,0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   Aaron Jensen, " [Introducción](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)a las especificaciones de la máquina"
-   Eric Lee, " [BDD con MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"
-   Eric Evans, " [diseño controlado por dominios](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, "los [simulacros no son stubs](https://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>Biografía

Scott Allen es miembro del personal técnico de Pluralsight y el fundador de OdeToCode.com. En 15 años de desarrollo de software comercial, Scott ha trabajado en soluciones para todo, desde dispositivos incrustados de 8 bits hasta aplicaciones Web ASP.NET altamente escalables. Puede ponerse en contacto con Scott en su blog en OdeToCode o en Twitter en [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) .
