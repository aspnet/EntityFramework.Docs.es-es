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
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="b7347-103">Capacidad de prueba y Entity Framework 4,0</span><span class="sxs-lookup"><span data-stu-id="b7347-103">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="b7347-104">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="b7347-104">Scott Allen</span></span>

<span data-ttu-id="b7347-105">Publicación: mayo de 2010</span><span class="sxs-lookup"><span data-stu-id="b7347-105">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="b7347-106">Introducción</span><span class="sxs-lookup"><span data-stu-id="b7347-106">Introduction</span></span>

<span data-ttu-id="b7347-107">En estas notas del producto se describe y se muestra cómo escribir código que se pueda probar con ADO.NET Entity Framework 4,0 y Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="b7347-107">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="b7347-108">Este documento no intenta centrarse en una metodología de prueba específica, como el diseño basado en pruebas (TDD) o el diseño controlado por comportamientos (BDD).</span><span class="sxs-lookup"><span data-stu-id="b7347-108">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="b7347-109">En su lugar, este documento se centrará en cómo escribir código que use el Entity Framework de ADO.NET, pero sigue siendo fácil aislar y probar de forma automatizada.</span><span class="sxs-lookup"><span data-stu-id="b7347-109">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="b7347-110">Veremos patrones de diseño comunes que facilitan las pruebas en escenarios de acceso a datos y ven cómo aplicar esos patrones al usar el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-110">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="b7347-111">También veremos características específicas del marco de trabajo para ver cómo estas características pueden funcionar en código comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-111">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="b7347-112">¿Qué es el código que se pueda probar?</span><span class="sxs-lookup"><span data-stu-id="b7347-112">What Is Testable Code?</span></span>

<span data-ttu-id="b7347-113">La capacidad de comprobar una parte del software mediante pruebas unitarias automatizadas ofrece muchas ventajas deseadas.</span><span class="sxs-lookup"><span data-stu-id="b7347-113">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="b7347-114">Todo el mundo sabe que las buenas pruebas reducirán el número de defectos de software de una aplicación y aumentarán la calidad de la aplicación, pero si tiene pruebas unitarias en su lugar, más allá de buscar errores.</span><span class="sxs-lookup"><span data-stu-id="b7347-114">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="b7347-115">Un buen conjunto de pruebas unitarias permite que un equipo de desarrollo Ahorre tiempo y mantenga el control del software que crean.</span><span class="sxs-lookup"><span data-stu-id="b7347-115">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="b7347-116">Un equipo puede realizar cambios en el código existente, refactorizar, rediseñar y reestructurar el software para cumplir los requisitos nuevos y agregar nuevos componentes a una aplicación, al tiempo que sabe que el conjunto de pruebas puede comprobar el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7347-116">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="b7347-117">Las pruebas unitarias forman parte de un ciclo de comentarios rápido para facilitar el cambio y preservar el mantenimiento del software a medida que aumenta la complejidad.</span><span class="sxs-lookup"><span data-stu-id="b7347-117">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="b7347-118">Sin embargo, las pruebas unitarias incluyen un precio.</span><span class="sxs-lookup"><span data-stu-id="b7347-118">Unit testing comes with a price, however.</span></span> <span data-ttu-id="b7347-119">Un equipo tiene que invertir el tiempo de creación y mantenimiento de las pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="b7347-119">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="b7347-120">La cantidad de esfuerzo necesaria para crear estas pruebas está directamente relacionada con la capacidad de **prueba** del software subyacente.</span><span class="sxs-lookup"><span data-stu-id="b7347-120">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="b7347-121">¿Es fácil probar el software?</span><span class="sxs-lookup"><span data-stu-id="b7347-121">How easy is the software to test?</span></span> <span data-ttu-id="b7347-122">Un equipo que diseñe software con capacidad de prueba en mente creará pruebas eficaces más rápido que el equipo que trabaja con un software no comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-122">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="b7347-123">Microsoft diseñó el ADO.NET Entity Framework 4,0 (EF4), teniendo en cuenta la capacidad de prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-123">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="b7347-124">Esto no significa que los desarrolladores vayan a escribir pruebas unitarias en el propio código del marco.</span><span class="sxs-lookup"><span data-stu-id="b7347-124">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="b7347-125">En su lugar, los objetivos de capacidad de prueba de EF4 facilitan la creación de código comprobable que se basa en el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-125">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="b7347-126">Antes de ver ejemplos específicos, merece la pena comprender las cualidades del código comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-126">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="b7347-127">Las cualidades del código comprobable</span><span class="sxs-lookup"><span data-stu-id="b7347-127">The Qualities of Testable Code</span></span>

<span data-ttu-id="b7347-128">El código que es fácil de probar siempre presentará al menos dos rasgos.</span><span class="sxs-lookup"><span data-stu-id="b7347-128">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="b7347-129">En primer lugar, el código comprobable se **observa**fácilmente.</span><span class="sxs-lookup"><span data-stu-id="b7347-129">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="b7347-130">Dado un conjunto de entradas, debe ser fácil observar la salida del código.</span><span class="sxs-lookup"><span data-stu-id="b7347-130">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="b7347-131">Por ejemplo, es fácil probar el método siguiente porque el método devuelve directamente el resultado de un cálculo.</span><span class="sxs-lookup"><span data-stu-id="b7347-131">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="b7347-132">Probar un método es difícil si el método escribe el valor calculado en un socket de red, una tabla de base de datos o un archivo como el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-132">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="b7347-133">La prueba tiene que realizar trabajo adicional para recuperar el valor.</span><span class="sxs-lookup"><span data-stu-id="b7347-133">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="b7347-134">En segundo lugar, el código comprobable es fácil de **aislar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-134">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="b7347-135">Vamos a usar el siguiente pseudocódigo como ejemplo incorrecto de código comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-135">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="b7347-136">El método es fácil de observar: podemos pasar una directiva de seguros y comprobar que el valor devuelto coincide con un resultado esperado.</span><span class="sxs-lookup"><span data-stu-id="b7347-136">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="b7347-137">Sin embargo, para probar el método, es necesario tener una base de datos de instalada con el esquema correcto y configurar el servidor SMTP en caso de que el método intente enviar un correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="b7347-137">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="b7347-138">La prueba unitaria solo desea comprobar la lógica de cálculo dentro del método, pero puede producirse un error en la prueba porque el servidor de correo electrónico está sin conexión o porque se ha desconectado el servidor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-138">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="b7347-139">Ambos errores no están relacionados con el comportamiento que la prueba desea comprobar.</span><span class="sxs-lookup"><span data-stu-id="b7347-139">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="b7347-140">Es difícil aislar el comportamiento.</span><span class="sxs-lookup"><span data-stu-id="b7347-140">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="b7347-141">Los desarrolladores de software que se esfuerzan por escribir código comprobable a menudo esfuerzan por mantener una separación de preocupaciones en el código que escriben.</span><span class="sxs-lookup"><span data-stu-id="b7347-141">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="b7347-142">El método anterior debe centrarse en los cálculos empresariales y delegar la base de datos y los detalles de implementación de correo electrónico a otros componentes.</span><span class="sxs-lookup"><span data-stu-id="b7347-142">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="b7347-143">Robert C. Martin llama a este principio de responsabilidad única.</span><span class="sxs-lookup"><span data-stu-id="b7347-143">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="b7347-144">Un objeto debe encapsular una única responsabilidad estrecha, como calcular el valor de una directiva.</span><span class="sxs-lookup"><span data-stu-id="b7347-144">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="b7347-145">El resto de la base de datos y el trabajo de notificación deben ser responsabilidad de algún otro objeto.</span><span class="sxs-lookup"><span data-stu-id="b7347-145">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="b7347-146">El código escrito de este modo es más fácil de aislar porque se centra en una sola tarea.</span><span class="sxs-lookup"><span data-stu-id="b7347-146">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="b7347-147">En .NET tenemos las abstracciones que necesitamos seguir el principio de responsabilidad única y conseguir el aislamiento.</span><span class="sxs-lookup"><span data-stu-id="b7347-147">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="b7347-148">Podemos usar las definiciones de interfaz y forzar que el código use la abstracción de la interfaz en lugar de un tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="b7347-148">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="b7347-149">Más adelante en este documento veremos cómo un método como el ejemplo incorrecto presentado anteriormente puede funcionar con interfaces que *parecen* que se comunicarán con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-149">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="b7347-150">Sin embargo, en el momento de la prueba, podemos sustituir una implementación ficticia que no se comunica con la base de datos, sino que almacena los datos en la memoria.</span><span class="sxs-lookup"><span data-stu-id="b7347-150">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="b7347-151">Esta implementación ficticia aislará el código de problemas no relacionados en el código de acceso a datos o la configuración de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-151">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="b7347-152">Existen ventajas adicionales para el aislamiento.</span><span class="sxs-lookup"><span data-stu-id="b7347-152">There are additional benefits to isolation.</span></span> <span data-ttu-id="b7347-153">El cálculo empresarial en el último método solo debe tardar unos milisegundos en ejecutarse, pero la propia prueba podría ejecutarse durante varios segundos a medida que el código se salto alrededor de la red y se comunique con varios servidores.</span><span class="sxs-lookup"><span data-stu-id="b7347-153">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="b7347-154">Las pruebas unitarias se deben ejecutar rápidamente para facilitar pequeños cambios.</span><span class="sxs-lookup"><span data-stu-id="b7347-154">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="b7347-155">Las pruebas unitarias también se deben repetir y no generar un error porque un componente no relacionado con la prueba tiene un problema.</span><span class="sxs-lookup"><span data-stu-id="b7347-155">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="b7347-156">Escribir código que sea fácil de observar y aislar significa que los desarrolladores tendrán un tiempo más sencillo escribiendo las pruebas para el código, dedique menos tiempo a esperar a que se ejecuten las pruebas y, lo que es más importante, dedique menos tiempo a realizar un seguimiento de los errores que no existen.</span><span class="sxs-lookup"><span data-stu-id="b7347-156">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="b7347-157">Espero que pueda apreciar las ventajas de las pruebas y comprender las cualidades que exhibe el código.</span><span class="sxs-lookup"><span data-stu-id="b7347-157">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="b7347-158">Estamos a punto de tratar cómo escribir código que funcione con EF4 para guardar datos en una base de datos, mientras que el resto es visible y fácil aislar, pero en primer lugar limitaremos nuestro enfoque para analizar los diseños que se pueden probar para el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-158">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="b7347-159">Modelos de diseño para la persistencia de datos</span><span class="sxs-lookup"><span data-stu-id="b7347-159">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="b7347-160">Los dos ejemplos no válidos que se presentaron anteriormente tenían demasiadas responsabilidades.</span><span class="sxs-lookup"><span data-stu-id="b7347-160">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="b7347-161">El primer ejemplo incorrecto tenía que realizar un cálculo *y* escribir en un archivo.</span><span class="sxs-lookup"><span data-stu-id="b7347-161">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="b7347-162">El segundo ejemplo incorrecto tenía que leer los datos de una base de datos *y* realizar un cálculo empresarial *y* enviar un correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="b7347-162">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="b7347-163">Al diseñar métodos más pequeños que separan los problemas y delegar la responsabilidad en otros componentes, hará grandes progresos en la escritura de código comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-163">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="b7347-164">El objetivo es crear la funcionalidad mediante la composición de acciones a partir de abstracciones pequeñas y centradas.</span><span class="sxs-lookup"><span data-stu-id="b7347-164">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="b7347-165">En cuanto a la persistencia de los datos, las abstracciones pequeñas y centradas que se buscan son tan comunes que se han documentado como modelos de diseño.</span><span class="sxs-lookup"><span data-stu-id="b7347-165">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="b7347-166">Los patrones de libros de Martin Fowler de la arquitectura de aplicaciones empresariales fueron el primer trabajo para describir estos patrones en la impresión.</span><span class="sxs-lookup"><span data-stu-id="b7347-166">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="b7347-167">Proporcionaremos una breve descripción de estos patrones en las secciones siguientes antes de mostrar cómo estos ADO.NET Entity Framework implementan y funcionan con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="b7347-167">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="b7347-168">The Repository Pattern (El modelo de repositorio)</span><span class="sxs-lookup"><span data-stu-id="b7347-168">The Repository Pattern</span></span>

<span data-ttu-id="b7347-169">Fowler indica un repositorio "media entre las capas de asignación de datos y dominio mediante una interfaz similar a la colección para tener acceso a los objetos de dominio".</span><span class="sxs-lookup"><span data-stu-id="b7347-169">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="b7347-170">El objetivo del patrón de repositorio es aislar el código del Minutiae de acceso a datos y, como vimos, el aislamiento anterior es un rasgo necesario para la prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-170">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="b7347-171">La clave del aislamiento es cómo expone los objetos el repositorio mediante una interfaz similar a la de una colección.</span><span class="sxs-lookup"><span data-stu-id="b7347-171">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="b7347-172">La lógica que escriba para usar el repositorio no tiene ninguna idea de cómo el repositorio materializará los objetos que solicite.</span><span class="sxs-lookup"><span data-stu-id="b7347-172">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="b7347-173">El repositorio puede comunicarse con una base de datos o simplemente devolver objetos de una colección en memoria.</span><span class="sxs-lookup"><span data-stu-id="b7347-173">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="b7347-174">Todo el código debe saber que el repositorio parece mantener la colección, y puede recuperar, agregar y eliminar objetos de la colección.</span><span class="sxs-lookup"><span data-stu-id="b7347-174">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="b7347-175">En las aplicaciones .NET existentes, un repositorio concreto a menudo hereda de una interfaz genérica como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7347-175">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="b7347-176">Realizaremos algunos cambios en la definición de interfaz cuando se proporciona una implementación para EF4, pero el concepto básico sigue siendo el mismo.</span><span class="sxs-lookup"><span data-stu-id="b7347-176">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="b7347-177">El código puede usar un repositorio concreto que implemente esta interfaz para recuperar una entidad por su valor de clave principal, para recuperar una colección de entidades en función de la evaluación de un predicado, o simplemente para recuperar todas las entidades disponibles.</span><span class="sxs-lookup"><span data-stu-id="b7347-177">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="b7347-178">El código también puede Agregar y quitar entidades a través de la interfaz del repositorio.</span><span class="sxs-lookup"><span data-stu-id="b7347-178">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="b7347-179">Dado un IRepository de objetos de empleado, el código puede realizar las siguientes operaciones.</span><span class="sxs-lookup"><span data-stu-id="b7347-179">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="b7347-180">Dado que el código está usando una interfaz (IRepository de Employee), podemos proporcionar el código con diferentes implementaciones de la interfaz.</span><span class="sxs-lookup"><span data-stu-id="b7347-180">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="b7347-181">Una implementación puede ser una implementación respaldada por EF4 y almacenar objetos en una base de datos Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b7347-181">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="b7347-182">Una implementación diferente (una que usamos durante las pruebas) puede estar respaldada por una lista en memoria de objetos de empleado.</span><span class="sxs-lookup"><span data-stu-id="b7347-182">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="b7347-183">La interfaz le ayudará a lograr el aislamiento en el código.</span><span class="sxs-lookup"><span data-stu-id="b7347-183">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="b7347-184">Observe que la &lt; interfaz IRepository T &gt; no expone una operación de guardar.</span><span class="sxs-lookup"><span data-stu-id="b7347-184">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="b7347-185">¿Cómo se actualizan los objetos existentes?</span><span class="sxs-lookup"><span data-stu-id="b7347-185">How do we update existing objects?</span></span> <span data-ttu-id="b7347-186">Puede que se encuentre entre las definiciones de IRepository que incluyen la operación de guardar, y las implementaciones de estos repositorios deberán conservar de forma inmediata un objeto en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-186">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="b7347-187">Sin embargo, en muchas aplicaciones no queremos conservar los objetos individualmente.</span><span class="sxs-lookup"><span data-stu-id="b7347-187">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="b7347-188">En su lugar, queremos traer objetos a la vida, quizás desde diferentes repositorios, modificar esos objetos como parte de una actividad económica y, a continuación, conservar todos los objetos como parte de una única operación atómica.</span><span class="sxs-lookup"><span data-stu-id="b7347-188">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="b7347-189">Afortunadamente, hay un patrón que permite este tipo de comportamiento.</span><span class="sxs-lookup"><span data-stu-id="b7347-189">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="b7347-190">Patrón de unidad de trabajo</span><span class="sxs-lookup"><span data-stu-id="b7347-190">The Unit of Work Pattern</span></span>

<span data-ttu-id="b7347-191">Fowler indica que una unidad de trabajo "mantendrá una lista de objetos afectados por una transacción empresarial y coordina la escritura de los cambios y la resolución de los problemas de simultaneidad".</span><span class="sxs-lookup"><span data-stu-id="b7347-191">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="b7347-192">Es responsabilidad de la unidad de trabajo realizar un seguimiento de los cambios en los objetos que aportamos a la vida desde un repositorio y que conservan los cambios realizados en los objetos cuando se indica a la unidad de trabajo que confirme los cambios.</span><span class="sxs-lookup"><span data-stu-id="b7347-192">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="b7347-193">También es responsabilidad de la unidad de trabajo realizar los nuevos objetos que hemos agregado a todos los repositorios e insertar los objetos en una base de datos, así como administrar la eliminación.</span><span class="sxs-lookup"><span data-stu-id="b7347-193">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="b7347-194">Si alguna vez ha realizado algún trabajo con conjuntos de valores de ADO.NET, ya estará familiarizado con el patrón de unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-194">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="b7347-195">Los conjuntos de datos de ADO.NET tenían la capacidad de realizar un seguimiento de las actualizaciones, eliminaciones e inserción de objetos DataRow y podrían (con la ayuda de un TableAdapter) conciliar todos los cambios en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-195">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="b7347-196">Sin embargo, los objetos DataSet modelan un subconjunto desconectado de la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="b7347-196">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="b7347-197">El patrón de unidad de trabajo exhibe el mismo comportamiento, pero funciona con objetos de negocio y objetos de dominio que están aislados del código de acceso a datos y sin tener en cuenta la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-197">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="b7347-198">Una abstracción para modelar la unidad de trabajo en código .NET podría ser similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7347-198">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="b7347-199">Al exponer las referencias del repositorio a partir de la unidad de trabajo, se puede asegurar de que un solo objeto de unidad de trabajo tiene la capacidad de realizar un seguimiento de todas las entidades materializadas durante una transacción empresarial.</span><span class="sxs-lookup"><span data-stu-id="b7347-199">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="b7347-200">La implementación del método commit para una unidad de trabajo real es donde se produce toda la instrucción mágica para conciliar los cambios en memoria con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-200">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="b7347-201">Dada una referencia IUnitOfWork, el código puede realizar cambios en los objetos comerciales recuperados de uno o varios repositorios y guardar todos los cambios mediante la operación de confirmación atómica.</span><span class="sxs-lookup"><span data-stu-id="b7347-201">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="b7347-202">El modelo de carga diferida</span><span class="sxs-lookup"><span data-stu-id="b7347-202">The Lazy Load Pattern</span></span>

<span data-ttu-id="b7347-203">Fowler usa el nombre Lazy LOAD para describir "un objeto que no contiene todos los datos que necesita, pero sabe cómo obtenerlo".</span><span class="sxs-lookup"><span data-stu-id="b7347-203">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="b7347-204">La carga diferida transparente es una característica importante que se debe tener al escribir código empresarial comprobable y al trabajar con una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="b7347-204">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="b7347-205">Como ejemplo, considere el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="b7347-205">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="b7347-206">¿Cómo se rellena la colección de tarjetas de horas?</span><span class="sxs-lookup"><span data-stu-id="b7347-206">How is the TimeCards collection populated?</span></span> <span data-ttu-id="b7347-207">Hay dos posibles respuestas.</span><span class="sxs-lookup"><span data-stu-id="b7347-207">There are two possible answers.</span></span> <span data-ttu-id="b7347-208">Una respuesta es que el repositorio del empleado, cuando se le pide que capture un empleado, emite una consulta para recuperar el empleado junto con la información de la tarjeta de tiempo asociada al empleado.</span><span class="sxs-lookup"><span data-stu-id="b7347-208">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="b7347-209">En las bases de datos relacionales, esto normalmente requiere una consulta con una cláusula JOIN y puede dar lugar a la recuperación de más información de la que necesita una aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7347-209">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="b7347-210">¿Qué ocurre si la aplicación no necesita tocar la propiedad de las tarjetas de información.</span><span class="sxs-lookup"><span data-stu-id="b7347-210">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="b7347-211">Una segunda respuesta es cargar la propiedad "a petición" de las tarjetas de horas.</span><span class="sxs-lookup"><span data-stu-id="b7347-211">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="b7347-212">Esta carga diferida es implícita y transparente para la lógica de negocios, ya que el código no invoca API especiales para recuperar la información de la tarjeta de tiempo.</span><span class="sxs-lookup"><span data-stu-id="b7347-212">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="b7347-213">El código asume que la información de la tarjeta de tiempo está presente cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b7347-213">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="b7347-214">Hay una especial participación en la carga diferida que generalmente implica la interceptación en tiempo de ejecución de las invocaciones de método.</span><span class="sxs-lookup"><span data-stu-id="b7347-214">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="b7347-215">El código de interceptación es responsable de comunicarse con la base de datos y recuperar la información de la tarjeta de tiempo, a la vez que la lógica de negocios queda libre para ser lógica empresarial.</span><span class="sxs-lookup"><span data-stu-id="b7347-215">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="b7347-216">Esta magia de carga diferida permite al código de negocio aislarse de las operaciones de recuperación de datos y da como resultado un código más comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-216">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="b7347-217">El inconveniente de una carga diferida es que cuando *una aplicación necesita* la información de la tarjeta de tiempo, el código ejecutará una consulta adicional.</span><span class="sxs-lookup"><span data-stu-id="b7347-217">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="b7347-218">Esto no supone un problema para muchas aplicaciones, pero para las aplicaciones o aplicaciones sensibles al rendimiento se repiten por un número de objetos de empleado y la ejecución de una consulta para recuperar las tarjetas de tiempo durante cada iteración del bucle (un problema a menudo conocido como el problema de consulta N + 1), la carga diferida es un arrastre.</span><span class="sxs-lookup"><span data-stu-id="b7347-218">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="b7347-219">En estos escenarios, es posible que una aplicación quiera cargar la información de la tarjeta de tiempo de la manera más eficaz posible.</span><span class="sxs-lookup"><span data-stu-id="b7347-219">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="b7347-220">Afortunadamente, veremos cómo EF4 admite las cargas diferidas implícitas y las cargas diligentes eficaces a medida que avanzamos en la siguiente sección e implementamos estos patrones.</span><span class="sxs-lookup"><span data-stu-id="b7347-220">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="b7347-221">Implementar patrones con el Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b7347-221">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="b7347-222">La buena noticia es que todos los patrones de diseño que se describen en la última sección son sencillos de implementar con EF4.</span><span class="sxs-lookup"><span data-stu-id="b7347-222">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="b7347-223">Para demostrar que vamos a usar una sencilla aplicación ASP.NET MVC para editar y mostrar los empleados y la información de su tarjeta de tiempo asociada.</span><span class="sxs-lookup"><span data-stu-id="b7347-223">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="b7347-224">Comenzaremos usando los siguientes "objetos CLR antiguos sin formato" (POCO).</span><span class="sxs-lookup"><span data-stu-id="b7347-224">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="b7347-225">Estas definiciones de clase cambiarán ligeramente a medida que exploramos diferentes enfoques y características de EF4, pero el objetivo es mantener estas clases como la persistencia ignorada (PI) como sea posible.</span><span class="sxs-lookup"><span data-stu-id="b7347-225">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="b7347-226">Un objeto PI no sabe *Cómo*, o incluso *si*, el estado que contiene reside en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-226">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="b7347-227">PI y POCO go están a mano con el software que se pueda probar.</span><span class="sxs-lookup"><span data-stu-id="b7347-227">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="b7347-228">Los objetos que usan un enfoque POCO son menos restrictivos, más flexibles y fáciles de probar porque pueden funcionar sin una base de datos presente.</span><span class="sxs-lookup"><span data-stu-id="b7347-228">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="b7347-229">Con los POCO en vigor, podemos crear un Entity Data Model (EDM) en Visual Studio (vea la ilustración 1).</span><span class="sxs-lookup"><span data-stu-id="b7347-229">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="b7347-230">No usaremos el EDM para generar código para nuestras entidades.</span><span class="sxs-lookup"><span data-stu-id="b7347-230">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="b7347-231">En su lugar, queremos usar las entidades que lovinglymos de forma manual.</span><span class="sxs-lookup"><span data-stu-id="b7347-231">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="b7347-232">Solo usaremos el EDM para generar el esquema de la base de datos y proporcionar los metadatos que EF4 necesita para asignar objetos a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-232">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![test_01 EF](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="b7347-234">**Ilustración 1**</span><span class="sxs-lookup"><span data-stu-id="b7347-234">**Figure 1**</span></span>

<span data-ttu-id="b7347-235">Nota: Si desea desarrollar el modelo EDM en primer lugar, es posible generar código limpio y POCO a partir del EDM.</span><span class="sxs-lookup"><span data-stu-id="b7347-235">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="b7347-236">Puede hacerlo con una extensión de Visual Studio 2010 proporcionada por el equipo de programación de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-236">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="b7347-237">Para descargar la extensión, inicie el administrador de extensiones desde el menú herramientas de Visual Studio y busque "POCO" en la galería en línea de plantillas (consulte la figura 2).</span><span class="sxs-lookup"><span data-stu-id="b7347-237">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="b7347-238">Hay varias plantillas POCO disponibles para EF.</span><span class="sxs-lookup"><span data-stu-id="b7347-238">There are several POCO templates available for EF.</span></span> <span data-ttu-id="b7347-239">Para obtener más información sobre el uso de la plantilla, vea " [Tutorial: poco plantilla para el Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".</span><span class="sxs-lookup"><span data-stu-id="b7347-239">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![test_02 EF](~/ef6/media/eftest-02.png)

<span data-ttu-id="b7347-241">**Ilustración 2**</span><span class="sxs-lookup"><span data-stu-id="b7347-241">**Figure 2**</span></span>

<span data-ttu-id="b7347-242">Desde este punto de partida POCO, exploraremos dos enfoques diferentes para el código comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-242">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="b7347-243">El primer enfoque que se llama al enfoque EF porque aprovecha las abstracciones de la API de Entity Framework para implementar unidades de trabajo y repositorios.</span><span class="sxs-lookup"><span data-stu-id="b7347-243">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="b7347-244">En el segundo enfoque, crearemos nuestras propias abstracciones de repositorio personalizadas y, a continuación, veremos las ventajas y desventajas de cada enfoque.</span><span class="sxs-lookup"><span data-stu-id="b7347-244">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="b7347-245">Comenzaremos explorando el enfoque de EF.</span><span class="sxs-lookup"><span data-stu-id="b7347-245">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="b7347-246">Una implementación centrada en EF</span><span class="sxs-lookup"><span data-stu-id="b7347-246">An EF Centric Implementation</span></span>

<span data-ttu-id="b7347-247">Considere la siguiente acción del controlador de un proyecto de MVC de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="b7347-247">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="b7347-248">La acción recupera un objeto de empleado y devuelve un resultado para mostrar una vista detallada del empleado.</span><span class="sxs-lookup"><span data-stu-id="b7347-248">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="b7347-249">¿Se va a probar el código?</span><span class="sxs-lookup"><span data-stu-id="b7347-249">Is the code testable?</span></span> <span data-ttu-id="b7347-250">Hay al menos dos pruebas que necesitamos para comprobar el comportamiento de la acción.</span><span class="sxs-lookup"><span data-stu-id="b7347-250">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="b7347-251">En primer lugar, nos gustaría comprobar que la acción devuelve la vista correcta (una prueba sencilla).</span><span class="sxs-lookup"><span data-stu-id="b7347-251">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="b7347-252">También deseamos escribir una prueba para comprobar que la acción recupera el empleado correcto y nos gustaría hacerlo sin ejecutar código para consultar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-252">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="b7347-253">Recuerde que queremos aislar el código sometido a prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-253">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="b7347-254">El aislamiento garantizará que la prueba no produzca un error debido a un error en el código de acceso a datos o la configuración de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-254">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="b7347-255">Si se produce un error en la prueba, se sabrá que tenemos un error en la lógica del controlador y no en un componente del sistema de nivel inferior.</span><span class="sxs-lookup"><span data-stu-id="b7347-255">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="b7347-256">Para lograr el aislamiento, necesitamos algunas abstracciones como las interfaces que presentamos anteriormente para los repositorios y las unidades de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-256">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="b7347-257">Recuerde que el patrón de repositorio está diseñado para mediar entre objetos de dominio y la capa de asignación de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-257">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="b7347-258">En este escenario, EF4 *es* la capa de asignación de datos y ya proporciona una abstracción similar a la del repositorio denominada IObjectSet &lt; T &gt; (del espacio de nombres System. Data. Objects).</span><span class="sxs-lookup"><span data-stu-id="b7347-258">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="b7347-259">La definición de la interfaz es similar a la siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-259">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="b7347-260">IObjectSet &lt; T &gt; cumple los requisitos de un repositorio porque se parece a una colección de objetos (a través de IEnumerable &lt; T &gt; ) y proporciona métodos para agregar y quitar objetos de la colección simulada.</span><span class="sxs-lookup"><span data-stu-id="b7347-260">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="b7347-261">Los métodos Attach y detach exponen funcionalidades adicionales de la API de EF4.</span><span class="sxs-lookup"><span data-stu-id="b7347-261">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="b7347-262">Para usar IObjectSet &lt; T &gt; como interfaz para los repositorios, se necesita una abstracción de unidad de trabajo para enlazar repositorios juntos.</span><span class="sxs-lookup"><span data-stu-id="b7347-262">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="b7347-263">Una implementación concreta de esta interfaz se comunicará con SQL Server y es fácil de crear mediante la clase ObjectContext desde EF4.</span><span class="sxs-lookup"><span data-stu-id="b7347-263">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="b7347-264">La clase ObjectContext es la unidad real de trabajo de la API de EF4.</span><span class="sxs-lookup"><span data-stu-id="b7347-264">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="b7347-265">Llevar un IObjectSet &lt; T &gt; a la vida es tan sencillo como invocar el método método createobjectset del objeto ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="b7347-265">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="b7347-266">En segundo plano, el marco de trabajo usará los metadatos proporcionados en el EDM para producir un ObjectSet &lt; T concreto &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-266">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="b7347-267">Nos centraremos en la devolución de &lt; la &gt; interfaz IObjectSet T porque le ayudará a mantener la capacidad de prueba en el código de cliente.</span><span class="sxs-lookup"><span data-stu-id="b7347-267">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="b7347-268">Esta implementación concreta es útil en producción, pero es necesario centrarnos en cómo usaremos nuestra abstracción IUnitOfWork para facilitar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7347-268">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="b7347-269">La prueba se duplica</span><span class="sxs-lookup"><span data-stu-id="b7347-269">The Test Doubles</span></span>

<span data-ttu-id="b7347-270">Para aislar la acción del controlador, se necesita la capacidad de cambiar entre la unidad de trabajo real (respaldada por un ObjectContext) y una unidad de trabajo de prueba doble o "falsa" (realizando operaciones en memoria).</span><span class="sxs-lookup"><span data-stu-id="b7347-270">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="b7347-271">El método común para realizar este tipo de conmutación es no permitir que el controlador de MVC cree una instancia de una unidad de trabajo, sino que en su lugar pasa la unidad de trabajo al controlador como un parámetro de constructor.</span><span class="sxs-lookup"><span data-stu-id="b7347-271">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="b7347-272">El código anterior es un ejemplo de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b7347-272">The above code is an example of dependency injection.</span></span> <span data-ttu-id="b7347-273">No permitimos que el controlador cree su dependencia (la unidad de trabajo), sino que inserte la dependencia en el controlador.</span><span class="sxs-lookup"><span data-stu-id="b7347-273">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="b7347-274">En un proyecto de MVC, es habitual usar un generador de controlador personalizado en combinación con un contenedor de inversión de control (IoC) para automatizar la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b7347-274">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="b7347-275">Estos temas están fuera del ámbito de este artículo, pero puede leer más mediante las referencias que se indican al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="b7347-275">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="b7347-276">Una implementación de unidad de trabajo falsa que se puede usar para las pruebas podría ser similar a la siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-276">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="b7347-277">Observe que la unidad de trabajo falsa expone una propiedad confirmada.</span><span class="sxs-lookup"><span data-stu-id="b7347-277">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="b7347-278">A veces resulta útil agregar características a una clase falsa que facilitan las pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7347-278">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="b7347-279">En este caso, es fácil observar si el código confirma una unidad de trabajo mediante la comprobación de la propiedad confirmada.</span><span class="sxs-lookup"><span data-stu-id="b7347-279">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="b7347-280">También se necesitará una IObjectSet falsa &lt; &gt; para almacenar los objetos de los empleados y las tarjetas de la tarjeta de la memoria.</span><span class="sxs-lookup"><span data-stu-id="b7347-280">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="b7347-281">Se puede proporcionar una implementación única mediante genéricos.</span><span class="sxs-lookup"><span data-stu-id="b7347-281">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="b7347-282">Esta prueba Double delega la mayor parte de su trabajo en un &lt; objeto HashSet T subyacente &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-282">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="b7347-283">Tenga en cuenta que IObjectSet &lt; T &gt; requiere una restricción genérica que aplique t como clase (un tipo de referencia) y también nos obliga a implementar IQueryable &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-283">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="b7347-284">Es fácil hacer que una colección en memoria aparezca como IQueryable &lt; T &gt; mediante el operador estándar LINQ que se pueda consultar.</span><span class="sxs-lookup"><span data-stu-id="b7347-284">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="b7347-285">Las pruebas</span><span class="sxs-lookup"><span data-stu-id="b7347-285">The Tests</span></span>

<span data-ttu-id="b7347-286">Las pruebas unitarias tradicionales usarán una sola clase de prueba para contener todas las pruebas de todas las acciones en un solo controlador de MVC.</span><span class="sxs-lookup"><span data-stu-id="b7347-286">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="b7347-287">Podemos escribir estas pruebas, o cualquier tipo de prueba unitaria, mediante el uso de las falsificaciones de memoria que hemos creado.</span><span class="sxs-lookup"><span data-stu-id="b7347-287">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="b7347-288">Sin embargo, en este artículo se evitará el enfoque de la clase de prueba monolítica y, en su lugar, se agruparán las pruebas para centrarse en una parte específica de la funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="b7347-288">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="b7347-289">Por ejemplo, "crear nuevo empleado" podría ser la funcionalidad que queremos probar, por lo que usaremos una sola clase de prueba para comprobar la acción de controlador único responsable de crear un nuevo empleado.</span><span class="sxs-lookup"><span data-stu-id="b7347-289">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="b7347-290">Hay algún código de instalación común que necesitamos para todas estas clases de prueba concretas.</span><span class="sxs-lookup"><span data-stu-id="b7347-290">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="b7347-291">Por ejemplo, siempre tenemos que crear los repositorios en memoria y la unidad de trabajo falsa.</span><span class="sxs-lookup"><span data-stu-id="b7347-291">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="b7347-292">También se necesita una instancia del controlador de empleados con la unidad falsa de trabajo insertada.</span><span class="sxs-lookup"><span data-stu-id="b7347-292">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="b7347-293">Se compartirá este código de instalación común en todas las clases de prueba mediante una clase base.</span><span class="sxs-lookup"><span data-stu-id="b7347-293">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="b7347-294">El "objeto madre" que usamos en la clase base es un patrón común para crear datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-294">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="b7347-295">Un objeto Mother contiene métodos de generador para crear instancias de entidades de prueba para su uso en varios extras de prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-295">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="b7347-296">Podemos usar EmployeeControllerTestBase como la clase base para una serie de accesorios de prueba (vea la figura 3).</span><span class="sxs-lookup"><span data-stu-id="b7347-296">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="b7347-297">Cada accesorio de prueba probará una acción de controlador específica.</span><span class="sxs-lookup"><span data-stu-id="b7347-297">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="b7347-298">Por ejemplo, un accesorio de prueba se centrará en probar la acción de creación que se usa durante una solicitud GET de HTTP (para mostrar la vista de creación de un empleado) y un accesorio diferente se centrará en la acción de creación usada en una solicitud HTTP POST (para que el usuario envíe la información para crear un empleado).</span><span class="sxs-lookup"><span data-stu-id="b7347-298">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="b7347-299">Cada clase derivada solo es responsable de la configuración necesaria en su contexto específico y de proporcionar las aserciones necesarias para comprobar los resultados de su contexto de prueba específico.</span><span class="sxs-lookup"><span data-stu-id="b7347-299">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![test_03 EF](~/ef6/media/eftest-03.png)

<span data-ttu-id="b7347-301">**Ilustración 3**</span><span class="sxs-lookup"><span data-stu-id="b7347-301">**Figure 3**</span></span>

<span data-ttu-id="b7347-302">La Convención de nomenclatura y el estilo de prueba presentados aquí no son necesarios para el código comprobable, sino solo un enfoque.</span><span class="sxs-lookup"><span data-stu-id="b7347-302">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="b7347-303">En la figura 4 se muestran las pruebas que se ejecutan en el complemento del Ejecutor de pruebas de jet cerebro ReSharper para Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="b7347-303">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![test_04 EF](~/ef6/media/eftest-04.png)

<span data-ttu-id="b7347-305">**Figura 4**</span><span class="sxs-lookup"><span data-stu-id="b7347-305">**Figure 4**</span></span>

<span data-ttu-id="b7347-306">Con una clase base para controlar el código compartido de la instalación, las pruebas unitarias para cada acción de controlador son pequeñas y fáciles de escribir.</span><span class="sxs-lookup"><span data-stu-id="b7347-306">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="b7347-307">Las pruebas se ejecutarán rápidamente (dado que se están realizando operaciones en memoria) y no se debería producir un error debido a la infraestructura no relacionada o a problemas ambientales (porque hemos aislado la unidad en pruebas).</span><span class="sxs-lookup"><span data-stu-id="b7347-307">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="b7347-308">En estas pruebas, la clase base realiza la mayor parte del trabajo de configuración.</span><span class="sxs-lookup"><span data-stu-id="b7347-308">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="b7347-309">Recuerde que el constructor de clase base crea el repositorio en memoria, una unidad de trabajo falsa y una instancia de la clase EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="b7347-309">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="b7347-310">La clase de prueba se deriva de esta clase base y se centra en los detalles de la prueba del método Create.</span><span class="sxs-lookup"><span data-stu-id="b7347-310">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="b7347-311">En este caso, las características específicas se reducen a los pasos "Arrange, Act y Assert" que verá en cualquier procedimiento de prueba unitaria:</span><span class="sxs-lookup"><span data-stu-id="b7347-311">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="b7347-312">Cree un objeto newEmployee para simular los datos entrantes.</span><span class="sxs-lookup"><span data-stu-id="b7347-312">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="b7347-313">Invocar la acción de creación de EmployeeController y pasar newEmployee.</span><span class="sxs-lookup"><span data-stu-id="b7347-313">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="b7347-314">Compruebe que la acción crear produce los resultados esperados (el empleado aparece en el repositorio).</span><span class="sxs-lookup"><span data-stu-id="b7347-314">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="b7347-315">Lo que hemos creado nos permite probar cualquiera de las acciones de EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="b7347-315">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="b7347-316">Por ejemplo, cuando se escriben pruebas para la acción de índice del controlador de empleado, se puede heredar de la clase base de prueba para establecer la misma configuración base para nuestras pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7347-316">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="b7347-317">De nuevo, la clase base creará el repositorio en memoria, la unidad de trabajo falsa y una instancia de EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="b7347-317">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="b7347-318">Las pruebas para la acción de índice solo deben centrarse en la invocación de la acción de índice y en la prueba de las cualidades del modelo que devuelve la acción.</span><span class="sxs-lookup"><span data-stu-id="b7347-318">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="b7347-319">Las pruebas que creamos con las falsificaciones en memoria están orientadas a probar el *Estado* del software.</span><span class="sxs-lookup"><span data-stu-id="b7347-319">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="b7347-320">Por ejemplo, al probar la acción de creación, queremos inspeccionar el estado del repositorio después de que se ejecute la acción de creación: ¿el repositorio mantiene el nuevo empleado?</span><span class="sxs-lookup"><span data-stu-id="b7347-320">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="b7347-321">Más adelante veremos las pruebas basadas en la interacción.</span><span class="sxs-lookup"><span data-stu-id="b7347-321">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="b7347-322">Las pruebas basadas en interacción le preguntarán si el código sometido a prueba invocó los métodos adecuados en nuestros objetos y pasa los parámetros correctos.</span><span class="sxs-lookup"><span data-stu-id="b7347-322">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="b7347-323">Por ahora, pasaremos a la portada otro patrón de diseño: la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="b7347-323">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="b7347-324">Carga diligente y carga diferida</span><span class="sxs-lookup"><span data-stu-id="b7347-324">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="b7347-325">En algún momento de la aplicación web MVC de ASP.NET podríamos querer Mostrar la información de un empleado e incluir las tarjetas de tiempo asociadas del empleado.</span><span class="sxs-lookup"><span data-stu-id="b7347-325">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="b7347-326">Por ejemplo, es posible que tengamos una pantalla de Resumen de tarjeta de tiempo que muestre el nombre del empleado y el número total de tarjetas de tiempo del sistema.</span><span class="sxs-lookup"><span data-stu-id="b7347-326">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="b7347-327">Existen varios enfoques que se pueden seguir para implementar esta característica.</span><span class="sxs-lookup"><span data-stu-id="b7347-327">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="b7347-328">Proyección</span><span class="sxs-lookup"><span data-stu-id="b7347-328">Projection</span></span>

<span data-ttu-id="b7347-329">Un enfoque sencillo para crear el resumen es construir un modelo dedicado a la información que queremos mostrar en la vista.</span><span class="sxs-lookup"><span data-stu-id="b7347-329">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="b7347-330">En este escenario, el modelo podría ser similar al siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-330">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="b7347-331">Tenga en cuenta que EmployeeSummaryViewModel no es una entidad; es decir, no es algo que queremos conservar en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-331">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="b7347-332">Solo vamos a usar esta clase para ordenar los datos en la vista de forma fuertemente tipada.</span><span class="sxs-lookup"><span data-stu-id="b7347-332">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="b7347-333">El modelo de vista es como un objeto de transferencia de datos (DTO) porque no contiene ningún comportamiento (sin métodos): solo propiedades.</span><span class="sxs-lookup"><span data-stu-id="b7347-333">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="b7347-334">Las propiedades contendrán los datos que necesitamos trasladar.</span><span class="sxs-lookup"><span data-stu-id="b7347-334">The properties will hold the data we need to move.</span></span> <span data-ttu-id="b7347-335">Es fácil crear instancias de este modelo de vista mediante el operador de proyección estándar de LINQ: el operador Select.</span><span class="sxs-lookup"><span data-stu-id="b7347-335">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="b7347-336">Hay dos características importantes para el código anterior.</span><span class="sxs-lookup"><span data-stu-id="b7347-336">There are two notable features to the above code.</span></span> <span data-ttu-id="b7347-337">En primer lugar, el código es fácil de probar porque todavía es fácil de observar y aislar.</span><span class="sxs-lookup"><span data-stu-id="b7347-337">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="b7347-338">El operador Select funciona igual que en las falsificaciones en memoria que en la unidad de trabajo real.</span><span class="sxs-lookup"><span data-stu-id="b7347-338">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="b7347-339">La segunda característica importante es la forma en que el código permite a EF4 generar una consulta única y eficaz para ensamblar la información de los empleados y la tarjeta de tiempo conjuntamente.</span><span class="sxs-lookup"><span data-stu-id="b7347-339">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="b7347-340">Hemos cargado información de empleados e información de la tarjeta de tiempo en el mismo objeto sin usar ninguna API especial.</span><span class="sxs-lookup"><span data-stu-id="b7347-340">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="b7347-341">El código simplemente expresó la información necesaria para usar operadores LINQ estándar que funcionan con orígenes de datos en memoria y con orígenes de datos remotos.</span><span class="sxs-lookup"><span data-stu-id="b7347-341">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="b7347-342">EF4 pudo traducir los árboles de expresión generados por la consulta LINQ y el \# compilador de C en una consulta T-SQL eficaz y única.</span><span class="sxs-lookup"><span data-stu-id="b7347-342">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="b7347-343">Hay otras ocasiones en las que no se desea trabajar con un modelo de vista o un objeto DTO, sino con entidades reales.</span><span class="sxs-lookup"><span data-stu-id="b7347-343">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="b7347-344">Cuando sabemos que necesitamos un empleado *y* las tarjetas de tiempo del empleado, podemos cargar diligentemente los datos relacionados de manera discreta y eficaz.</span><span class="sxs-lookup"><span data-stu-id="b7347-344">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="b7347-345">Carga diligente explícita</span><span class="sxs-lookup"><span data-stu-id="b7347-345">Explicit Eager Loading</span></span>

<span data-ttu-id="b7347-346">Cuando queremos cargar diligentemente la información relacionada de la entidad, necesitamos algún mecanismo para la lógica de negocios (o en este escenario, la lógica de acción del controlador) para expresar su deseo en el repositorio.</span><span class="sxs-lookup"><span data-stu-id="b7347-346">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="b7347-347">La clase EF4 ObjectQuery &lt; T &gt; define un método include para especificar los objetos relacionados que se van a recuperar durante una consulta.</span><span class="sxs-lookup"><span data-stu-id="b7347-347">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="b7347-348">Recuerde que EF4 ObjectContext expone entidades a través de la &lt; clase de ObjectSet t concreta, &gt; que hereda de ObjectQuery &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-348">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="b7347-349">Si usamos &lt; referencias de ObjectSet T &gt; en nuestra acción del controlador, podríamos escribir el código siguiente para especificar una carga diligente de información de la tarjeta de tiempo para cada empleado.</span><span class="sxs-lookup"><span data-stu-id="b7347-349">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="b7347-350">Sin embargo, puesto que estamos intentando mantener el código comprobable, no exponemos &lt; el ObjectSet T &gt; desde fuera de la clase real de la unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-350">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="b7347-351">En su lugar, confiamos en la &lt; interfaz IObjectSet T &gt; , que es más fácil de falsificar, pero IObjectSet &lt; T &gt; no define un método include.</span><span class="sxs-lookup"><span data-stu-id="b7347-351">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="b7347-352">La belleza de LINQ es que podemos crear nuestro propio operador include.</span><span class="sxs-lookup"><span data-stu-id="b7347-352">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="b7347-353">Observe que este operador include se define como un método de extensión para IQueryable &lt; t &gt; en lugar de IObjectSet &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-353">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="b7347-354">Esto nos da la posibilidad de usar el método con una gama más amplia de tipos posibles, incluidos IQueryable &lt; t &gt; , IObjectSet &lt; t &gt; , ObjectQuery &lt; t &gt; y ObjectSet &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-354">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="b7347-355">En el caso de que la secuencia subyacente no sea una copia de EF4 original de ObjectQuery &lt; T &gt; , no habrá ningún daño y el operador include es una operación no operativa.</span><span class="sxs-lookup"><span data-stu-id="b7347-355">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="b7347-356">Si la secuencia subyacente *es* un ObjectQuery &lt; t &gt; (o se deriva de OBJECTQUERY &lt; t &gt; ), EF4 verá nuestro requisito de datos adicionales y formulará la consulta SQL adecuada.</span><span class="sxs-lookup"><span data-stu-id="b7347-356">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="b7347-357">Con este nuevo operador en su lugar, podemos solicitar explícitamente una carga diligente de información de la tarjeta de tiempo del repositorio.</span><span class="sxs-lookup"><span data-stu-id="b7347-357">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="b7347-358">Cuando se ejecuta en un ObjectContext real, el código genera la siguiente consulta única.</span><span class="sxs-lookup"><span data-stu-id="b7347-358">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="b7347-359">La consulta recopila suficiente información de la base de datos en un recorrido para materializar los objetos de empleado y rellenar completamente su propiedad de tarjetas de seguridad.</span><span class="sxs-lookup"><span data-stu-id="b7347-359">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="b7347-360">La gran noticia es que el código dentro del método de acción sigue siendo totalmente comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-360">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="b7347-361">No es necesario proporcionar ninguna característica adicional para nuestras falsificaciones para admitir el operador include.</span><span class="sxs-lookup"><span data-stu-id="b7347-361">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="b7347-362">La mala noticia es que teníamos que usar el operador include en el código que deseamos para mantener la persistencia.</span><span class="sxs-lookup"><span data-stu-id="b7347-362">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="b7347-363">Este es un buen ejemplo del tipo de inconvenientes que debe evaluar al compilar código comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-363">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="b7347-364">Hay ocasiones en las que es necesario dejar que la persistencia tenga pérdidas fuera de la abstracción del repositorio para satisfacer los objetivos de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b7347-364">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="b7347-365">La alternativa a la carga diligente es la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="b7347-365">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="b7347-366">La carga diferida significa que *no* es necesario que nuestro código de negocio anuncie explícitamente el requisito de los datos asociados.</span><span class="sxs-lookup"><span data-stu-id="b7347-366">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="b7347-367">En su lugar, usamos nuestras entidades en la aplicación y si se necesitan datos adicionales Entity Framework cargarán los datos a petición.</span><span class="sxs-lookup"><span data-stu-id="b7347-367">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="b7347-368">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="b7347-368">Lazy Loading</span></span>

<span data-ttu-id="b7347-369">Es fácil imaginar un escenario en el que no se sepa qué datos necesitará una lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="b7347-369">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="b7347-370">Podríamos saber que la lógica necesita un objeto de empleado, pero podemos crear una rama en diferentes rutas de ejecución, donde algunas de esas rutas requieren información de tarjeta de tiempo del empleado y otras no.</span><span class="sxs-lookup"><span data-stu-id="b7347-370">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="b7347-371">Los escenarios como este son idóneos para la carga diferida implícita porque los datos aparecen de forma mágica según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b7347-371">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="b7347-372">La carga diferida, también conocida como carga aplazada, impone algunos requisitos en nuestros objetos entidad.</span><span class="sxs-lookup"><span data-stu-id="b7347-372">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="b7347-373">Un POCO con persistencia real omisión no tendría ningún requisito de la capa de persistencia, pero la persistencia real omisión es prácticamente imposible de lograr.</span><span class="sxs-lookup"><span data-stu-id="b7347-373">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="b7347-374">En su lugar, medimos la persistencia omisión en grados relativos.</span><span class="sxs-lookup"><span data-stu-id="b7347-374">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="b7347-375">Sería desafortunable si era necesario heredar de una clase base orientada a la persistencia o usar una colección especializada para lograr la carga diferida en POCO.</span><span class="sxs-lookup"><span data-stu-id="b7347-375">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="b7347-376">Afortunadamente, EF4 tiene una solución menos intrusiva.</span><span class="sxs-lookup"><span data-stu-id="b7347-376">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="b7347-377">Prácticamente no detectable</span><span class="sxs-lookup"><span data-stu-id="b7347-377">Virtually Undetectable</span></span>

<span data-ttu-id="b7347-378">Cuando se usan objetos POCO, EF4 puede generar dinámicamente servidores proxy en tiempo de ejecución para las entidades.</span><span class="sxs-lookup"><span data-stu-id="b7347-378">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="b7347-379">Estos proxies invisiblemente encapsulan los POCO materializados y proporcionan servicios adicionales interceptando cada operación get y set de cada propiedad para realizar trabajo adicional.</span><span class="sxs-lookup"><span data-stu-id="b7347-379">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="b7347-380">Uno de estos servicios es la característica de carga diferida que estamos buscando.</span><span class="sxs-lookup"><span data-stu-id="b7347-380">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="b7347-381">Otro servicio es un mecanismo de seguimiento de cambios eficaz que puede grabar cuando el programa cambia los valores de propiedad de una entidad.</span><span class="sxs-lookup"><span data-stu-id="b7347-381">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="b7347-382">El ObjectContext usa la lista de cambios durante el método SaveChanges para conservar las entidades modificadas mediante comandos UPDATE.</span><span class="sxs-lookup"><span data-stu-id="b7347-382">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="b7347-383">Sin embargo, para que estos proxies funcionen, necesitan una manera de enlazar las operaciones GET y set de la propiedad en una entidad, y los proxies logran este objetivo mediante la invalidación de los miembros virtuales.</span><span class="sxs-lookup"><span data-stu-id="b7347-383">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="b7347-384">Por lo tanto, si queremos tener una carga diferida implícita y un seguimiento de cambios eficaz, es necesario volver a las definiciones de clase POCO y marcar las propiedades como virtuales.</span><span class="sxs-lookup"><span data-stu-id="b7347-384">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="b7347-385">Todavía podemos decir que la entidad Employee es la que ignora la persistencia.</span><span class="sxs-lookup"><span data-stu-id="b7347-385">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="b7347-386">El único requisito es usar miembros virtuales y esto no afecta a la capacidad de prueba del código.</span><span class="sxs-lookup"><span data-stu-id="b7347-386">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="b7347-387">No es necesario derivar de ninguna clase base especial ni siquiera usar una colección especial dedicada a la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="b7347-387">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="b7347-388">Como se muestra en el código, cualquier clase que implemente ICollection &lt; T &gt; está disponible para contener entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b7347-388">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="b7347-389">También hay un pequeño cambio que necesitamos hacer dentro de nuestra unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-389">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="b7347-390">La carga diferida está *desactivada* de forma predeterminada cuando se trabaja directamente con un objeto ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="b7347-390">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="b7347-391">Hay una propiedad que se puede establecer en la propiedad ContextOptions para habilitar la carga aplazada y podemos establecer esta propiedad dentro de nuestra unidad real de trabajo si queremos habilitar la carga diferida en todas partes.</span><span class="sxs-lookup"><span data-stu-id="b7347-391">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="b7347-392">Con la carga diferida implícita habilitada, el código de aplicación puede usar un empleado y las tarjetas de tiempo asociadas del empleado, mientras que el resto de completamente no es consciente del trabajo necesario para que EF cargue los datos adicionales.</span><span class="sxs-lookup"><span data-stu-id="b7347-392">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="b7347-393">La carga diferida facilita la escritura del código de la aplicación y, con el proxy mágico, el código sigue siendo totalmente comprobable.</span><span class="sxs-lookup"><span data-stu-id="b7347-393">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="b7347-394">Las falsificaciones en memoria de la unidad de trabajo pueden simplemente precargar las entidades falsas con datos asociados cuando sea necesario durante una prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-394">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="b7347-395">Llegados a este punto, nos centraremos en la creación de repositorios con IObjectSet &lt; T &gt; y veremos las abstracciones para ocultar todos los signos del marco de persistencia.</span><span class="sxs-lookup"><span data-stu-id="b7347-395">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="b7347-396">Repositorios personalizados</span><span class="sxs-lookup"><span data-stu-id="b7347-396">Custom Repositories</span></span>

<span data-ttu-id="b7347-397">La primera vez que se presentó el patrón de diseño de unidad de trabajo en este artículo se proporciona código de ejemplo para el aspecto que podría tener la unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-397">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="b7347-398">Vamos a presentar esta idea original con el escenario de tarjeta de tiempo empleado y empleado con el que hemos trabajado.</span><span class="sxs-lookup"><span data-stu-id="b7347-398">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="b7347-399">La principal diferencia entre esta unidad de trabajo y la unidad de trabajo que creamos en la última sección es cómo esta unidad de trabajo no usa ninguna abstracción del marco de EF4 (no hay IObjectSet &lt; T &gt; ).</span><span class="sxs-lookup"><span data-stu-id="b7347-399">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="b7347-400">IObjectSet &lt; T &gt; funciona bien como una interfaz de repositorio, pero es posible que la API que expone no se alinee perfectamente con las necesidades de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7347-400">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="b7347-401">En este próximo enfoque, se representarán los repositorios con una &lt; abstracción de IRepository T personalizada &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-401">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="b7347-402">Muchos desarrolladores que siguen el diseño basado en pruebas, el diseño controlado por el comportamiento y las metodologías controladas por el dominio prefieren el &lt; enfoque IRepository T &gt; por varias razones.</span><span class="sxs-lookup"><span data-stu-id="b7347-402">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="b7347-403">En primer lugar, &lt; la &gt; interfaz IRepository T representa una capa "contra daños".</span><span class="sxs-lookup"><span data-stu-id="b7347-403">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="b7347-404">Tal como se describe en Eric Evans en su libro de diseño controlado por dominios, una capa contra daños mantiene el código de dominio fuera de las API de infraestructura, como una API de persistencia.</span><span class="sxs-lookup"><span data-stu-id="b7347-404">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="b7347-405">En segundo lugar, los desarrolladores pueden crear métodos en el repositorio que satisfagan las necesidades exactas de una aplicación (como se detectó al escribir pruebas).</span><span class="sxs-lookup"><span data-stu-id="b7347-405">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="b7347-406">Por ejemplo, con frecuencia es necesario buscar una sola entidad con un valor de identificador, por lo que podemos agregar un método FindById a la interfaz de repositorio.</span><span class="sxs-lookup"><span data-stu-id="b7347-406">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="b7347-407">Nuestra &lt; &gt; definición de IRepository T tendrá un aspecto similar al siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-407">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="b7347-408">Observe que volveremos a usar una interfaz IQueryable &lt; T &gt; para exponer las colecciones de entidades.</span><span class="sxs-lookup"><span data-stu-id="b7347-408">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="b7347-409">IQueryable &lt; T &gt; permite que los árboles de expresión LINQ fluyan al proveedor EF4 y proporcione al proveedor una vista holística de la consulta.</span><span class="sxs-lookup"><span data-stu-id="b7347-409">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="b7347-410">Una segunda opción sería devolver IEnumerable &lt; T &gt; , lo que significa que el proveedor EF4 LINQ solo verá las expresiones compiladas dentro del repositorio.</span><span class="sxs-lookup"><span data-stu-id="b7347-410">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="b7347-411">Cualquier agrupación, ordenación y proyección realizadas fuera del repositorio no se creará en el comando SQL enviado a la base de datos, lo que puede afectar negativamente al rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b7347-411">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="b7347-412">Por otro lado, un repositorio que devuelva solo &lt; los resultados de IEnumerable T &gt; nunca le sorprenderá con un nuevo comando SQL.</span><span class="sxs-lookup"><span data-stu-id="b7347-412">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="b7347-413">Ambos enfoques funcionarán y ambos enfoques seguirán siendo comprobables.</span><span class="sxs-lookup"><span data-stu-id="b7347-413">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="b7347-414">Es sencillo proporcionar una única implementación de la &lt; interfaz IRepository T &gt; mediante genéricos y la API de OBJECTCONTEXT de EF4.</span><span class="sxs-lookup"><span data-stu-id="b7347-414">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="b7347-415">El &lt; enfoque IRepository T &gt; nos proporciona un control adicional sobre nuestras consultas porque un cliente tiene que invocar un método para llegar a una entidad.</span><span class="sxs-lookup"><span data-stu-id="b7347-415">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="b7347-416">Dentro del método, podríamos proporcionar comprobaciones adicionales y operadores LINQ para aplicar las restricciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7347-416">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="b7347-417">Observe que la interfaz tiene dos restricciones en el parámetro de tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="b7347-417">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="b7347-418">La primera restricción es la clase cons que requiere el ObjectSet &lt; T &gt; y la segunda restricción obliga a nuestras entidades a implementar IEntity: una abstracción creada para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7347-418">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="b7347-419">La interfaz IEntity fuerza a las entidades a tener una propiedad de identificador legible y, a continuación, podemos usar esta propiedad en el método FindById.</span><span class="sxs-lookup"><span data-stu-id="b7347-419">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="b7347-420">IEntity se define con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-420">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="b7347-421">IEntity podría considerarse una pequeña infracción de la persistencia omisión, ya que las entidades necesitan implementar esta interfaz.</span><span class="sxs-lookup"><span data-stu-id="b7347-421">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="b7347-422">Recuerde que la persistencia omisión se refiere a las ventajas y, para muchas de las funciones de FindById, la restricción impuesta por la interfaz.</span><span class="sxs-lookup"><span data-stu-id="b7347-422">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="b7347-423">La interfaz no tiene ningún impacto en la capacidad de prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-423">The interface has no impact on testability.</span></span>

<span data-ttu-id="b7347-424">La creación de una instancia &lt; de Live IRepository T &gt; requiere un OBJECTCONTEXT de EF4, por lo que una implementación concreta de la unidad de trabajo debe administrar la creación de instancias.</span><span class="sxs-lookup"><span data-stu-id="b7347-424">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="b7347-425">Uso del repositorio personalizado</span><span class="sxs-lookup"><span data-stu-id="b7347-425">Using the Custom Repository</span></span>

<span data-ttu-id="b7347-426">El uso de nuestro repositorio personalizado no es significativamente diferente del uso del repositorio basado en IObjectSet &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-426">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="b7347-427">En lugar de aplicar operadores LINQ directamente a una propiedad, primero debemos invocar los métodos del repositorio para obtener una &lt; referencia IQueryable T &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-427">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="b7347-428">Observe que el operador de inclusión personalizado implementado anteriormente funcionará sin cambios.</span><span class="sxs-lookup"><span data-stu-id="b7347-428">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="b7347-429">El método FindById del repositorio quita la lógica duplicada de acciones que intentan recuperar una sola entidad.</span><span class="sxs-lookup"><span data-stu-id="b7347-429">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="b7347-430">No hay ninguna diferencia significativa en la capacidad de prueba de los dos métodos que hemos examinado.</span><span class="sxs-lookup"><span data-stu-id="b7347-430">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="b7347-431">Podríamos proporcionar implementaciones falsas de IRepository &lt; T &gt; mediante la creación de clases concretas respaldadas por &lt; &gt; un empleado de HashSet, exactamente igual que en la última sección.</span><span class="sxs-lookup"><span data-stu-id="b7347-431">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="b7347-432">Sin embargo, algunos desarrolladores prefieren usar objetos ficticios y marcos de objetos ficticios en lugar de crear simulaciones.</span><span class="sxs-lookup"><span data-stu-id="b7347-432">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="b7347-433">Veremos el uso de simulacros para probar nuestra implementación y analizar las diferencias entre los simulacros y las simulaciones en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-433">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="b7347-434">Pruebas con simulacros</span><span class="sxs-lookup"><span data-stu-id="b7347-434">Testing with Mocks</span></span>

<span data-ttu-id="b7347-435">Hay diferentes enfoques para crear lo que Martin Fowler llama "Double de prueba".</span><span class="sxs-lookup"><span data-stu-id="b7347-435">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="b7347-436">Una prueba Double (como una película Stunt Double) es un objeto que se compila en "out" para objetos reales de producción durante las pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7347-436">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="b7347-437">Los repositorios en memoria creados son dobles de pruebas para los repositorios que se comunican con SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b7347-437">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="b7347-438">Hemos visto cómo usar estas pruebas dobles durante las pruebas unitarias para aislar el código y mantener la ejecución rápida de las pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7347-438">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="b7347-439">Los dobles de pruebas que hemos creado son implementaciones reales y en funcionamiento.</span><span class="sxs-lookup"><span data-stu-id="b7347-439">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="b7347-440">En segundo plano, cada uno almacena una colección concreta de objetos y agrega y quita objetos de esta colección mientras se manipula el repositorio durante una prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-440">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="b7347-441">Algunos desarrolladores como para compilar la prueba se duplican de esta manera: con código real y implementaciones de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b7347-441">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="b7347-442">Estos dobles de pruebas son lo que llamamos *imitaciones*.</span><span class="sxs-lookup"><span data-stu-id="b7347-442">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="b7347-443">Tienen implementaciones en funcionamiento, pero no son lo suficientemente reales para su uso en producción.</span><span class="sxs-lookup"><span data-stu-id="b7347-443">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="b7347-444">El repositorio falso no escribe realmente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-444">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="b7347-445">El servidor SMTP falso no envía un mensaje de correo electrónico a través de la red.</span><span class="sxs-lookup"><span data-stu-id="b7347-445">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="b7347-446">Simuladores frente a falsificaciones</span><span class="sxs-lookup"><span data-stu-id="b7347-446">Mocks versus Fakes</span></span>

<span data-ttu-id="b7347-447">Hay otro tipo de prueba doble conocido como *ficticio*.</span><span class="sxs-lookup"><span data-stu-id="b7347-447">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="b7347-448">Mientras que las simulaciones tienen implementaciones en funcionamiento, los simulacros se incluyen sin implementación.</span><span class="sxs-lookup"><span data-stu-id="b7347-448">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="b7347-449">Con la ayuda de un marco de objeto ficticio, construimos estos objetos ficticios en tiempo de ejecución y los usamos como dobles de pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7347-449">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="b7347-450">En esta sección vamos a usar el marco de trabajo ficticio de código abierto MOQ.</span><span class="sxs-lookup"><span data-stu-id="b7347-450">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="b7347-451">Este es un ejemplo sencillo del uso de MOQ para crear dinámicamente una prueba Double para un repositorio de empleados.</span><span class="sxs-lookup"><span data-stu-id="b7347-451">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="b7347-452">Solicitamos MOQ para una implementación de empleados de IRepository &lt; &gt; y creamos una dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="b7347-452">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="b7347-453">Podemos obtener el objeto que implementa IRepository &lt; Employee &gt; mediante el acceso a la propiedad Object del &lt; objeto ficticio T &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-453">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="b7347-454">Es este objeto interno que se puede pasar a nuestros controladores y no sabrá si se trata de un doble de prueba o del repositorio real.</span><span class="sxs-lookup"><span data-stu-id="b7347-454">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="b7347-455">Se pueden invocar métodos en el objeto del mismo modo que se invocan los métodos en un objeto con una implementación real.</span><span class="sxs-lookup"><span data-stu-id="b7347-455">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="b7347-456">Debe preguntarse lo que hará el repositorio ficticio cuando se invoque el método Add.</span><span class="sxs-lookup"><span data-stu-id="b7347-456">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="b7347-457">Dado que no hay ninguna implementación detrás del objeto ficticio, Add no hace nada.</span><span class="sxs-lookup"><span data-stu-id="b7347-457">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="b7347-458">No hay ninguna colección concreta en segundo plano como teníamos con las falsificaciones que escribimos, por lo que el empleado se descarta.</span><span class="sxs-lookup"><span data-stu-id="b7347-458">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="b7347-459">¿Qué ocurre con el valor devuelto de FindById?</span><span class="sxs-lookup"><span data-stu-id="b7347-459">What about the return value of FindById?</span></span> <span data-ttu-id="b7347-460">En este caso, el objeto ficticio hace lo único que puede hacer, que devuelve un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b7347-460">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="b7347-461">Dado que se devuelve un tipo de referencia (un empleado), el valor devuelto es un valor null.</span><span class="sxs-lookup"><span data-stu-id="b7347-461">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="b7347-462">Los simulacros podrían parecer no útil; sin embargo, hay dos características más de los simulacros que no hemos hablado.</span><span class="sxs-lookup"><span data-stu-id="b7347-462">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="b7347-463">En primer lugar, el marco MOQ registra todas las llamadas realizadas en el objeto ficticio.</span><span class="sxs-lookup"><span data-stu-id="b7347-463">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="b7347-464">Más adelante en el código, se puede preguntar a MOQ si alguien invocó el método Add o si alguien invocó el método FindById.</span><span class="sxs-lookup"><span data-stu-id="b7347-464">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="b7347-465">Más adelante veremos cómo podemos usar esta característica de grabación de "caja negra" en las pruebas.</span><span class="sxs-lookup"><span data-stu-id="b7347-465">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="b7347-466">La segunda característica excelente es cómo se puede usar MOQ para programar un objeto ficticio con las *expectativas*.</span><span class="sxs-lookup"><span data-stu-id="b7347-466">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="b7347-467">Una expectativa indica al objeto ficticio cómo responder a cualquier interacción determinada.</span><span class="sxs-lookup"><span data-stu-id="b7347-467">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="b7347-468">Por ejemplo, se puede programar una expectativa en nuestro simulacro e indicar a que devuelva un objeto de empleado cuando alguien invoque FindById.</span><span class="sxs-lookup"><span data-stu-id="b7347-468">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="b7347-469">El marco de trabajo de MOQ usa una API de instalación y expresiones lambda para programar estas expectativas.</span><span class="sxs-lookup"><span data-stu-id="b7347-469">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="b7347-470">En este ejemplo, se pide a MOQ que cree de forma dinámica un repositorio y, a continuación, programemos el repositorio con una expectativa.</span><span class="sxs-lookup"><span data-stu-id="b7347-470">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="b7347-471">La expectativa indica al objeto ficticio que devuelva un nuevo objeto de empleado con un valor de identificador de 5 cuando alguien invoque el método FindById pasando un valor de 5.</span><span class="sxs-lookup"><span data-stu-id="b7347-471">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="b7347-472">Esta prueba se supera y no es necesario crear una implementación completa para falsificar IRepository &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-472">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="b7347-473">Vamos a revisar las pruebas que hemos escrito anteriormente y a trabajar con ellas para usar simulacros en lugar de simulaciones.</span><span class="sxs-lookup"><span data-stu-id="b7347-473">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="b7347-474">Al igual que antes, usaremos una clase base para configurar los componentes comunes de la infraestructura que necesitamos para todas las pruebas del controlador.</span><span class="sxs-lookup"><span data-stu-id="b7347-474">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="b7347-475">El código de instalación se mantiene principalmente igual.</span><span class="sxs-lookup"><span data-stu-id="b7347-475">The setup code remains mostly the same.</span></span> <span data-ttu-id="b7347-476">En lugar de usar falsificaciones, usaremos MOQ para construir objetos ficticios.</span><span class="sxs-lookup"><span data-stu-id="b7347-476">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="b7347-477">La clase base organiza la unidad de trabajo simulada para devolver un repositorio ficticio cuando el código invoca la propiedad Employees.</span><span class="sxs-lookup"><span data-stu-id="b7347-477">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="b7347-478">El resto de la configuración del simulacro tendrá lugar dentro de los extras de prueba dedicados a cada escenario específico.</span><span class="sxs-lookup"><span data-stu-id="b7347-478">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="b7347-479">Por ejemplo, el accesorio de prueba para la acción de índice configurará el repositorio ficticio para devolver una lista de empleados cuando la acción invoca el método FindAll del repositorio ficticio.</span><span class="sxs-lookup"><span data-stu-id="b7347-479">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="b7347-480">A excepción de las expectativas, nuestras pruebas son similares a las pruebas que teníamos antes.</span><span class="sxs-lookup"><span data-stu-id="b7347-480">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="b7347-481">Sin embargo, con la capacidad de grabación de un marco ficticio, podemos enfocar las pruebas desde un ángulo diferente.</span><span class="sxs-lookup"><span data-stu-id="b7347-481">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="b7347-482">Veremos esta nueva perspectiva en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="b7347-482">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="b7347-483">Pruebas de estado frente a interacción</span><span class="sxs-lookup"><span data-stu-id="b7347-483">State versus Interaction Testing</span></span>

<span data-ttu-id="b7347-484">Hay distintas técnicas que puede usar para probar el software con objetos ficticios.</span><span class="sxs-lookup"><span data-stu-id="b7347-484">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="b7347-485">Un enfoque consiste en usar pruebas basadas en el estado, que es lo que hemos hecho en este documento hasta ahora.</span><span class="sxs-lookup"><span data-stu-id="b7347-485">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="b7347-486">Las pruebas basadas en el estado realizan aserciones sobre el estado del software.</span><span class="sxs-lookup"><span data-stu-id="b7347-486">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="b7347-487">En la última prueba, se ha invocado un método de acción en el controlador y se ha realizado una aserción sobre el modelo que debe compilar.</span><span class="sxs-lookup"><span data-stu-id="b7347-487">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="b7347-488">Estos son algunos ejemplos de estado de prueba:</span><span class="sxs-lookup"><span data-stu-id="b7347-488">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="b7347-489">Compruebe que el repositorio contiene el nuevo objeto de empleado después de que se ejecute Create.</span><span class="sxs-lookup"><span data-stu-id="b7347-489">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="b7347-490">Compruebe que el modelo contiene una lista de todos los empleados después de que se ejecute el índice.</span><span class="sxs-lookup"><span data-stu-id="b7347-490">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="b7347-491">Compruebe que el repositorio no contiene un empleado determinado después de que se ejecute la eliminación.</span><span class="sxs-lookup"><span data-stu-id="b7347-491">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="b7347-492">Otro enfoque que verá con objetos ficticios es comprobar las *interacciones*.</span><span class="sxs-lookup"><span data-stu-id="b7347-492">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="b7347-493">Mientras que las pruebas basadas en el estado realizan aserciones sobre el estado de los objetos, las pruebas basadas en la interacción realizan aserciones sobre cómo interactúan los objetos.</span><span class="sxs-lookup"><span data-stu-id="b7347-493">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="b7347-494">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b7347-494">For example:</span></span>

-   <span data-ttu-id="b7347-495">Compruebe que el controlador invoca el método Add del repositorio cuando se ejecuta Create.</span><span class="sxs-lookup"><span data-stu-id="b7347-495">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="b7347-496">Compruebe que el controlador invoca el método FindAll del repositorio cuando se ejecuta el índice.</span><span class="sxs-lookup"><span data-stu-id="b7347-496">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="b7347-497">Compruebe que el controlador invoca el método commit de la unidad del trabajo para guardar los cambios cuando se ejecuta la edición.</span><span class="sxs-lookup"><span data-stu-id="b7347-497">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="b7347-498">A menudo, las pruebas de interacción requieren menos datos de prueba, porque no se Poking dentro de las colecciones y se comprueban los recuentos.</span><span class="sxs-lookup"><span data-stu-id="b7347-498">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="b7347-499">Por ejemplo, si sabemos que la acción de detalles invoca el método FindById de un repositorio con el valor correcto, es probable que la acción se comporte correctamente.</span><span class="sxs-lookup"><span data-stu-id="b7347-499">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="b7347-500">Podemos comprobar este comportamiento sin configurar ningún dato de prueba para que se devuelva desde FindById.</span><span class="sxs-lookup"><span data-stu-id="b7347-500">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="b7347-501">La única configuración necesaria en el accesorio de prueba anterior es la que proporciona la clase base.</span><span class="sxs-lookup"><span data-stu-id="b7347-501">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="b7347-502">Cuando se invoca la acción del controlador, MOQ registrará las interacciones con el repositorio ficticio.</span><span class="sxs-lookup"><span data-stu-id="b7347-502">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="b7347-503">Mediante la comprobación de la API de MOQ, podemos preguntar a MOQ si el controlador invocó FindById con el valor de ID. adecuado.</span><span class="sxs-lookup"><span data-stu-id="b7347-503">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="b7347-504">Si el controlador no invocó el método o invocó el método con un valor de parámetro inesperado, el método verify producirá una excepción y se producirá un error en la prueba.</span><span class="sxs-lookup"><span data-stu-id="b7347-504">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="b7347-505">Este es otro ejemplo para comprobar que la acción de creación invoca la confirmación en la unidad de trabajo actual.</span><span class="sxs-lookup"><span data-stu-id="b7347-505">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="b7347-506">Un peligro con las pruebas de interacción es la tendencia de especificar las interacciones.</span><span class="sxs-lookup"><span data-stu-id="b7347-506">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="b7347-507">La capacidad del objeto ficticio de registrar y comprobar cada interacción con el objeto ficticio no significa que la prueba debe intentar comprobar cada interacción.</span><span class="sxs-lookup"><span data-stu-id="b7347-507">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="b7347-508">Algunas interacciones son detalles de la implementación y solo se deben comprobar las interacciones *necesarias* para satisfacer la prueba actual.</span><span class="sxs-lookup"><span data-stu-id="b7347-508">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="b7347-509">La elección entre simulacros o falsificaciones depende en gran medida del sistema que se está probando y de sus preferencias personales (o de equipo).</span><span class="sxs-lookup"><span data-stu-id="b7347-509">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="b7347-510">Los objetos ficticios pueden reducir drásticamente la cantidad de código que necesita para implementar los dobles de pruebas, pero no todos se sienten cómodos con la programación de las expectativas y la comprobación de las interacciones.</span><span class="sxs-lookup"><span data-stu-id="b7347-510">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="b7347-511">Conclusiones</span><span class="sxs-lookup"><span data-stu-id="b7347-511">Conclusions</span></span>

<span data-ttu-id="b7347-512">En este documento hemos mostrado varios enfoques para crear código comprobable al usar el Entity Framework ADO.NET para la persistencia de datos.</span><span class="sxs-lookup"><span data-stu-id="b7347-512">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="b7347-513">Podemos aprovechar las abstracciones integradas como IObjectSet &lt; t &gt; o crear nuestras propias abstracciones como IRepository &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7347-513">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="b7347-514">En ambos casos, la compatibilidad POCO en la Entity Framework 4,0 de ADO.NET permite a los consumidores de estas abstracciones seguir siendo ignorables y muy comprobables.</span><span class="sxs-lookup"><span data-stu-id="b7347-514">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="b7347-515">Características adicionales de EF4 como la carga diferida implícita permite que el código del servicio de aplicaciones y del negocio funcione sin preocuparse por los detalles de un almacén de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="b7347-515">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="b7347-516">Por último, las abstracciones que creamos son fáciles de simular o falsificar dentro de las pruebas unitarias, y podemos usar estos dobles de pruebas para lograr pruebas de ejecución rápida, muy aisladas y confiables.</span><span class="sxs-lookup"><span data-stu-id="b7347-516">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="b7347-517">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b7347-517">Additional Resources</span></span>

-   <span data-ttu-id="b7347-518">Robert C. Martin, " [el principio de responsabilidad única](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="b7347-518">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="b7347-519">Martin Fowler, [Catálogo de patrones](https://www.martinfowler.com/eaaCatalog/index.html) de *patrones de arquitectura de aplicaciones empresariales*</span><span class="sxs-lookup"><span data-stu-id="b7347-519">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="b7347-520">Griffin Caprio, " [inyección de dependencia](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="b7347-520">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="b7347-521">Blog de programación de datos, " [Tutorial: desarrollo controlado por pruebas con el Entity Framework 4,0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".</span><span class="sxs-lookup"><span data-stu-id="b7347-521">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="b7347-522">Blog de programación de datos, " [uso de patrones de repositorio y unidad de trabajo con Entity Framework 4,0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="b7347-522">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="b7347-523">Aaron Jensen, " [Introducción](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)a las especificaciones de la máquina"</span><span class="sxs-lookup"><span data-stu-id="b7347-523">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="b7347-524">Eric Lee, " [BDD con MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"</span><span class="sxs-lookup"><span data-stu-id="b7347-524">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="b7347-525">Eric Evans, " [diseño controlado por dominios](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="b7347-525">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="b7347-526">Martin Fowler, "los [simulacros no son stubs](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="b7347-526">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="b7347-527">Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="b7347-527">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="b7347-528">Moq</span><span class="sxs-lookup"><span data-stu-id="b7347-528">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="b7347-529">Biografía</span><span class="sxs-lookup"><span data-stu-id="b7347-529">Biography</span></span>

<span data-ttu-id="b7347-530">Scott Allen es miembro del personal técnico de Pluralsight y el fundador de OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="b7347-530">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="b7347-531">En 15 años de desarrollo de software comercial, Scott ha trabajado en soluciones para todo, desde dispositivos incrustados de 8 bits hasta aplicaciones Web ASP.NET altamente escalables.</span><span class="sxs-lookup"><span data-stu-id="b7347-531">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="b7347-532">Puede ponerse en contacto con Scott en su blog en OdeToCode o en Twitter en [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) .</span><span class="sxs-lookup"><span data-stu-id="b7347-532">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
